sequenceDiagram
    autonumber
    actor Op as Оператор
    participant Sys as Веб-інтерфейс системи
    participant Server as Backend
    participant Broker as MQTT Брокер

    Note over Broker, Server: Ретранслятор втратив живлення 220V
    Broker->>Server: Status update: power battery
    
    activate Server
    Server->>Server: Валідація даних
    Server-->>Sys: WebSockets: Критичний статус!
    deactivate Server
    
    activate Sys
    Sys->>Sys: Ввімкнути звуковий сигнал
    Sys-->>Op: Візуальне попередження
    deactivate Sys
    
    Op->>Sys: Натиснути Підтвердити аварію
    activate Sys
    Sys->>Server: Post ack repeater_id 1
    activate Server
    Server-->>Sys: HTTP 200 OK
    deactivate Server
    Sys-->>Op: Зупинка сигналу
    deactivate Sys
