SOLID

변경에 대한 유연성, 확장 가능성 등을 고려한 설계 원칙

S = Single Responsibility Principle 단일 책임 원칙
O = Open/Closed Principle 개방/폐쇄 원칙
L = Liskov Substitution Principle 리스코프 치환 원칙
I = Interface Segregation Principle 인터페이스 분리 원칙
D = Dependency Inversion Principle 의존 역전 원칙

## 1. Single Responsibility Principle 단일 책임 원칙

한 클래스는 하나의 책임만 가져야 함

따라서 클래스가 변경되어야 하는 이유는 오직 하나여야 함

ex. UI 로직과 비즈니스 로직 분리

## 2. Open/Closed Principle 개방/폐쇄 원칙

확장에 대해서는 열려 있어야 하지만 변경에 대해서는 닫혀 있어야 함

기존의 코드를 변경하지 않고 새로운 기능을 추가할 수 있어야 함

ex. plugin 패턴

## 3. Liskov Substitution Principle 리스코프 치환 원칙

서브 클래스는 언제나 자신의 베이스 클래스를 대체할 수 있어야 함

기본적인 요구사항이 유지되어야 함

ex.
면적을 구하는 getArea() 메서드는 면적을 반환해야 함
면적이라는 것의 정의가 변경되면 안 됨

```typescript
interface Shape {
  // 요구사항 - 해당 도형의 면적을 반환
  getArea(): number;
}

class Rectangle implements Shape {
  constructor(private width: number, private height: number) {}

  getArea(): number {
    return this.width * this.height; // 직사각형 면적 공식
  }
}

class Circle implements Shape {
  constructor(private radius: number) {}

  getArea(): number {
    return Math.PI * this.radius ** 2; // 원 면적 공식
  }
}

// Bad Example - LSP 위반
class WeirdShape implements Shape {
  getArea(): number {
    return -50; // 면적의 개념을 위반 (음수 면적은 없음)
  }
}

// Bad Example - LSP 위반
class PerimeterAsArea implements Shape {
  constructor(private side: number) {}

  getArea(): number {
    return this.side * 4; // 면적 대신 둘레를 반환
  }
}
```

## 4. Interface Segregation Principle 인터페이스 분리 원칙

클라이언트는 자신이 사용하지 않는 메서드에 의존 관계를 맺으면 안 됨

사용하지 않는 메서드의 변경에 영향을 받지 않아야 함

## 5. Dependency Inversion Principle 의존 역전 원칙

고수준 모듈은 저수준 모듈의 구현에 의존해서는 안 됨

추상화에 의존해야 함 - 구현은 언제든지 바뀔 수 있음

의존성 방향이 구체적인 것 -> 추상적인 것으로 역전되어야 함

ex.

```typescript
// BAD - MyModal 구현에 의존
const useDialog = () => {
  const modal = new MyModal();

  return {
    open: (msg: string) => modal.open(msg),
  };
};

class MyModal {
  open(msg: string) {
    // 모달 열기
  }
}

// GOOD - Modal 인터페이스에 의존
interface Modal {
  open(msg: string): void;
}

const useDialog = (modal: Modal) => {
  return {
    open: (msg: string) => modal.open(msg),
  };
};

class MyModal implements Modal {
  open(msg: string) {
    // 모달 열기
  }
}
```
