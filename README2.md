```mermaid
sequenceDiagram
    autonumber
    participant R as Ретранслятор (DMR)
    participant B as MQTT Брокер (IPSC)
    participant S as Сервер АРМ
    participant DB as База Даних
    participant UI as Інтерфейс Оператора (Web)
    actor Op as Черговий зв'язківець

    Note over R, B: Виникнення аварії (напр., зникло живлення)

    R->>B: ПУБЛІКАЦІЯ alarm/repeater_01 (payload: critical_error)
    activate B
    B->>S: ПЕРЕДАЧА ПОВІДОМЛЕННЯ (subscriber)
    deactivate B
    
    activate S
    Note right of S: Парсинг даних,<br/>визначення типу аварії
    S->>DB: Зберегти подію в лог
    
    S->>UI: НАДСИЛАННЯ СПОВІЩЕННЯ (Websocket/SSE)
    activate UI
    
    UI->>Op: Відобразити червоне вікно та звуковий сигнал
    activate Op
    Note over Op: Бачить аварію
    Op-->>UI: Натискає "Підтвердити" (Acknowledge)
    deactivate Op
    
    UI->>S: POST /api/alarms/ack (ID_аварії)
    deactivate UI
    
    S->>DB: Оновити статус аварії (підтверджено)
    S-->>UI: OK
    deactivate S
