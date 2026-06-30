# Практичне заняття: Побудова поведінкових UML-діаграм

**Предметна область:** Система моніторингу та управління мережею радіозв'язку (АРМ Зв'язківця)  
**Інструмент:** Markdown + Mermaid.js

---

## 1. Діаграма варіантів використання (Use Case Diagram)

```mermaid
graph TD
    %% Актори
    Operator(("Черговий зв'язківець"))
    Admin(("Адміністратор зв'язку"))
    Broker["MQTT Брокер / IPSC Сервер"]

    %% Прецеденти
    UC_View["Перегляд стану ретрансляторів"]
    UC_Alerts["Отримання сповіщень про аварії"]
    UC_Config["Зміна конфігурації частот/IP"]
    UC_Users["Керування користувачами системи"]

    %% Зв'язки
    Operator --> UC_View
    Operator --> UC_Alerts
    Admin --> UC_View
    Admin --> UC_Config
    Admin --> UC_Users

    %% Залежності
    UC_Config -.-> |include| UC_View
    UC_Alerts -.-> |extend| UC_View
    UC_View --- Broker
    UC_Alerts --- Broker
```
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
    ```
    flowchart TD
    Start([Вхід в меню конфігурації]) --> Input[Введення параметрів IP, Частота, ID]
    Input --> Save[Натискання кнопки Зберегти]
    
    Save --> Cond1{Валідація полів успішна?}
    
    Cond1 -- Ні --> ErrorFields[Підсвітити помилки валідації]
    ErrorFields --> Input
    
    Cond1 -- Так --> Ping[Перевірка доступності IP]
    
    Ping --> Cond2{Пристрій в мережі?}
    
    Cond2 -- Так --> DBWrite[Запис конфігурації в БД]
    DBWrite --> SendCmd[Відправка команди на ретранслятор]
    SendCmd --> Success[Показати індикатор Успішно]
    Success --> End([Кінець])
    
    Cond2 -- Ні --> ErrorNet[Показати помилку Пристрій недоступний]
    ErrorNet --> Cond3{Зберегти в офлайн?}
    
    Cond3 -- Так --> DBWrite
    Cond3 -- Ні --> Input
