graph TD
    %% Актори
    Operator((Черговий зв'язківець))
    Admin((Адміністратор зв'язку))
    Broker[MQTT Брокер / IPSC Сервер]

    %% Прецеденти Оператора
    UC_View[Перегляд стану ретрансляторів]
    UC_Alerts[Отримання сповіщень про аварії]
    
    %% Прецеденти Адміністратора
    UC_Config[Зміна конфігурації частот/IP]
    UC_Users[Керування користувачами системи]
    
    %% Зв'язки Оператора
    Operator --> UC_View
    Operator --> UC_Alerts

    %% Зв'язки Адміністратора
    Admin --> UC_View
    Admin --> UC_Config
    Admin --> UC_Users

    %% Залежності (Include/Extend)
    UC_Config -.-> |<<include>>| UC_View
    UC_Alerts -.-> |<<extend>>| UC_View

    %% Зв'язок із зовнішньою системою
    UC_View --- Broker
    UC_Alerts --- Broker
