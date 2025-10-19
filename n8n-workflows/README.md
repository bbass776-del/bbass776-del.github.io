# N8N Workflows Collection

Коллекция готовых workflow для n8n - мощной платформы автоматизации рабочих процессов.

## 📁 Структура проекта

```
n8n-workflows/
├── basic/                    # Базовые примеры
│   └── simple-data-processing.json
├── webhook/                  # Workflow с webhook
│   └── email-notification-webhook.json
├── api-integration/          # API интеграции
│   └── weather-data-processor.json
├── data-processing/          # Обработка данных
│   └── csv-to-database.json
└── README.md
```

## 🚀 Доступные Workflow

### 1. Simple Data Processing (`basic/simple-data-processing.json`)
**Описание:** Базовый пример обработки данных с валидацией и сохранением в базу данных.

**Функции:**
- Ручной запуск workflow
- Обработка пользовательских данных
- Проверка возраста
- Условная логика
- Сохранение в PostgreSQL

**Использование:**
1. Импортируйте workflow в n8n
2. Настройте подключение к PostgreSQL
3. Запустите workflow вручную

### 2. Email Notification Webhook (`webhook/email-notification-webhook.json`)
**Описание:** Webhook для отправки email уведомлений с валидацией данных.

**Функции:**
- HTTP webhook endpoint
- Валидация входящих данных
- Приоритизация сообщений
- Отправка email через SMTP
- Логирование в базу данных
- HTTP ответы с результатами

**API Endpoint:** `POST /webhook/email-notification`

**Пример запроса:**
```json
{
  "to": "user@example.com",
  "subject": "Тестовое сообщение",
  "message": "Привет! Это тестовое сообщение.",
  "priority": "high"
}
```

**Настройка:**
1. Настройте SMTP credentials в n8n
2. Настройте подключение к PostgreSQL
3. Активируйте workflow

### 3. Weather Data Processor (`api-integration/weather-data-processor.json`)
**Описание:** Автоматический сбор и обработка данных о погоде с уведомлениями.

**Функции:**
- Ежедневный запуск по расписанию (9:00 UTC)
- Получение данных о погоде для нескольких городов
- Обработка и нормализация данных
- Сохранение в базу данных
- Уведомления о высокой температуре
- Интеграция со Slack

**Настройка:**
1. Получите API ключ OpenWeatherMap
2. Настройте Slack webhook URL
3. Настройте SMTP для email уведомлений
4. Настройте подключение к PostgreSQL

### 4. CSV to Database Processor (`data-processing/csv-to-database.json`)
**Описание:** Обработка CSV файлов с валидацией и загрузкой в базу данных.

**Функции:**
- Чтение CSV файлов
- Парсинг и валидация данных
- Очистка и нормализация
- Сохранение в PostgreSQL
- Генерация отчетов
- Email уведомления с результатами

**Поддерживаемые поля CSV:**
- `customer_name` (обязательно)
- `product` (обязательно)
- `price` (обязательно)
- `quantity` (обязательно)
- `date` (обязательно)
- `email` (опционально)

## 🛠️ Настройка и установка

### Предварительные требования

1. **n8n** - установленная и настроенная платформа
2. **PostgreSQL** - для хранения данных
3. **SMTP сервер** - для отправки email
4. **API ключи** (для соответствующих workflow):
   - OpenWeatherMap API
   - Slack webhook URL

### Установка

1. **Импорт workflow:**
   ```bash
   # Скопируйте JSON файлы в n8n
   # Или импортируйте через веб-интерфейс n8n
   ```

2. **Настройка credentials:**
   - PostgreSQL connection
   - SMTP email settings
   - OpenWeatherMap API key
   - Slack webhook URL

3. **Активация workflow:**
   - Включите нужные workflow в n8n
   - Проверьте настройки подключений

## 📊 База данных

### Схемы таблиц

#### `processed_users`
```sql
CREATE TABLE processed_users (
    id SERIAL PRIMARY KEY,
    full_name VARCHAR(255),
    email VARCHAR(255),
    age INTEGER,
    is_adult BOOLEAN,
    processed_at TIMESTAMP,
    message TEXT
);
```

#### `email_logs`
```sql
CREATE TABLE email_logs (
    id SERIAL PRIMARY KEY,
    email_id VARCHAR(255),
    to_email VARCHAR(255),
    from_email VARCHAR(255),
    subject TEXT,
    priority VARCHAR(50),
    sent_at TIMESTAMP,
    status VARCHAR(50)
);
```

#### `weather_data`
```sql
CREATE TABLE weather_data (
    id SERIAL PRIMARY KEY,
    data_id VARCHAR(255),
    city VARCHAR(255),
    country VARCHAR(3),
    temperature INTEGER,
    feels_like INTEGER,
    humidity INTEGER,
    pressure INTEGER,
    description TEXT,
    wind_speed DECIMAL,
    wind_direction INTEGER,
    visibility DECIMAL,
    cloudiness INTEGER,
    sunrise TIMESTAMP,
    sunset TIMESTAMP,
    temp_category VARCHAR(20),
    timestamp TIMESTAMP
);
```

#### `sales_data`
```sql
CREATE TABLE sales_data (
    id SERIAL PRIMARY KEY,
    data_id VARCHAR(255),
    customer_name VARCHAR(255),
    email VARCHAR(255),
    product VARCHAR(255),
    price DECIMAL,
    quantity INTEGER,
    total_amount DECIMAL,
    order_category VARCHAR(20),
    date TIMESTAMP,
    processed_at TIMESTAMP,
    row_number INTEGER
);
```

#### `processing_reports`
```sql
CREATE TABLE processing_reports (
    id SERIAL PRIMARY KEY,
    report_id VARCHAR(255),
    total_records INTEGER,
    total_amount DECIMAL,
    average_order DECIMAL,
    categories JSONB,
    top_products JSONB,
    processing_time TIMESTAMP
);
```

## 🔧 Кастомизация

### Изменение расписания
Для изменения времени запуска workflow с расписанием, отредактируйте cron выражение в узле "Daily Schedule":
```javascript
// Примеры cron выражений:
"0 9 * * *"    // Каждый день в 9:00 UTC
"0 */6 * * *"  // Каждые 6 часов
"0 0 * * 1"    // Каждый понедельник в полночь
```

### Добавление новых городов
В workflow погоды измените список городов в узле "Cities List":
```javascript
"cities": "Moscow,London,New York,Tokyo,Paris,Beijing,Sydney"
```

### Настройка уведомлений
Измените email адреса и сообщения в соответствующих узлах:
- `fromEmail` - отправитель
- `toEmail` - получатель
- `subject` - тема письма
- `message` - текст сообщения

## 🐛 Отладка

### Логирование
Все workflow включают подробное логирование. Проверьте:
1. Execution logs в n8n
2. Console output в узлах Code
3. Database logs для ошибок подключения

### Частые проблемы

1. **Ошибки подключения к БД:**
   - Проверьте credentials
   - Убедитесь, что таблицы созданы
   - Проверьте права доступа

2. **Ошибки SMTP:**
   - Проверьте настройки email
   - Убедитесь в правильности портов
   - Проверьте SSL/TLS настройки

3. **Ошибки API:**
   - Проверьте API ключи
   - Убедитесь в правильности URL
   - Проверьте лимиты API

## 📝 Лицензия

Этот проект распространяется под лицензией MIT. См. файл LICENSE для подробностей.

## 🤝 Вклад в проект

1. Fork репозитория
2. Создайте feature branch
3. Внесите изменения
4. Создайте Pull Request

## 📞 Поддержка

Если у вас есть вопросы или проблемы:
1. Проверьте документацию n8n
2. Создайте issue в репозитории
3. Обратитесь к сообществу n8n

---

**Примечание:** Перед использованием в продакшене обязательно протестируйте все workflow в тестовой среде и настройте мониторинг.