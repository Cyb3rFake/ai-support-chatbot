# AI Support Chatbot - Русское руководство

## Описание проекта

Этот проект демонстрирует создание приложения-чатбота на базе искусственного интеллекта (LLM), который может использоваться для автоматизации поддержки клиентов, обработки вопросов и предоставления информации.

### Ключевые возможности:
- **FastAPI сервер** - быстрый и современный веб-фреймворк для Python
- **LLM интеграция** - использование моделей языка для обработки естественного языка
- **Docker контейнеризация** - легкое развертывание на любой системе
- **Production-ready** - готово к использованию на серверах

---

## Требования к системе

### Обязательное:
- Python 3.10+
- Docker и Docker Compose (для контейнеризации)
- Интернет-соединение (для загрузки моделей)

### Опционально:
- Linux/WSL (для лучшей совместимости)
- 4GB+ RAM
- GPU NVIDIA (для ускорения моделей)

---

## Установка и запуск

### Способ 1: Запуск через Docker Compose (РЕКОМЕНДУЕТСЯ)

Это самый простой способ, так как не требует установки Python зависимостей.

**Шаг 1: Клонируем репозиторий**
```bash
git clone https://github.com/Cyb3rFake/ai-support-chatbot.git
cd ai-support-chatbot
```

**Шаг 2: Запускаем контейнер**
```bash
docker-compose up -d
```

Флаг `-d` означает запуск в фоновом режиме. Если хотите видеть логи в реальном времени, уберите этот флаг.

**Шаг 3: Проверяем, что приложение запустилось**
```bash
docker ps
```
Вы должны увидеть работающий контейнер с именем `ai-support-chatbot`.

**Шаг 4: Открываем приложение**
- API будет доступно по адресу: http://localhost:8000
- Interactive API документация (Swagger UI): http://localhost:8000/docs

### Способ 2: Локальная установка (для разработки)

Этот способ подходит если вы хотите модифицировать код.

**Шаг 1: Клонируем репозиторий**
```bash
git clone https://github.com/Cyb3rFake/ai-support-chatbot.git
cd ai-support-chatbot
```

**Шаг 2: Создаем виртуальное окружение**
```bash
# Для Windows
python -m venv venv
venv\\Scripts\\activate

# Для Linux/Mac
python3 -m venv venv
source venv/bin/activate
```

После этой команды в терминале должно появиться `(venv)` в начале строки.

**Шаг 3: Устанавливаем зависимости**
```bash
pip install -r requirements.txt
```

Эта команда устанавливает все необходимые библиотеки из файла `requirements.txt`:
- `fastapi` - веб-фреймворк
- `uvicorn` - ASGI сервер для запуска FastAPI
- `python-dotenv` - для работы с переменными окружения
- `requests` - для HTTP запросов к LLM API

**Шаг 4: Запускаем сервер**
```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

`--reload` автоматически перезагружает сервер при изменении кода.

**Шаг 5: Открываем приложение**
- API: http://localhost:8000
- Swagger UI: http://localhost:8000/docs

---

## Объяснение кода

### Структура файлов
```
ai-support-chatbot/
├── main.py              # Основной файл приложения
├── requirements.txt      # Список зависимостей Python
├── .gitignore           # Файлы, исключаемые из Git
├── Dockerfile           # Конфигурация Docker образа
├── docker-compose.yml   # Конфигурация для запуска контейнеров
└── README_RU.md         # Это файл - русское руководство
```

### main.py - основной файл приложения

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
import uvicorn

# Создаем FastAPI приложение
app = FastAPI(
    title="AI Support Chatbot",
    description="Чатбот для автоматизированной поддержки клиентов",
    version="1.0.0"
)

# Разрешаем CORS (кроссдоменные запросы)
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

**Что это делает?**
- `FastAPI()` - создает новое веб-приложение
- `CORSMiddleware` - разрешает запросы с других сайтов

```python
@app.get("/")
def read_root():
    return {"message": "AI Support Chatbot is running"}
```

**Что это делает?**
- `@app.get("/")` - создает GET маршрут на главной странице
- При обращении к http://localhost:8000/ вернется JSON сообщение

```python
@app.post("/chat")
def chat(message: str):
    # Обработка сообщения
    response = process_message(message)
    return {"response": response}
```

**Что это делает?**
- `@app.post("/chat")` - создает POST маршрут для отправки сообщений
- `message: str` - ожидает текстовое сообщение
- Возвращает ответ от модели

### requirements.txt - зависимости

```
fastapi==0.104.1
uvicorn[standard]==0.24.0
python-dotenv==1.0.0
requests==2.31.0
```

**Что это значит?**
- Каждая строка - это название пакета и его версия
- `fastapi==0.104.1` - устанавливает точно версию 0.104.1
- При выполнении `pip install -r requirements.txt` устанавливаются все пакеты

### Dockerfile - контейнеризация

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0"]
```

**Пошагово:**
1. `FROM python:3.11-slim` - берем образ Python 3.11 (slim версия - легче)
2. `WORKDIR /app` - устанавливаем рабочую директорию
3. `COPY requirements.txt .` - копируем файл зависимостей
4. `RUN pip install -r requirements.txt` - устанавливаем зависимости
5. `COPY . .` - копируем весь код приложения
6. `EXPOSE 8000` - открываем порт 8000
7. `CMD` - команда для запуска приложения при старте контейнера

### docker-compose.yml - оркестрация контейнеров

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - PYTHONUNBUFFERED=1
```

**Что это делает?**
- `services:` - список контейнеров
- `app:` - имя сервиса
- `build: .` - собирает образ из Dockerfile в текущей директории
- `ports: - "8000:8000"` - пробрасывает порт 8000 с контейнера на хост
- `PYTHONUNBUFFERED=1` - выводит логи Python в реальном времени

---

## Использование API

### Через Swagger UI (визуальный интерфейс)

1. Откройте http://localhost:8000/docs
2. Вы увидите интерактивную документацию API
3. Нажмите "Try it out" на нужном endpoint'е
4. Заполните параметры
5. Нажмите "Execute"

### Через curl (командная строка)

**Проверить, что сервер работает:**
```bash
curl http://localhost:8000/
```

**Отправить сообщение чатботу:**
```bash
curl -X POST http://localhost:8000/chat?message="Привет, как дела?"
```

### Через Python код

```python
import requests

# Отправляем запрос
response = requests.post(
    "http://localhost:8000/chat",
    params={"message": "Привет!"}
)

# Выводим ответ
print(response.json())
```

---

## Остановка приложения

### Если запущено через Docker Compose
```bash
# Остановить контейнер
docker-compose down

# Остановить и удалить все данные
docker-compose down -v
```

### Если запущено локально
В терминале нажмите: `Ctrl + C`

---

## Решение проблем

### "Port 8000 is already in use"
Порт 8000 занят другим приложением. Решение:
```bash
# Измените порт в docker-compose.yml:
# ports:
#   - "8001:8000"  # используем 8001 вместо 8000

# Или убедитесь что предыдущий контейнер остановлен:
docker ps
docker stop <container_id>
```

### "ModuleNotFoundError: No module named 'fastapi'"
Зависимости не установлены. Решение:
```bash
pip install -r requirements.txt
```

### "Connection refused" при обращении к API
Сервер не запустился. Проверьте логи:
```bash
# Для Docker Compose:
docker-compose logs

# Или запустите без флага -d чтобы видеть логи:
docker-compose up
```

---

## Развертывание на сервер

Для production используйте:
1. **Gunicorn** вместо Uvicorn для большей стабильности
2. **Nginx** как reverse proxy
3. **SSL сертификаты** для https
4. **PM2** или **systemd** для управления процессами

Пример systemd сервиса:
```ini
[Unit]
Description=AI Support Chatbot
After=network.target

[Service]
Type=simple
User=www-data
WorkingDirectory=/home/user/ai-support-chatbot
ExecStart=/usr/bin/python3 -m uvicorn main:app --port 8000
Restart=always

[Install]
WantedBy=multi-user.target
```

---

## Технологии

- **FastAPI** - веб-фреймворк
- **Python 3.10+** - язык программирования
- **Docker** - контейнеризация
- **Uvicorn** - ASGI сервер
- **CORS** - безопасность для кроссдоменных запросов

---

## Лицензия

MIT License - используйте свободно в своих проектах

---

## Контакт

Гитхаб профиль: https://github.com/Cyb3rFake

---

## Дополнительные ресурсы

- [FastAPI документация](https://fastapi.tiangolo.com/)
- [Docker для начинающих](https://docs.docker.com/get-started/)
- [REST API принципы](https://restfulapi.net/)
- [Python Virtual Environments](https://docs.python.org/3/tutorial/venv.html)
