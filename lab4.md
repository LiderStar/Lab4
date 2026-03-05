    class Client {
        +Browser / Console
        +Port: Random
        +sendRequest(url)
    }

    class DiscoveryService {
        <<Infrastructure>>
        +Port: 8761
        +ServiceRegistry
        +register(service)
        +lookup(name)
    }

    class APIGateway {
        <<Infrastructure>>
        +Port: 8080
        +RoutingRules
        +LoadBalancer
        +dispatch(request)
    }

    class TaxiOrchestrator {
        <<Orchestration>>
        +Port: 8089
        +RestTemplate (LoadBalanced)
        +requestRide()
        +completeRide()
    }

    class RideService {
        <<Core Service>>
        +Port: 8082
        +ConcurrentHashMap storage
        +saveRide()
        +updateStatus()
    }

    class DriverService {
        <<Core Service>>
        +Port: 8081
        +DriverStatusMap
        +getAvailable()
        +setBusy()
    }

    class PaymentService {
        <<Core Service>>
        +Port: 8083
        +TransactionHistory
        +process()
    }

    class PassengerService {
        <<Core Service>>
        +Port: 8084
        +RideHistory
        +addToHistory()
    }

    %% Зв'язки
    Client ..> APIGateway : 1. Зовнішній запит
    APIGateway --|> DiscoveryService : 2. Service Lookup
    APIGateway --> TaxiOrchestrator : 3. Dynamic Route (lb://)
    
    TaxiOrchestrator --|> DiscoveryService : 4. Register/Lookup
    TaxiOrchestrator o-- RideService : Координує
    TaxiOrchestrator o-- DriverService : Координує
    TaxiOrchestrator o-- PaymentService : Координує
    TaxiOrchestrator o-- PassengerService : Координує
