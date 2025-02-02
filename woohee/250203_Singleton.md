# Singleton 디자인 패턴

## 1. 싱글톤 등장 배경

### 역사적 배경

- 1970년대 컴퓨터 자원의 제한적 환경
- Gang of Four (GoF) 디자인 패턴의 일부로 공식화
- 객체 지향 프로그래밍의 발전과 함께 진화

### 해결하고자 했던 문제들

- 자원 관리의 비효율성
  - 동일 기능의 객체가 중복 생성되는 문제
  - 메모리 낭비 문제
  - 공유 자원 접근 충돌 문제
- 전역 상태 관리의 어려움
  - 여러 곳에서 상태가 불일치하는 문제
  - 설정값 동기화 문제

## 2. 싱글톤 개념 설명

### 핵심 개념

- 단일 인스턴스 보장
  - 클래스의 인스턴스가 오직 하나만 존재
  - 전역적인 접근점 제공
- 주요 구현 요소
  - private 생성자
  - private static 인스턴스
  - public static 접근 메서드

### 동작 방식

- 지연 초기화 (Lazy Initialization)
- Thread Safety 고려사항
- 메모리 관리 측면

## 3. 싱글톤 예시 (TS)

### 기본 구현

```typescript
class BasicSingleton {
  private static instance: BasicSingleton;
  private constructor() {}

  public static getInstance(): BasicSingleton {
    if (!BasicSingleton.instance) {
      BasicSingleton.instance = new BasicSingleton();
    }
    return BasicSingleton.instance;
  }
}
```

### Thread-Safe 버전

```typescript
class ThreadSafeSingleton {
  private static instance: ThreadSafeSingleton;
  private static creating: boolean = false;

  private constructor() {
    if (ThreadSafeSingleton.creating === false) {
      throw new Error('You cannot call new() on a Singleton.');
    }
  }

  public static getInstance(): ThreadSafeSingleton {
    if (!ThreadSafeSingleton.instance) {
      ThreadSafeSingleton.creating = true;
      ThreadSafeSingleton.instance = new ThreadSafeSingleton();
      ThreadSafeSingleton.creating = false;
    }
    return ThreadSafeSingleton.instance;
  }
}
```

### 모던 타입스크립트 싱글톤

```typescript
class ModernSingleton {
  private static instance: ModernSingleton;
  private data: Map<string, any> = new Map();

  private constructor() {}

  public static getInstance(): ModernSingleton {
    if (!ModernSingleton.instance) {
      ModernSingleton.instance = new ModernSingleton();
    }
    return ModernSingleton.instance;
  }

  public setData(key: string, value: any): void {
    this.data.set(key, value);
  }

  public getData(key: string): any {
    return this.data.get(key);
  }

  public clearData(): void {
    this.data.clear();
  }
}
```

## 4. 사용되는 곳

### 4-1. 주로 사용되는 곳

- 설정(Configuration) 관리
- 로깅 시스템

### 4-2. 프론트엔드에서 사용되는 곳

- 상태 관리
- 웹 스토리지 관리
- API 클라이언트

## 5. DI와의 비교

### 싱글톤 방식

```typescript
class UserService {
  private api = APIClient.getInstance();

  async getUser(id: string) {
    return this.api.get(`/users/${id}`);
  }
}
```

### DI 방식

```typescript
interface APIClientInterface {
  get(endpoint: string): Promise<any>;
}

class UserService {
  constructor(private api: APIClientInterface) {}

  async getUser(id: string) {
    return this.api.get(`/users/${id}`);
  }
}
```

### 비교 분석

- 싱글톤
  - 장점: 구현 간단, 전역 상태 관리 용이
  - 단점: 테스트 어려움, 코드 결합도 높음
- DI
  - 장점: 테스트 용이, 느슨한 결합, 유연성
  - 단점: 초기 설정 복잡, 보일러플레이트 코드 증가
