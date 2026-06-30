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
