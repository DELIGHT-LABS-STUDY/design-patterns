### 상태 패턴
- 객체 내부 상태가 변경 될 때 해당 객체가 그의 행동을 변경할 수 있도록 하는 행동 디자인 패턴
- 객체가 행동을 변경할 때 클래스가 변경한 것처럼 보일 수 있다.

#### 특징
- 상태들의 수는 유한
- 다른 상태로 즉시 전활 될수도 특정 상태에는 그렇지 않을 수도 있는 '천이' 라는 전환 규칙 존재
- 전략 패턴과 다르게 상태를 인식
### 적용
- 모든 상태를 예측하기 어려움
- 상태에 따라 다르게 행동하는 객체가 있고 상태의 수가 많을 때 상태별 코드가 자주 변경될때 용이
- 상태 인터페이스 선언 파생 클래스를 생성

### 장점
- ocp , 단일 책임 원칙 준수
- 콘텍스트 코드 단순화
### 단점
- 오버 프로그래밍 - 상태가 없거나 거의 변경이 없음

```
sealed class UiState<out R> {
    data class Success<out T>(val data: T) : UiState<T>()
    data object Loading : UiState<Nothing>()
    data class Error(val throwable: Throwable) : UiState<Nothing>()
}

fun <T> Flow<T>.asUiState(): Flow<UiState<T>> {
    return this
        .map<T, UiState<T>> { UiState.Success(it) }
        .onStart { emit(UiState.Loading) }
        .catch {
            Timber.e(it)
            emit(UiState.Error(it))
        }
}
```
- 상태 전환 로직 캡슐화 -> 사용하는 곳 내부구현 모름,상태전환 순서를 보장,모든 상태전환이 내부에서 발생
- 각 상태 명확히 구분
- 상태 전환 순서가 있음
- 상태가 각 고유의 동작
- 상태 변화 예측