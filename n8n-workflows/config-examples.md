# Примеры конфигурации для N8N Workflows

## 🔐 Настройка Credentials

### PostgreSQL Connection
```json
{
  "host": "localhost",
  "port": 5432,
  "database": "n8n_workflows",
  "user": "n8n_user",
  "password": "your_password",
  "ssl": {
    "rejectUnauthorized": false
  }
}
```

### SMTP Email Settings
```json
{
  "host": "smtp.gmail.com",
  "port": 587,
  "secure": false,
  "auth": {
    "user": "your-email@gmail.com",
    "pass": "your-app-password"
  }
}
```

### OpenWeatherMap API
```json
{
  "apiKey": "your_openweathermap_api_key_here"
}
```

### Slack Webhook
```json
{
  "webhookUrl": "https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK"
}
```

## 📊 SQL скрипты для создания таблиц

### Создание базы данных
```sql
-- Создание базы данных
CREATE DATABASE n8n_workflows;

-- Создание пользователя
CREATE USER n8n_user WITH PASSWORD 'your_password';
GRANT ALL PRIVILEGES ON DATABASE n8n_workflows TO n8n_user;
```

### Создание всех таблиц
```sql
-- Подключение к базе данных
\c n8n_workflows;

-- Таблица для обработанных пользователей
CREATE TABLE processed_users (
    id SERIAL PRIMARY KEY,
    full_name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL,
    age INTEGER NOT NULL,
    is_adult BOOLEAN NOT NULL,
    processed_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    message TEXT
);

-- Таблица логов email
CREATE TABLE email_logs (
    id SERIAL PRIMARY KEY,
    email_id VARCHAR(255) UNIQUE NOT NULL,
    to_email VARCHAR(255) NOT NULL,
    from_email VARCHAR(255) NOT NULL,
    subject TEXT NOT NULL,
    priority VARCHAR(50) DEFAULT 'normal',
    sent_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    status VARCHAR(50) DEFAULT 'sent'
);

-- Таблица данных о погоде
CREATE TABLE weather_data (
    id SERIAL PRIMARY KEY,
    data_id VARCHAR(255) UNIQUE NOT NULL,
    city VARCHAR(255) NOT NULL,
    country VARCHAR(3) NOT NULL,
    temperature INTEGER NOT NULL,
    feels_like INTEGER NOT NULL,
    humidity INTEGER NOT NULL,
    pressure INTEGER NOT NULL,
    description TEXT NOT NULL,
    wind_speed DECIMAL(5,2),
    wind_direction INTEGER,
    visibility DECIMAL(5,2),
    cloudiness INTEGER,
    sunrise TIMESTAMP NOT NULL,
    sunset TIMESTAMP NOT NULL,
    temp_category VARCHAR(20) NOT NULL,
    timestamp TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Таблица данных о продажах
CREATE TABLE sales_data (
    id SERIAL PRIMARY KEY,
    data_id VARCHAR(255) UNIQUE NOT NULL,
    customer_name VARCHAR(255) NOT NULL,
    email VARCHAR(255),
    product VARCHAR(255) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    quantity INTEGER NOT NULL,
    total_amount DECIMAL(10,2) NOT NULL,
    order_category VARCHAR(20) NOT NULL,
    date TIMESTAMP NOT NULL,
    processed_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    row_number INTEGER NOT NULL
);

-- Таблица отчетов обработки
CREATE TABLE processing_reports (
    id SERIAL PRIMARY KEY,
    report_id VARCHAR(255) UNIQUE NOT NULL,
    total_records INTEGER NOT NULL,
    total_amount DECIMAL(15,2) NOT NULL,
    average_order DECIMAL(10,2) NOT NULL,
    categories JSONB NOT NULL,
    top_products JSONB NOT NULL,
    processing_time TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Создание индексов для оптимизации
CREATE INDEX idx_processed_users_email ON processed_users(email);
CREATE INDEX idx_email_logs_sent_at ON email_logs(sent_at);
CREATE INDEX idx_weather_data_city ON weather_data(city);
CREATE INDEX idx_weather_data_timestamp ON weather_data(timestamp);
CREATE INDEX idx_sales_data_customer ON sales_data(customer_name);
CREATE INDEX idx_sales_data_date ON sales_data(date);
CREATE INDEX idx_processing_reports_time ON processing_reports(processing_time);
```

## 🌐 Примеры API запросов

### Email Notification Webhook
```bash
# Базовый запрос
curl -X POST http://your-n8n-instance.com/webhook/email-notification \
  -H "Content-Type: application/json" \
  -d '{
    "to": "user@example.com",
    "subject": "Тестовое сообщение",
    "message": "Привет! Это тестовое сообщение.",
    "priority": "normal"
  }'

# Запрос с высоким приоритетом
curl -X POST http://your-n8n-instance.com/webhook/email-notification \
  -H "Content-Type: application/json" \
  -d '{
    "to": "admin@example.com",
    "subject": "Срочное уведомление",
    "message": "Требуется немедленное внимание!",
    "priority": "high",
    "from": "alerts@example.com"
  }'
```

## 📁 Примеры CSV файлов

### Для CSV to Database Processor
```csv
customer_name,email,product,price,quantity,date
Иван Петров,ivan@example.com,Ноутбук,50000,1,2024-01-15
Мария Сидорова,maria@example.com,Мышь,1500,2,2024-01-15
Алексей Козлов,alex@example.com,Клавиатура,3000,1,2024-01-16
Елена Волкова,elena@example.com,Монитор,25000,1,2024-01-16
Дмитрий Соколов,dmitry@example.com,Наушники,5000,3,2024-01-17
```

## 🔧 Docker Compose для тестирования

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: n8n_workflows
      POSTGRES_USER: n8n_user
      POSTGRES_PASSWORD: your_password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql

  n8n:
    image: n8nio/n8n:latest
    ports:
      - "5678:5678"
    environment:
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n_workflows
      - DB_POSTGRESDB_USER=n8n_user
      - DB_POSTGRESDB_PASSWORD=your_password
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=admin123
    depends_on:
      - postgres
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  postgres_data:
  n8n_data:
```

## 📋 Environment Variables

### .env файл для n8n
```bash
# Database
DB_TYPE=postgresdb
DB_POSTGRESDB_HOST=localhost
DB_POSTGRESDB_PORT=5432
DB_POSTGRESDB_DATABASE=n8n_workflows
DB_POSTGRESDB_USER=n8n_user
DB_POSTGRESDB_PASSWORD=your_password

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-app-password

# API Keys
OPENWEATHER_API_KEY=your_openweathermap_api_key
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK

# n8n Settings
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=admin123
N8N_HOST=localhost
N8N_PORT=5678
N8N_PROTOCOL=http
```

## 🚀 Скрипты для быстрого запуска

### start.sh
```bash
#!/bin/bash

# Запуск PostgreSQL
docker run -d \
  --name postgres-n8n \
  -e POSTGRES_DB=n8n_workflows \
  -e POSTGRES_USER=n8n_user \
  -e POSTGRES_PASSWORD=your_password \
  -p 5432:5432 \
  postgres:15

# Ожидание запуска PostgreSQL
sleep 10

# Создание таблиц
psql -h localhost -U n8n_user -d n8n_workflows -f init.sql

# Запуск n8n
docker run -d \
  --name n8n \
  --link postgres-n8n:postgres \
  -e DB_TYPE=postgresdb \
  -e DB_POSTGRESDB_HOST=postgres \
  -e DB_POSTGRESDB_PORT=5432 \
  -e DB_POSTGRESDB_DATABASE=n8n_workflows \
  -e DB_POSTGRESDB_USER=n8n_user \
  -e DB_POSTGRESDB_PASSWORD=your_password \
  -p 5678:5678 \
  n8nio/n8n:latest

echo "n8n доступен по адресу: http://localhost:5678"
```

### stop.sh
```bash
#!/bin/bash

# Остановка и удаление контейнеров
docker stop n8n postgres-n8n
docker rm n8n postgres-n8n

echo "Контейнеры остановлены и удалены"
```

## 📊 Мониторинг и логи

### Проверка статуса workflow
```bash
# Проверка последних выполнений
curl -X GET "http://localhost:5678/api/v1/executions" \
  -H "Authorization: Bearer YOUR_API_TOKEN"

# Проверка статуса конкретного workflow
curl -X GET "http://localhost:5678/api/v1/workflows/WORKFLOW_ID" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

### Логи PostgreSQL
```sql
-- Проверка активности
SELECT * FROM pg_stat_activity WHERE datname = 'n8n_workflows';

-- Статистика таблиц
SELECT 
    schemaname,
    tablename,
    n_tup_ins as inserts,
    n_tup_upd as updates,
    n_tup_del as deletes
FROM pg_stat_user_tables 
WHERE schemaname = 'public';
```

## 🔍 Отладка

### Включение подробных логов
```bash
# В docker-compose.yml добавьте:
environment:
  - N8N_LOG_LEVEL=debug
  - N8N_LOG_OUTPUT=console,file
```

### Проверка подключений
```javascript
// В узле Code добавьте для отладки:
console.log('Database connection:', $credentials.postgres);
console.log('SMTP settings:', $credentials.smtp);
console.log('Input data:', $input.all());
```