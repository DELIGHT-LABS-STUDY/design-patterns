### 개념
- 클래스의 인스턴스가 애플리케이션 단에서 1개만 존재하도록 보장 및 접근 지점 제공

1.클래스에 인스턴스가 하나만 있도록 합니다.

2.해당 인스턴스에 대한 전역 접근 지점을 제공합니다.

### 장점
- 인스턴스를 한 번만 생성하여 메모리 절약
- 인스턴스 접근 제어 가능

### 단점
- 전역 상태로 인한 코드 결합도 증가
- 단위 테스트 어려움
- 멀티스레드 환경에서 동기화 필요

## Ex
Android Hilt Singleton 구현 방식
```declarative
private volatile ActivityComponentManager componentManager;

volatile: 메모리 가시성 보장 -> CPU 캐시가 아닌 메인 메모리에서 직접 값을 읽고 씀 -> 다른 스레드에서 값 변경 즉시 반영
```
```declarative
public final ActivityComponentManager componentManager() {
    if (componentManager == null) {
        synchronized (componentManagerLock) {
            if (componentManager == null) {
                componentManager = createComponentManager();
            }
        }
    }
    return componentManager;
}
```