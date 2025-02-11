# Singleton

![singleton](./singleton.svg.png)

## Singleton이란?
- `객체지향 프로그래밍(Object-oriented programming)`에서 한 개의 클래스를 단일 객체로 제한하는 디자인 패턴

### 장점
- 단일 객체를 사용하기에 공유 리소스의 문제가 발생하지 않으며 매번 같은 객체를 참조하기에 메모리를 절약 가능
- 해당 인스턴스에 대한 전역 접근 지점을 제공
- 객체가 리소스를 많이 차지하는 역할을 하는 무거운 클래스일때 적합
  - ex) 데이터베이스 연결 모듈, 디스크 연결, 네트워크 통신, DBCP 커넥션풀, 스레드풀, 캐시, 로그 기록 객체 등

### 단점
- S.O.L.I.D 원칙에 위배되는 사례가 다수 존재
  - 인스턴스 자체가 하나만 생성하기 때문에 여러가지 책임을 지니게 되는 경우가 많아 단일 책임 원칙(SRP)를 위반
  - 싱글톤 인스턴스가 혼자 너무 많은 일을 하거나, 많은 데이터를 공유시키면 다른 클래스들 간의 결합도가 높아지게 되어 개방-폐쇄 원칙(OCP)에도 위배 가능성 존재
  - 경우에 따라 의존 관계상 클라이언트가 인터페이스와 같은 추상화가 아닌, 구체 클래스에 의존하게 되어 의존 역전 원칙(DIP)도 위반
- 테스트 난이도 상승
  - 많은 테스트 프레임워크들에서 Mock 객체에 의존 하지만 싱글턴 클래스의 생성자는 비공개이고 대부분 언어에서 정적 메서드를 오버라이딩하는 것이 불가능하기에 테스트의 난이도가 상승
- 잘못된 디자인​을 가리기가 가능


## 구현
```go
package main

import (
	"fmt"
	"sync"
)

var instance Singleton
var defaultMutex sync.Mutex

type Singleton interface {
	GetAddress() string
}

type singleton struct{}

func (s *singleton) GetAddress() string {
	return fmt.Sprintf("%p", s)
}

func SingletonInstance() Singleton {
	if instance != nil {
		return instance
	}

	defaultMutex.Lock()
	defer defaultMutex.Unlock()

	if instance == nil {
		instance = &singleton{}
		return instance
	}

	return instance
}

func main() {
	ins := SingletonInstance()
	fmt.Println(ins.GetAddress()) // 0x25cf360
	ins = SingletonInstance()
	fmt.Println(ins.GetAddress()) // 0x25cf360

	fmt.Println(fmt.Sprintf("%p", instance)) // 0x25cf360

	fmt.Println(ins == instance) // true
}

```

## Reference
- https://refactoring.guru/ko/design-patterns/singleton
- https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%8B%B1%EA%B8%80%ED%86%A4Singleton-%ED%8C%A8%ED%84%B4-%EA%BC%BC%EA%BC%BC%ED%95%98%EA%B2%8C-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90
- https://puppyloper.medium.com/go%EA%B3%BC-singleton-ce29c3eff449


