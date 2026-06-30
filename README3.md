graph TD
    %% Початкова точка
    Start(( )) --> Login[Авторизація Адміністратора]

    %% Дії
    Login --> SelectRep[Вибір ретранслятора зі списку]
    SelectRep --> LoadConfig[Завантаження поточної конфігурації]
    
    %% Блок прийняття рішень (Decision)
    LoadConfig --> InputData{Введення нових<br/>частот Rx/Tx}
    
    InputData -- "Дані некоректні" --> ShowError[Показати помилку валідації]
    ShowError --> InputData
    
    InputData -- "Дані валідні" --> SaveConfig[Зберегти зміни локально]

    %% Підтвердження
    SaveConfig --> Confirm{Застосувати зміни<br/>на обладнанні?}
    
    Confirm -- "Ні (скасувати)" --> Close[Закрити вікно редагування]
    Close --> End(( ))
    
    Confirm -- "Так (застосувати)" --> GenerateCMD[Генерація команди конфігурації]
    
    %% Взаємодія із зовнішньою мережею
    subgraph Зовнішня мережа
        GenerateCMD --> SendMQTT[Відправка команди через MQTT Брокер]
        SendMQTT --> WaitResp[Очікування відповіді від ретранслятора]
    end
    
    %% Аналіз відповіді
    WaitResp --> CheckResp{Результат<br/>застосування?}
    
    CheckResp -- "Успіх" --> UpdateUI[Оновити статус: Зміни застосовано]
    UpdateUI --> LogEvent[Записати подію в аудит]
    LogEvent --> End
    
    CheckResp -- "Помилка / Таймаут" --> ShowFail[Показати аварію: Помилка конфігурації]
    ShowFail --> LogEvent
