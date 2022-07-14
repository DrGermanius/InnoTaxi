# InnoTaxi

InnoTaxi - приложение для заказа такси. 


Функциональные требования: 

Taxi types:
1) Economy
2) Comfort
3) Business

Роли:
1) User
2) Driver
3) Analyst

User:
1) User может зарегистрироваться. 
    - Поля для регистрации: name, phone number, email, password.
2) User может войти в систему.
    - Поля для входа: phone number, password.
3) User может заказать такси. 
    - Поля заказа: taxi type, from, to.
    - При заказе такси ищется свободный (в статусе free) водитель и, если он найден, создается order со статусом in progress. Статус водителя меняется на busy.
    - Если свободного водителя не найдено, пользователь остается в состоянии поиска водителя в течении некоторого(настраиваемого) времени. Если за это время водитель не находится, пользователю возвращается ответ с отказом.
    - При отсутствии свободных водителей из ожидающих пользователей образуется очередь. Первый заказавший такси пользователей должен первым получить водителя или сообщение, что водитель не найден.
4) User может оценить поездку. Оценить можно только последнюю поездку, если с нее прошло менее некоторого(настраиваемого) времени. Оценка от 1 до 5 включительно.
5) User может узнать свой общий рейтинг.
6) User может просмотреть свои поездки (taxi type, driver, from, to).

Driver:
1) Driver может зарегистрироваться. 
    - Поля для регистрации: name, phone number, email, password, taxi type.
2) Driver моджет войти в систему.
    - Поля для входа: phone number, password.
3) Driver может сменить свой статус с busy на free, когда поездка закончена. При этом статус order'a меняется на finished.
4) Driver может оценить поездку. Оценить можно только последнюю поездку, если с нее прошло менее некоторого(настраиваемого) времени. Оценка от 1 до 5 включительно.
5) Driver может узнать свой общий рейтинг.
6) Driver может просмотреть свои поездки (taxi type, user, from to).
7) У Driver могут быть статусы busy и free.

Analyst:
1) Analyst может смотреть статистику заказов(количество по дням, месяцам).
2) Analyst может смотреть рейтинг всех водителей.
3) Analyst может смотреть рейтинг всех пользователей.
4) Analyst имеет заранее созданный в системе аккаунт, логин происходит по username, password.

Order: 
1) У Order могут быть статусы in progress и finished.
2) Поля Order: user, driver, from, to, taxi type, date, status.

Схема приложения: 

<img src="Design diagram.png" width="600" height="500" /> 


Сервис аналитики: все регистрации и выполненные заказы должны записываться в аналитическую базу. 

Допускается добавление новых сервисов, уменьшать количество сервисов не допускается. При изменении схемы(добавление новых сервисов) приложения необходимо дополнить диаграмму и выложить себе в репозиторий в .png и .drawio форматах.

Нефункциональные требования:

1) Общее:
  - UNIX подобная система.
  - Работа по GitHub Flow
    - две основных ветки (master, dev)
    - ответвляемся всегда от dev в feature-ветки
    - делаем работу в них и пушим
    - делаем pull request в dev
    - когда сделали pull request, пишем ментору, приступаем к новой задаче не дожидаясь ревью
    - если ментор оставит комментарии на pr, исправляем и заливаем изменения
    - если ментор аппрувит pr то заливаем его в dev
  - PostgreSQL в качестве БД для driver, user и order сервисов.
  - Cassandra в качестве БД для сервиса аналитики.
  - Kafka в качестве message broker.
  - Для RPC взаимодействия использовать GRPC или Twirp.
  - Для каждого сервиса должен быть описан Dockerfile.
  - Поднятие всего приложения осуществлять через docker-compose. 
  - Все изменяемые переменные(подключение к бд, время ожидания юзером свободного водителя) должны устанавливаться через environment variables.
  - Каждый сервис должен иметь README(описано, что необходимо для запуска, environment variables) и swagger(подробно описаны все эндпоинты). 
  - Все сервисы должны быть покрыты интеграционными тестами.
  - Аутентификация с использованием JWT.
 2) Go: 
  - В качестве HTTP фреймворка использовать Gin. 
  - Для работы с Postgres допустимо использовать чистые запросы или sqlc, sqlx. Использование ORM не допускается.
  - Для работы с Kafka и Cassadnra допустимо использовать любые библиотеки.
  - Для реализации ожидания юзером свободных водителей использовать горутины и каналы, при необходимости пакет sync.
  - Для миграций можно использовать Goose, другие решения допускаются.
  - По структуре проекта посматривать [сюда](https://github.com/golang-standards/project-layout).
  - Pre-commit hooks с golangci-lint.
  - Для тестирования использовать табличные тесты, gomock, testify/suite.
3) Rust:
  - Rocket или Actix-web для работы с HTTP. 
  - Tokio для асинхронных операций.
  - Diesel для миграций и работы с БД.
