---

### 1. Текст для Use Case Diagram

<pre>
```mermaid
graph LR
    subgraph АРМ Зв'язківця
        UC_View(["Перегляд стану ретрансляторів"])
        UC_Alerts(["Отримання сповіщень про аварії"])
        UC_Config(["Зміна конфігурації частот"])
        UC_Users(["Керування користувачами"])
    end

    Operator((Черговий зв'язківець))
    Admin((Адміністратор зв'язку))
    Broker["MQTT Брокер / IPSC Сервер"]

    Operator --- UC_View
    Operator --- UC_Alerts
    Admin --- UC_View
    Admin --- UC_Config
    Admin --- UC_Users

    UC_View --- Broker
    UC_Alerts --- Broker

    UC_Config -.->|include| UC_View
    UC_Alerts -.->|extend| UC_View
```
</pre>

---

### 2. Текст для Sequence Diagram

<pre>
```mermaid
sequenceDiagram
    autonumber
    participant R as Ретранслятор DMR
    participant B as MQTT Брокер
    participant S as Сервер АРМ
    participant DB as База Даних
    participant UI as Інтерфейс Operator
    actor Op as Черговий зв'язківець

    Note over R, B: Виникнення аварії на лінії
    R->>B: Публікація alarm/repeater_01
    activate B
    B->>S: Передача повідомлення
    deactivate B
    
    activate S
    S->>DB: Зберегти подію в лог
    S->>UI: Надсилання сповіщення Websocket
    activate UI
    
    UI->>Op: Показати червоне вікно та звук
    activate Op
    Op-->>UI: Натискає Підтвердити
    deactivate Op
    
    UI->>S: POST /api/alarms/ack
    deactivate UI
    S->>DB: Оновити статус аварії
    S-->>UI: OK
    deactivate S
```
</pre>

---

### 3. Текст для Activity Diagram

<pre>
```mermaid
graph TD
    Start((Початок)) --> Login[Авторизація Адміністратора]
    Login --> SelectRep[Вибір ретранслятора]
    SelectRep --> LoadConfig[Завантаження конфігурації]
    
    LoadConfig --> InputData{Введення нових частот}
    
    InputData -- Некоректні --> ShowError[Показати помилку]
    ShowError --> InputData
    
    InputData -- Валідні --> SaveConfig[Зберегти зміни локально]
    SaveConfig --> Confirm{Застосувати на обладнанні}
    
    Confirm -- Скасувати --> Close[Закрити вікно]
    Close --> End((Кінець))
    
    Confirm -- Застосувати --> GenerateCMD[Генерація команди]
    GenerateCMD --> SendMQTT[Відправка через MQTT Брокер]
    SendMQTT --> WaitResp[Очікування відповіді від заліза]
    
    WaitResp --> CheckResp{Результат тесту}
    
    CheckResp -- Успіх --> UpdateUI[Статус Зміни застосовано]
    UpdateUI --> LogEvent[Записати подію в аудит]
    LogEvent --> End
    
    CheckResp -- Помилка --> ShowFail[Помилка конфігурації]
    ShowFail --> LogEvent
