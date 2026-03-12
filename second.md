'''mermaid

graph TD
    %% Вузол Клієнта
    Client[<b>Taxi Console Client / Browser</b><br/>Port: Random]


    %% Інфраструктурні сервіси
    subgraph "Infrastructure"
        Eureka[<b>Discovery Service (Eureka)</b><br/>Port: 8761<br/><i>Реєстр сервісів</i>]
        Gateway[<b>API Gateway</b><br/>Port: 8080<br/><i>Маршрутизація та Load Balancing</i>]
    end

    %% Бізнес-логіка (Оркестрація)
    subgraph "Orchestration Layer"
        Orch[<b>Taxi Orchestrator</b><br/>Port: 8089<br/><i>Координація процесів</i>]
    end

    %% Основні мікросервіси
    subgraph "Core Microservices"
        RideMS[<b>Ride Service</b><br/>Port: 8082<br/><i>Дані поїздок</i>]
        DriverMS[<b>Driver Service</b><br/>Port: 8081<br/><i>Стан водіїв</i>]
        PayMS[<b>Payment Service</b><br/>Port: 8083<br/><i>Оплата</i>]
        PassMS[<b>Passenger Service</b><br/>Port: 8084<br/><i>Історія клієнтів</i>]
    end

    %% Потоки реєстрації (Service Discovery)
    Gateway -. "Реєстрація" .-> Eureka
    Orch -. "Реєстрація" .-> Eureka
    RideMS -. "Реєстрація" .-> Eureka
    DriverMS -. "Реєстрація" .-> Eureka
    PayMS -. "Реєстрація" .-> Eureka
    PassMS -. "Реєстрація" .-> Eureka

    %% Потоки запитів (Data Flow)
    Client -- "1. Запит (http://localhost:8080/api/...)" --> Gateway
    Gateway -- "2. Пошук адреси" --> Eureka
    Gateway -- "3. Маршрутизація (lb://)" --> Orch
    
    Orch -- "4. Запит на сервіс (lb://ride-service)" --> Eureka
    Orch -- "5. Виклик методів" --> RideMS
    Orch -- "5. Виклик методів" --> DriverMS
    
    %% Пост-процесинг
    Orch -- "6. Оплата" --> PayMS
    Orch -- "7. Оновлення історії" --> PassMS

    %% Стилізація
    style Client fill:#f9f,stroke:#333,stroke-width:2px
    style Eureka fill:#fff4dd,stroke:#d4a017,stroke-width:2px
    style Gateway fill:#bbf,stroke:#333,stroke-width:2px
    style Orch fill:#f96,stroke:#333,stroke-width:2px
    style RideMS fill:#dfd,stroke:#333
    style DriverMS fill:#dfd,stroke:#333
    style PayMS fill:#eee,stroke:#333
    style PassMS fill:#eee,stroke:#333
