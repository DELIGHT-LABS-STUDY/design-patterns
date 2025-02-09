
### Single Responsibility Principle - 단일 책임
```declarative
1.응집도-관련되어 있는지, 결합도 -> 변경에 따라 연쇄작용 x
result -> 재사용성,유지보수성,확장성
// 정보저장 및 ui보여주는 로직으르 위반
class UserManager {
    fun saveUser(user: User)
    fun showUserInterface()
}
    
refactoring ->
    
class UserRepository {
    fun saveUser(user: User)
}

class UserView {
    fun showUserInterface()
}

```
### Open/Closed Principle 개방,폐쇄 원칙
1. 기존 구성요소의 변경 x -> 확장 및 재사용 용이
2. 추상화, 다형성
result -> 유지보수성 확장성 재사용성
```declarative
class TxSender {
    fun sendTx(type: String, message: String) {
        when (type) {
            "xrpl" -> // xrpl send
            "trn" ->  // trn send
        }
    }
}

// 좋은 예
interface TxSender {
    fun sendMessage(message: String)
}

class XrplSender : TxSender {
    override fun sendTx(message: String) {
       
    }
}

class TxSender : TxSender {
    override fun sendTx(message: String) {
        
    }
}
```
### Liskov Substitution Principle - 하위 클래스는 상위 클래스를 대체
1.type, 행동까지 호환 가능
result -> 코드 예측, 다형성, 재사용성, 테스트 용이
```declarative
open class Rectangle {
    open fun setWidth(width: Int)
    open fun setHeight(height: Int)
    fun getArea(): Int
}

// 잘못된 예
class Square : Rectangle() {
    override fun setWidth(width: Int) {
        super.setWidth(width)
        super.setHeight(width) 
    }
}

// 좋은 예
interface Shape {
    fun getArea(): Int
}

class Rectangle(val width: Int, val height: Int) : Shape {
    override fun getArea() = width * height
}

class Square(val side: Int) : Shape {
    override fun getArea() = side * side
}
```

### Interface Segregation Principle - 자신이 사용하지 않는 메서드에 의존 x
1. 인터페이스를 작은 단위로 분리 -> 필요한 인터페이스만 사용 -> 필요없는 메소드에 의존 x
result -> 코드 예측, 다형성, 재사용성, 테스트 용이
2. 
### Dependency Inversion Principle -고수준 모듈은 저수준 모듈에 의존x
1. ex) interface 구현체 -> 인터페이스
result -> 코드 예측, 다형성, 재사용성, 테스트 용이