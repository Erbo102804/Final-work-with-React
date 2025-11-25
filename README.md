# NetVillage - Интернет для села

NetVillage - это современная платформа для предоставления интернет-услуг в сельской местности с интегрированной системой оплаты через Kaspi.

## Возможности

- 📡 Управление тарифными планами
- 💰 Интеграция с Kaspi для приема платежей
- 📦 Система заказов и подписок
- 👤 Веб-интерфейс для клиентов
- 📊 Административная панель с аналитикой
- 🔒 Безопасность и валидация данных
- 🐳 Docker-ready для простого деплоя
- 📝 REST API с полной документацией

## Технологический стек

**Backend:**
- Python 3.12
- Django 4.2
- Django REST Framework 3.14
- PostgreSQL (production) / SQLite (development)
- Gunicorn + Nginx

**Frontend:**
- Vanilla JavaScript
- HTML5/CSS3
- Responsive Design

**Инфраструктура:**
- Docker & Docker Compose
- Nginx для статических файлов и reverse proxy

## Быстрый старт

### Требования

- Python 3.12+
- Docker и Docker Compose (для production)
- PostgreSQL (опционально для development)

### Установка для разработки

1. **Клонируйте репозиторий:**
```bash
git clone <repository-url>
cd NetVillage
```

2. **Создайте виртуальное окружение:**
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
# или
venv\Scripts\activate  # Windows
```

3. **Установите зависимости:**
```bash
pip install -r requirements.txt
```

4. **Настройте переменные окружения:**
```bash
cp .env.example .env
# Отредактируйте .env файл при необходимости
```

5. **Выполните миграции:**
```bash
python manage.py migrate
```

6. **Загрузите тестовые данные:**
```bash
python manage.py loaddata tariffs/fixtures/initial_tariffs.json
```

7. **Создайте суперпользователя:**
```bash
python manage.py createsuperuser
```

8. **Запустите сервер разработки:**
```bash
python manage.py runserver
```

Приложение будет доступно по адресу: http://localhost:8000

### Деплой с Docker

1. **Настройте production переменные:**
```bash
cp .env.production .env
# Отредактируйте .env файл с production данными
```

2. **Запустите приложение:**
```bash
docker-compose up -d
```

3. **Выполните миграции:**
```bash
docker-compose exec web python manage.py migrate
```

4. **Загрузите начальные данные:**
```bash
docker-compose exec web python manage.py loaddata tariffs/fixtures/initial_tariffs.json
```

5. **Создайте суперпользователя:**
```bash
docker-compose exec web python manage.py createsuperuser
```

Приложение будет доступно по адресу: http://localhost

### Автоматический деплой

Используйте скрипт автоматического деплоя:
```bash
./deploy.sh
```

## Структура проекта

```
NetVillage/
├── backend/                 # Django backend приложение
│   ├── settings.py         # Настройки Django
│   ├── urls.py             # URL маршруты
│   └── templates/          # Шаблоны
├── frontend/               # Frontend приложение
│   ├── index.html         # Главная страница
│   ├── styles.css         # Стили
│   └── app.js             # JavaScript логика
├── orders/                # Приложение заказов
│   ├── models.py          # Модели заказов
│   ├── serializers.py     # Сериализаторы
│   └── views.py           # API views
├── payments/              # Приложение платежей
│   ├── models.py          # Модели платежей
│   ├── serializers.py     # Сериализаторы
│   └── views.py           # API views и webhooks
├── tariffs/               # Приложение тарифов
│   ├── models.py          # Модели тарифов
│   ├── serializers.py     # Сериализаторы
│   ├── views.py           # API views
│   └── fixtures/          # Начальные данные
├── Dockerfile             # Docker образ
├── docker-compose.yml     # Docker Compose конфигурация
├── nginx.conf             # Nginx конфигурация
├── gunicorn_config.py     # Gunicorn настройки
├── requirements.txt       # Python зависимости
├── deploy.sh              # Скрипт деплоя
└── README.md              # Документация
```

## API Документация

### Endpoints

#### Тарифы

**GET** `/api/tariffs/`
- Получить список всех активных тарифов
- Response: `[{id, name, speed, price, description, features}]`

#### Заказы

**POST** `/api/orders/`
- Создать новый заказ
- Body:
```json
{
  "tariff_id": 1,
  "customer_name": "Иван Иванов",
  "customer_email": "ivan@example.com",
  "customer_phone": "+77771234567",
  "address": "г. Село, ул. Центральная, д. 1"
}
```

#### Платежи

**POST** `/api/payments/kaspi`
- Создать платеж через Kaspi
- Body:
```json
{
  "order_id": 1
}
```

**POST** `/api/payments/webhook/kaspi`
- Webhook для обработки платежей от Kaspi
- Body:
```json
{
  "paymentId": "payment-uuid",
  "status": "completed",
  "transactionId": "txn_123456"
}
```

**GET** `/api/payments/{id}/status`
- Получить статус платежа

Подробная документация доступна в файле [API_DOCUMENTATION.md](API_DOCUMENTATION.md)

## Админ-панель

Доступна по адресу: `/admin/`

**Возможности:**
- Управление тарифами
- Просмотр заказов
- Отслеживание платежей
- Статистика и аналитика

Dashboard: `/admin/dashboard/`

## Переменные окружения

Основные переменные окружения (см. `.env.example`):

```env
# Django
SECRET_KEY=your-secret-key
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# Database
DB_ENGINE=django.db.backends.postgresql
DB_NAME=netvillage
DB_USER=postgres
DB_PASSWORD=postgres
DB_HOST=localhost
DB_PORT=5432

# CORS
CORS_ALLOWED_ORIGINS=http://localhost:3000

# Kaspi
KASPI_API_KEY=your-api-key
KASPI_MERCHANT_ID=your-merchant-id
KASPI_WEBHOOK_SECRET=your-webhook-secret
```

## Безопасность

⚠️ **Важно для production:**

1. Измените `SECRET_KEY` на случайную строку
2. Установите `DEBUG=False`
3. Настройте `ALLOWED_HOSTS` с вашим доменом
4. Используйте PostgreSQL вместо SQLite
5. Настройте HTTPS с SSL сертификатом
6. Измените пароли базы данных
7. Настройте CORS для вашего домена
8. Включите firewall и ограничьте доступ к портам

## Разработка

### Запуск тестов
```bash
pytest
```

### Создание миграций
```bash
python manage.py makemigrations
python manage.py migrate
```

### Сбор статических файлов
```bash
python manage.py collectstatic
```

### Создание fixtures
```bash
python manage.py dumpdata tariffs --indent 2 > tariffs/fixtures/tariffs.json
```

## Мониторинг и логи

Логи приложения сохраняются в директории `logs/`:
- `django.log` - основные логи Django
- `gunicorn-access.log` - access логи Gunicorn
- `gunicorn-error.log` - error логи Gunicorn

Просмотр логов в Docker:
```bash
docker-compose logs -f web
```

## Поддержка

Для вопросов и предложений создайте issue в репозитории.

## Лицензия

MIT License

## Авторы

NetVillage Team
# Final-work-with-React
