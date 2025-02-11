### 어댑터 패턴
- 어댑터 역할은 특정 기능의 규격이 각각 다를 때, 어댑터를 사용하여 해결함(ex, 나라별 플러그인 규격, 결제 시스템 규격.. )
- 서로 관계가 없는 클래스들이 하나의 리스트에 담길 수 있다.
    - 특정 메서드를 통해 일률적으로 실행 가능
- 서로 호환되지 않는 규격의 클래스들이, 각각의 코드를 수정하지 않고도 한 곳에서 같은 방식으로 사용이 가능해짐.
- swift 사용 예시

```swift
// MARK: - 각 결제 시스템별 고유한 인터페이스 (Adaptee Interface)
protocol NaverPayInterface {
    func pay(_ amount: Double)
}

protocol KakaoPayInterface {
    func processTransaction(_ amount: Double)
}

// MARK: - 실제 결제 시스템 구현 (Concrete Adaptee)
class NaverPay: NaverPayInterface {
    func pay(_ amount: Double) {
        print("네이버페이 결제 처리: \(amount)원")
    }
}

class KakaoPay: KakaoPayInterface {
    func processTransaction(_ amount: Double) {
        print("카카오페이 결제 처리: \(amount)원")
    }
}

// MARK: - 통합 결제 인터페이스 (Target Interface)
protocol PaymentService {
    func processPayment(amount: Double)
}

// MARK: - 어댑터 (Adapter)
class NaverPayAdapter: PaymentService {
    private let naverPay: NaverPayInterface
    
    init(naverPay: NaverPayInterface = NaverPay()) {
        self.naverPay = naverPay
    }
    
    func processPayment(amount: Double) {
        naverPay.pay(amount)
    }
}

class KakaoPayAdapter: PaymentService {
    private let kakaoPay: KakaoPayInterface
    
    init(kakaoPay: KakaoPayInterface = KakaoPay()) {
        self.kakaoPay = kakaoPay
    }
    
    func processPayment(amount: Double) {
        kakaoPay.processTransaction(amount)
    }
}

// MARK: - 클라이언트 (Client)
class PaymentProcessor {
    private let paymentService: PaymentService
    
    init(paymentService: PaymentService) {
        self.paymentService = paymentService
    }
    
    func pay(amount: Double) {
        paymentService.processPayment(amount: amount)
    }
}

// MARK: - 사용 예시
let naverPayProcessor = PaymentProcessor(paymentService: NaverPayAdapter())
let kakaoPayProcessor = PaymentProcessor(paymentService: KakaoPayAdapter())

naverPayProcessor.pay(amount: 50000)  // 출력: 네이버페이 결제 처리: 50000.0원
kakaoPayProcessor.pay(amount: 30000)  // 출력: 카카오페이 결제 처리: 30000.0원
```
