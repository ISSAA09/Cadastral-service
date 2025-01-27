# FastAPI Cadastral Service

Этот сервис на базе FastAPI принимает запросы с кадастровым номером, широтой и долготой, эмулирует отправку запроса на внешний сервер, который обрабатывает запрос в течение до 60 секунд и возвращает результат в виде `true` или `false`. Все данные запроса и ответы сохраняются в базе данных PostgreSQL. Также предоставляется API для получения истории запросов.

---

## Структура проекта

- **`main.py`** — основной файл с FastAPI-приложением.
- **`Dockerfile`** — файл для создания Docker-образа.
- **`docker-compose.yml`** — конфигурация для запуска приложения и базы данных с использованием Docker Compose.
- **`requirements.txt`** — файл зависимостей для Python.
- **`alembic/`** — директория для миграций базы данных с использованием Alembic.
- **`models.py`** — файл с моделями данных для работы с PostgreSQL через SQLAlchemy.
- **`schemas.py`** — файл с Pydantic-схемами для валидации входных данных.

---

## Эндпоинты API

1. **`/query`** — принимает кадастровый номер, широту и долготу и эмулирует запрос на внешний сервер. Ответ сохраняется в базе данных.
   - **Метод:** `POST`
   - **Параметры (JSON):**
     ```json
     {
       "cadastral_number": "string",
       "latitude": "float",
       "longitude": "float"
     }
     ```

2. **`/ping`** — проверяет, что сервер запущен.
   - **Метод:** `GET`

3. **`/history`** — возвращает историю всех запросов или историю по конкретному кадастровому номеру.
   - **Метод:** `GET`
   - **Параметры (query string):**
     - `cadastral_number` (необязательный): фильтрация запросов по кадастровому номеру.

4. **`/result`** — эмулирует внешний сервер, который возвращает `true` или `false` (в реальном мире может быть подключен к внешнему серверу).
   - **Метод:** `GET`
   - **Ответ (JSON):**
     ```json
     {
       "result": true
     }
     ```

---

## Стек технологий

- **FastAPI** — для создания асинхронных API.
- **PostgreSQL** — для хранения данных о запросах.
- **SQLAlchemy (async)** — для асинхронной работы с базой данных.
- **Alembic** — для миграций базы данных.
- **Docker** — для контейнеризации приложения.
- **Docker Compose** — для организации многоконтейнерного приложения.

---

## Установка и запуск локально

### 1. Клонируйте репозиторий

Сначала клонируйте репозиторий на локальную машину:
```bash
git clone <url-репозитория>
cd <папка-с-репозиторием>    
```
### 2. Установите зависимости
Создайте виртуальное окружение (рекомендуется)
```
python -m venv venv
source venv/bin/activate  # Для Linux/Mac
venv\Scripts\activate     # Для Windows
```

### 3. Установите необходимые пакеты
```
pip install -r requirements.txt
```

### 4. Настройте базу данных
```
alembic revision --autogenerate -m "Initial migration"
alembic upgrade head
```

### 4. Запустите приложение
```
uvicorn main:app --reload
```
Приложение будет доступно по адресу http://localhost:8000

## Запуск через Docker
### 1. Убедитесь, что Docker и Docker Compose установлены на вашем компьютере

### 2. Сборка Docker-образа
```
docker-compose build
```

### 3. Запустите контейнеры
```
docker-compose up
```
Эта команда создаст два контейнера:
- app: контейнер с приложением FastAPI
- db: контейнер с базой данных PostgreSQL

### 4. Проверьте работу приложения
После запуска приложение будет доступно по адресу http://localhost:8000

## Документация API (Swagger)
FastAPI автоматически генерирует документацию для API с использованием Swagger UI. Для того чтобы подключиться к Swagger UI и ознакомиться с доступными эндпоинтами:

Откройте браузер и перейдите по следующему адресу:

```bash
http://localhost:8000/docs
```
На странице Swagger UI вы увидите интерактивную документацию с возможностью отправлять запросы прямо из интерфейса.

Пример Swagger UI:
* POST /query — Этот эндпоинт принимает кадастровый номер, широту и долготу, и возвращает результат (true или false).
* GET /ping — Этот эндпоинт позволяет проверить, что сервер работает.
* GET /history — Этот эндпоинт возвращает историю всех запросов.
