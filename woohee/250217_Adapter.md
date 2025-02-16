# 어댑터 패턴 (Adapter Pattern)

어댑터 패턴(Adapter Pattern)은 서로 다른 인터페이스를 가진 클래스들이 함께 작동할 수 있도록 중간 역할을 하는 패턴이다. 이 패턴을 사용하면 기존 코드를 변경하지 않고도 다른 시스템이나 라이브러리와 호환성을 유지할 수 있다.

## 1. 어댑터 패턴 개요

### 정의

어댑터 패턴은 **인터페이스가 서로 다른 클래스들을 연결하는 역할**을 한다. 즉, 기존 코드와 새로운 코드 사이에서 변환기를 제공하여 **클래스나 객체 간의 호환성을 보장**한다.

### 특징

- 기존 코드를 수정하지 않고 새로운 기능을 추가할 수 있다.
- 인터페이스가 서로 다른 두 개의 클래스 또는 시스템을 연결할 때 유용하다.
- 클라이언트 코드가 기존 코드와 동일한 방식으로 새로운 시스템을 사용할 수 있도록 만든다.

---

## 2. 어댑터 패턴의 구조

### UML 다이어그램

```
┌───────────────┐       ┌───────────────┐       ┌────────────────┐
│  Client       │ --->  │  Adapter      │ --->  │  Adaptee       │
│ (클라이언트)     │       │ (어댑터)        │       │ (적응 대상)      │
└───────────────┘       └───────────────┘       └────────────────┘
```

### 구성 요소

- **클라이언트(Client)**: 어댑터를 통해 원하는 기능을 사용하는 객체
- **어댑터(Adapter)**: 클라이언트가 기대하는 인터페이스를 제공하며, 내부적으로 `Adaptee`를 호출하여 변환 역할을 수행
- **적응 대상(Adaptee)**: 기존에 존재하는 클래스지만 클라이언트가 요구하는 인터페이스를 직접 구현하지 않은 객체

---

## 3. 어댑터 패턴 구현 예시

### 예제 1: 클래스 어댑터 패턴 (Class Adapter Pattern)

```typescript
// 기존 서비스 (Adaptee)
class OldSystem {
  getSpecificData(): string {
    return 'Old System Data';
  }
}

// 클라이언트가 기대하는 인터페이스
interface NewSystem {
  getData(): string;
}

// 어댑터 클래스 (OldSystem을 NewSystem에 맞게 변환)
class Adapter extends OldSystem implements NewSystem {
  getData(): string {
    return this.getSpecificData();
  }
}

// 사용 예시
const adaptedSystem: NewSystem = new Adapter();
console.log(adaptedSystem.getData()); // "Old System Data"
```

### 예제 2: 객체 어댑터 패턴 (Object Adapter Pattern)

```typescript
// 기존 서비스 (Adaptee)
class OldSystem {
  getSpecificData(): string {
    return 'Old System Data';
  }
}

// 클라이언트가 기대하는 인터페이스
interface NewSystem {
  getData(): string;
}

// 어댑터 (객체를 포함하여 변환 역할 수행)
class Adapter implements NewSystem {
  private oldSystem: OldSystem;

  constructor(oldSystem: OldSystem) {
    this.oldSystem = oldSystem;
  }

  getData(): string {
    return this.oldSystem.getSpecificData();
  }
}

// 사용 예시
const oldSystemInstance = new OldSystem();
const adaptedSystem: NewSystem = new Adapter(oldSystemInstance);
console.log(adaptedSystem.getData()); // "Old System Data"
```

---

## 4. 어댑터 패턴의 장점과 단점

### ✅ 장점

1. **기존 코드 수정 없이 새로운 인터페이스를 추가**할 수 있다.
2. **재사용성이 증가**하며, 레거시 코드와의 호환성을 유지할 수 있다.
3. 다른 인터페이스를 가진 여러 시스템을 연결할 수 있어 **유연성이 증가**한다.

### ❌ 단점

1. 코드가 복잡해질 수 있으며, **어댑터 클래스가 많아지면 관리가 어려워질 수 있다**.
2. 성능이 중요한 시스템에서는 **추가적인 호출 계층이 성능 저하를 초래**할 수 있다.

---

## 5. 어댑터 패턴을 사용하는 경우

### 사용 사례

- **레거시 코드와 새로운 시스템을 연결할 때**
- **외부 라이브러리 또는 API의 인터페이스가 변경되었을 때**
- **서로 다른 인터페이스를 가진 클래스들을 통합해야 할 때**

### 실제 사례

1. **Java의 `InputStreamReader`**: 바이트 스트림을 문자 스트림으로 변환하는 어댑터 역할을 한다.
2. **데이터베이스 드라이버(JDBC)**: 서로 다른 DB 시스템(MySQL, PostgreSQL 등)에 대해 동일한 API를 제공하기 위해 어댑터를 사용한다.
3. **프론트엔드 API 통합**: 서로 다른 API 응답 구조를 일관된 형식으로 변환하여 클라이언트에서 쉽게 사용할 수 있도록 한다.

---

## 6. 결론

어댑터 패턴은 기존 코드를 변경하지 않으면서도 새로운 시스템과의 호환성을 유지하는 강력한 패턴이다. 특히, 레거시 코드와 새로운 코드 간의 인터페이스를 맞추는 데 유용하며, 코드의 유연성과 확장성을 높이는 데 기여한다.

어댑터 패턴을 적절히 활용하면, 코드의 재사용성을 높이고 유지보수성을 향상시킬 수 있다.
