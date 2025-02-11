### SOLID 원칙
- SRP - 단일 책임 원칙
    - 하나의 클래스는 하나의 책임만 가진다.
    - 클래스를 변경해야 하는 이유는 오직 하나여야 한다.
    - 하나의 책임, 변경해야 하는 이유가 오직 하나
    - 코드의 응집도 향상
    - 각 클래스가 하나의 책임만 가짐으로써 변경이 필요할 때 해당 부분만 수정하면 된다.
    - 예시코드
        
        ```swift
        class UserManager {
            // 1. 사용자 데이터 관리 책임
            func saveUser(user: User) { }
            func deleteUser(id: String) { }
            
            // 2. 이메일 발송 책임
            func sendWelcomeEmail(to user: User) { }
            func sendPasswordResetEmail(to user: User) { }
            
            // 3. 사용자 인증 책임
            func login(email: String, password: String) { }
            func logout() { }
        }
        ```
        
        - UserManager 클래스가 3가지의 책임을 가지는 중
        - 만약 사용자 데이터 저장 방식을 변경한다? 이메일 발송, 사용자 인증은 아무런 변경 사항이 없지만 UsrManager 클래스를 변경해야 한다.
    - 변경 예시
        
        ```swift
        class UserManager {
            func saveUser(user: User) { }
            func deleteUser(id: String) { }
        }
        
        // 이메일 발송만 담당
        class EmailService {
            func sendWelcomeEmail(to user: User) { }
            func sendPasswordResetEmail(to user: User) { }
        }
        
        // 인증만 담당
        class AuthenticationService {
            func login(email: String, password: String) { }
            func logout() { }
        }
        ```
        
        - 클래스 3개로 분리 (각 클래스당 하나의 책임, 역할)
        - 각 클래스의 변경 이유는 하나 뿐
    - UIViewController라는 클래스가 하나의 책임만 가지는지? → 아님
        - 뷰의 생명주기
        - 화면 전환
        - 레이아웃 관리 등..
        - 이러한 기능들을 각각 별도의 클래스로 분리하면 개발자가 매번 여러번 조합해서 사용해야 하는 어려움 때문인가?
- OCP - 개방-폐쇄 원칙
    - 소프트웨어 요소는 확장에는 열려 있으나, 수정에는 닫혀 있어야 한다.
    - 기존 코드를 변경하지 않고 새로운 기능을 추가할 수 있어야 한다.
    - 확장성 향상
    - 예시 코드
        
        ```swift
        protocol PaymentProcessor {
            func processPayment(amount: Double)
        }
        
        // 신용카드 결제 처리기
        class CreditCardProcessor: PaymentProcessor {
            func processPayment(amount: Double) {
                // 신용카드 결제 처리
                print("신용카드로 \(amount)원 결제")
            }
        }
        
        // 결제를 실행하는 클래스
        class PaymentService {
            private let processor: PaymentProcessor
            
            init(processor: PaymentProcessor) {
                self.processor = processor
            }
            
            func pay(amount: Double) {
                processor.processPayment(amount: amount)
            }
        }
        
        // 새로운 결제 수단 추가
        class ApplePayProcessor: PaymentProcessor {
            func processPayment(amount: Double) {
                // Apple Pay 결제 처리
                print("Apple Pay로 \(amount)원 결제")
            }
        }
        
        // 사용 예시
        let creditCardPayment = PaymentService(processor: CreditCardProcessor())
        let applePayPayment = PaymentService(processor: ApplePayProcessor())
        
        creditCardPayment.pay(amount: 50000)  // "신용카드로 50000원 결제"
        applePayPayment.pay(amount: 30000)    // "Apple Pay로 30000원 결제"
        ```
        
        - ApplePayProcessor(확장), 아무런 코드 변경 없이(수정에 닫혀있음) PaymentProcessor 프로토콜을 준수함으로서 기능 추가 가능(애플 페이 결제)
- LSP - 리스코프 치환 원칙
    - 상위 타입의 객체를 하위 타입의 객체로 치환해도 프로그램의 정확성이 깨지지 않아야 한다.
    - 상위 타입(Protocol) 이라 하고, 하위 타입을 상위 타입을 준수 하는 구현체라고 하면 실제로 상위 타입이 필요한 곳에 상위 타입 객체가 들어가는 경우가 있는지?
    - 단순히 부모-자식 간의 상속관계는 상, 하위라고 볼 수 있는건지 상위 하위를 나누는 기준이 뭔지?
    - 예시 코드
- ISP - 인터페이스 분리 원칙
    - 클라이언트는 자신이 사용하지 않는 메서드에 의존하지 않아야 한다.
    - 큰 인터페이스를 여러 개의 작은 인터페이스로 분리해야 한다.
    - 불필요한 의존성 제거
    - 예시 코드
    
    ```swift
    // 위반 사례
    protocol Printer {
        func print()
        func scan()
        func fax()
    }
    
    // 일반 프린터는 fax 기능이 없는데도 구현해야 함
    class SimplePrinter: Printer {
        func print() { /* 구현 */ }
        func scan() { fatalError("지원하지 않는 기능") }
        func fax() { fatalError("지원하지 않는 기능") }
    }
    
    // ISP를 지키는 방법
    protocol Printable {
        func print()
    }
    
    protocol Scannable {
        func scan()
    }
    
    protocol Faxable {
        func fax()
    }
    
    // 필요한 기능만 채택
    class SimplePrinter: Printable {
        func print() { /* 구현 */ }
    }
    ```
    
- DIP - 의존성 역전 법칙
    - 결합도 감소
    - 고수준 모듈은 저수준 모듈에 의존해서는 안 되며, 둘 다 추상화에 의존해야 한다.
        - 고수준 모듈(viewModel) 저수준 모듈(repository) 둘 다 repositoryInterface에 의존하도록
            - 고수준 모듈 - 무엇을 할지? → 어떻게 하는지는 알 필요 X
            - 저수준 모듈 - 어떻게 할지?
    - 고수준 모듈이 저수준 모듈에 직접 의존하지 않는다.
    - 예시 코드
        
        ```swift
        // 1. 저수준 모듈을 위한 프로토콜 (추상화)
        protocol UserRepository {
            func fetchUser(id: String) async throws -> User
            func saveUser(_ user: User) async throws
        }
        
        protocol NetworkProvider {
            func request<T: Decodable>(endpoint: Endpoint) async throws -> T
        }
        
        // 2. 저수준 모듈 구현
        class UserRepositoryImpl: UserRepository {
            private let networkProvider: NetworkProvider
            
            init(networkProvider: NetworkProvider) {
                self.networkProvider = networkProvider
            }
            
            func fetchUser(id: String) async throws -> User {
                let endpoint = UserEndpoint.getUser(id: id)
                return try await networkProvider.request(endpoint: endpoint)
            }
            
            func saveUser(_ user: User) async throws {
                let endpoint = UserEndpoint.saveUser(user)
                try await networkProvider.request(endpoint: endpoint)
            }
        }
        
        class NetworkProviderImpl: NetworkProvider {
            func request<T: Decodable>(endpoint: Endpoint) async throws -> T {
                // 실제 네트워크 통신 구현
            }
        }
        
        // 3. 고수준 모듈
        class UserViewModel {
            private let userRepository: UserRepository
            @Published var user: User?
            
            init(userRepository: UserRepository) {
                self.userRepository = userRepository
            }
            
            func fetchUserInfo(id: String) async {
                do {
                    user = try await userRepository.fetchUser(id: id)
                } catch {
                    // 에러 처리
                }
            }
        }
        
        // 4. 실제 사용
        class UserViewController: UIViewController {
            // 의존성 주입
            let networkProvider = NetworkProviderImpl()
            let userRepository = UserRepositoryImpl(networkProvider: networkProvider)
            let viewModel = UserViewModel(userRepository: userRepository)
        }
        
        // 5. 테스트를 위한 Mock 객체
        class MockUserRepository: UserRepository {
            func fetchUser(id: String) async throws -> User {
                return User(id: "test", name: "Test User")
            }
            
            func saveUser(_ user: User) async throws {
                // 테스트용 구현
            }
        }
        
        // 6. 테스트 코드
        class UserViewModelTests: XCTestCase {
            func testFetchUser() async {
                let mockRepository = MockUserRepository()
                let viewModel = UserViewModel(userRepository: mockRepository)
                
                await viewModel.fetchUserInfo(id: "test")
                XCTAssertNotNil(viewModel.user)
            }
        }
        ```
