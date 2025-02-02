### 싱글턴 패턴
- 목적
    - 특정 용도로 객체를 하나만 생성하여, 공용으로 사용하고 싶을 때 사용하는 패턴
    - 클래스에 대한 인스턴스를 최초 생성될 때 한번만 생성해서 전역에서 사용

- 실제 사용 예시
    - private init의 역할 - 여러 곳에서 인스턴스를 생성하는것을 막기 위함
    
    ```swift
    final class NetworkSetting {
        static let shared = NetworkSetting()
        
        private init() {}
        
        var setNetwork: XplaChainNetwork = XplaChainNetwork(rawValue: UserDefaults.standard.string(forKey: NetworkChainManager.networkChainKey) ?? "Mainnet") ?? .MainNet
    }
    ```
    

- 단점
    - 여러 스레드에서 접근 하는 경우 race condition 발생 가능(동시성 문제)
    - 코드의 어디서나 상태를 변경할 수 있기 때문에 예측이 어렵다.
    - 강한 결합도
        
        ```swift
        class DataService {
            func fetchData() {
                NetworkManager.shared.request() // 강한 결합
            }
        }
        ```
        
        - protocol을 활용하여 결합도를 낮출 수 있음
        
        ```swift
        // 의존성 주입을 사용한 더 나은 방법
        protocol NetworkService {
            func request()
        }
        
        class DataService {
            let network: NetworkService
            
            init(network: NetworkService) {
                self.network = network
            }
            
            func fetchData() {
                network.request()
            }
        }
        ```
