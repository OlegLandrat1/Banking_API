## REST API для банковского сервиса.

Язык: Go 1.23+.
Фреймворки/библиотеки:
Маршрутизация: gorilla/mux.
Работа с БД: PostgreSQL + lib/pq.
Аутентификация: JWT (golang-jwt/jwt/v5).
Логирование: logrus.
Шифрование: bcrypt, HMAC-SHA256, PGP.
Дополнительно: gomail.v2 (отправка email), beevik/etree (парсинг XML).
База данных: PostgreSQL 17 с расширением pgcrypto.

### Безопасность
- Пароли хешируются с помощью bcrypt
- Данные карт шифруются с помощью PGP
- CVV хешируется с помощью bcrypt
- Все запросы защищены JWT-аутентификацией
- Используются параметризованные SQL-запросы

### Интеграции
- SMTP для отправки уведомлений
- SOAP API ЦБ РФ для получения ключевой ставки
- Автоматическое списание платежей по кредитам

### Логирование
Логи сохраняются в файл `app.log` и выводятся в консоль. Используется logrus с настройками:
- Уровень логирования: Info
- Формат: JSON
- Выход: файл и консоль

### Функции
- Генерация номеров карт с помощью алгоритма Луна
- Расчет аннуитетных платежей для кредитов
- Транзакции для обеспечения атомарности операций
- Middleware для аутентификации и логирования


## Структура проекта

```
.
├── cmd/
│   └── api/
│       └── main.go
├── internal/
│   ├── handlers/
│   ├── models/
│   ├── repositories/
│   ├── services/
│   └── utils/
├── migrations/
├── scripts/
└── README.md
```

## Установка и запуск

1. Клонирование репозитория:
```bash
git clone <repository-url>
cd go-2024-2025-2
```

2. Установка зависимостей:
```bash
go mod download
```

3. Настройка переменных окружения:
```bash
export DB_HOST=localhost
export DB_PORT=5432
export DB_USER=postgres
export DB_PASSWORD=your_password
export DB_NAME=bank
export JWT_SECRET=your_jwt_secret
export SMTP_HOST=smtp.example.com
export SMTP_PORT=587
export SMTP_USER=your_email@example.com
export SMTP_PASSWORD=your_smtp_password
```
4. Генерация ключей:
```bash
go run scripts/generate_key.go
```

5. Запуск сервиса:
```bash
go run cmd/api/main.go
```

Сервис доступен по адресу `http://localhost:8080`

## Использование

- **Регистрация**  
  `POST /api/register`  
  
  ```json
  {
    "username": "имя_пользователя",
    "email": "email@example.com",
    "password": "пароль"
  }
  ```

- **Вход**  
  `POST /api/login`  
 
  ```json
  {
    "email": "email@example.com",
    "password": "пароль"
  }
  ```
  Возвращает JWT токен для авторизованных запросов.

### Управление счетами

- **Создать счет**  
  `POST /api/accounts/create`  

  ```json
  {
    "type": "DEBIT"
  }
  ```

- **Получить список счетов**  
  `GET /api/accounts/list`

- **Пополнить счет**  
  `POST /api/accounts/deposit`  

  ```json
  {
    "account_id": 1,
    "amount": 1000.00
  }
  ```

- **Снять средства**  
  `POST /api/accounts/withdraw`  

  ```json
  {
    "account_id": 1,
    "amount": 500.00
  }
  ```

- **Перевод между счетами**  
  `POST /api/accounts/transfer`  

  ```json
  {
    "from_account_id": 1,
    "to_account_id": 2,
    "amount": 300.00
  }
  ```

- **Создать виртуальную карту**  
  `POST /api/cards/create`  

  ```json
  {
    "account_id": 1
  }
  ```

- **Получить список карт**  
  `GET /api/cards/list`

- **Получить информацию о карте**  
  `GET /api/cards/get?card_id=1`

- **Оформить кредит**  
  `POST /api/credits/create`  

  ```json
  {
    "account_id": 1,
    "amount": 10000.00,
    "term": 12,
    "rate": 15.5
  }
  ```

- **Получить список кредитов**  
  `GET /api/credits/list`

- **Получить информацию о кредите**  
  `GET /api/credits/get?id=1`

- **Получить график платежей**  
  `GET /api/credits/schedule?id=1`

- **Создать платеж**  
  `POST /api/payments/create`  

  ```json
  {
    "credit_id": 1,
    "amount": 1000.00,
    "due_date": "2024-05-01T00:00:00Z"
  }
  ```

- **Обработать платеж**  
  `POST /api/payments/process?payment_id=1`
