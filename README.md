
# Домашнее задание к занятию «Микросервисы: принципы» - Филатов А. В.

Вы работаете в крупной компании, которая строит систему на основе микросервисной архитектуры.
Вам как DevOps-специалисту необходимо выдвинуть предложение по организации инфраструктуры для разработки и эксплуатации.

## Задача 1: API Gateway 

Предложите решение для обеспечения реализации API Gateway. Составьте сравнительную таблицу возможностей различных программных решений. На основе таблицы сделайте выбор решения.

Решение должно соответствовать следующим требованиям:
- маршрутизация запросов к нужному сервису на основе конфигурации,
- возможность проверки аутентификационной информации в запросах,
- обеспечение терминации HTTPS.

Обоснуйте свой выбор.

### Решение 1

#### Сравнительная таблица (API Gateway)

| Функция                          | Kong                              | NGINX                            | Traefik                          | Yandex API Gateway               | Куратор API Gateway              |
|-----------------------------------|-----------------------------------|----------------------------------|----------------------------------|----------------------------------|----------------------------------|
| **Маршрутизация запросов**        | Поддержка сложных правил          | Поддержка сложных правил         | Простая автоматическая маршрутизация | Поддержка сложных правил         | Интеграция с российскими сервисами |
| **Аутентификация**                | Плагины для OAuth2, JWT           | OAuth2, JWT, Basic Auth          | Плагины для OAuth2, JWT          | Поддержка OAuth2 и других механизмов | Поддержка OAuth2 и SSO           |
| **Терминация HTTPS**              | Да                                | Да                               | Да                               | Да                               | Да                               |
| **Масштабируемость**              | Высокая                           | Высокая                          | Простая масштабируемость         | Автоматическая масштабируемость в облаке Яндекс | Поддержка масштабирования в облаке |

**Выбор: NGINX или Kong**

- **NGINX**: Широко используется в России, поддерживает сложные сценарии маршрутизации и аутентификации, имеет возможность автономного развертывания.
- **Kong**: Предлагает большую гибкость с плагинами и поддержкой аутентификации, подходит для масштабируемых решений.


## Задача 2: Брокер сообщений

Составьте таблицу возможностей различных брокеров сообщений. На основе таблицы сделайте обоснованный выбор решения.

Решение должно соответствовать следующим требованиям:
- поддержка кластеризации для обеспечения надёжности,
- хранение сообщений на диске в процессе доставки,
- высокая скорость работы,
- поддержка различных форматов сообщений,
- разделение прав доступа к различным потокам сообщений,
- простота эксплуатации.

Обоснуйте свой выбор.

### Решение 2

При выборе брокера сообщений важно учитывать автономность, отказоустойчивость и простоту эксплуатации.

#### Сравнительная таблица (Брокеры сообщений)

| Функция                          | RabbitMQ                          | Apache Kafka                     | NATS                             | ActiveMQ                         | Tarantool                        |
|-----------------------------------|-----------------------------------|----------------------------------|----------------------------------|----------------------------------|----------------------------------|
| **Кластеризация**                 | Да, поддержка HA                  | Да, встроенная поддержка кластеров | Поддержка кластеров              | Поддержка кластеров              | Поддержка репликации             |
| **Хранение сообщений на диске**   | Да                                | Да                               | Нет                              | Да                               | Да                               |
| **Скорость работы**               | Высокая                           | Очень высокая                    | Очень высокая                    | Умеренная                        | Высокая                          |
| **Поддержка форматов сообщений**  | AMQP, JSON, др.                   | JSON, Avro, Protobuf             | Текстовые, бинарные              | JMS, JSON, др.                   | Lua, JSON, Protobuf              |
| **Разделение прав доступа**       | Поддержка ролей                   | ACL                              | Минимальная поддержка            | ACL                              | Ролевое управление доступом      |
| **Простота эксплуатации**         | Простая установка, UI             | Требуется знание для настройки   | Очень простая конфигурация       | Средняя сложность                | Простой интерфейс, поддержка на русском |

**Выбор: RabbitMQ или Apache Kafka**

- **RabbitMQ**: Обеспечивает высокую скорость передачи данных, надежное хранение на диске, простую настройку и имеет активное сообщество в России.
- **Apache Kafka**: Подходит для крупных систем с высокими объемами сообщений и поддерживает масштабируемость и кластеризацию.

## Задача 3: API Gateway * (необязательная)

### Есть три сервиса:

**minio**
- хранит загруженные файлы в бакете images,
- S3 протокол,

**uploader**
- принимает файл, если картинка сжимает и загружает его в minio,
- POST /v1/upload,

**security**
- регистрация пользователя POST /v1/user,
- получение информации о пользователе GET /v1/user,
- логин пользователя POST /v1/token,
- проверка токена GET /v1/token/validation.

### Необходимо воспользоваться любым балансировщиком и сделать API Gateway:

**POST /v1/register**
1. Анонимный доступ.
2. Запрос направляется в сервис security POST /v1/user.

**POST /v1/token**
1. Анонимный доступ.
2. Запрос направляется в сервис security POST /v1/token.

**GET /v1/user**
1. Проверка токена. Токен ожидается в заголовке Authorization. Токен проверяется через вызов сервиса security GET /v1/token/validation/.
2. Запрос направляется в сервис security GET /v1/user.

**POST /v1/upload**
1. Проверка токена. Токен ожидается в заголовке Authorization. Токен проверяется через вызов сервиса security GET /v1/token/validation/.
2. Запрос направляется в сервис uploader POST /v1/upload.

**GET /v1/user/{image}**
1. Проверка токена. Токен ожидается в заголовке Authorization. Токен проверяется через вызов сервиса security GET /v1/token/validation/.
2. Запрос направляется в сервис minio GET /images/{image}.

### Ожидаемый результат

Результатом выполнения задачи должен быть docker compose файл, запустив который можно локально выполнить следующие команды с успешным результатом.
Предполагается, что для реализации API Gateway будет написан конфиг для NGinx или другого балансировщика нагрузки, который будет запущен как сервис через docker-compose и будет обеспечивать балансировку и проверку аутентификации входящих запросов.
Авторизация
curl -X POST -H 'Content-Type: application/json' -d '{"login":"bob", "password":"qwe123"}' http://localhost/token

**Загрузка файла**

curl -X POST -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJib2IifQ.hiMVLmssoTsy1MqbmIoviDeFPvo-nCd92d4UFiN2O2I' -H 'Content-Type: octet/stream' --data-binary @yourfilename.jpg http://localhost/upload

**Получение файла**
curl -X GET http://localhost/images/4e6df220-295e-4231-82bc-45e4b1484430.jpg

---

#### [Дополнительные материалы: как запускать, как тестировать, как проверить](https://github.com/netology-code/devkub-homeworks/tree/main/11-microservices-02-principles)

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---