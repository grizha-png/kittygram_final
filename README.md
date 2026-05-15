# Kittygram

`Kittygram` — это сервис для публикации карточек с котиками. Пользователи могут просматривать котов, загружать изображения, работать с API и использовать административную панель Django.

## Возможности

- публикация и просмотр карточек котов;
- загрузка пользовательских изображений;
- работа с API по пути `/api/`;
- административная панель Django по пути `/admin/`;
- запуск в Docker-контейнерах;
- CI/CD через GitHub Actions с публикацией образов на Docker Hub.

## Технологии

- Python 3.9
- Django 3.2
- Django REST Framework
- Djoser
- PostgreSQL 13
- Gunicorn
- Nginx
- React
- Docker и Docker Compose
- GitHub Actions

## Структура контейнеров

- `kittygram_backend` → контейнер `backend`
- `kittygram_frontend` → контейнер `frontend`
- `kittygram_gateway` → контейнер `gateway`
- `postgres:13` → контейнер `db`

## Переменные окружения

Создайте в корне проекта файл `.env` и заполните его по примеру из `.env.example`.

Обязательные переменные:

```env
POSTGRES_DB=kittygram
POSTGRES_USER=kittygram_user
POSTGRES_PASSWORD=kittygram_password
DB_NAME=kittygram
DB_HOST=db
DB_PORT=5432
SECRET_KEY=django-insecure-change-me
DEBUG=False
ALLOWED_HOSTS=localhost,127.0.0.1
TIME_ZONE=Europe/Moscow
DOCKERHUB_USERNAME=your-dockerhub-login
```

## Локальный запуск в контейнерах

1. Соберите и запустите проект:

```bash
docker compose up -d --build
```

2. Проверьте, что контейнеры поднялись:

```bash
docker compose ps
```

3. Проект будет доступен на `http://localhost:9000/`.

## Продакшен-конфигурация

Для сервера подготовлен отдельный файл `docker-compose.production.yml`. Он использует уже собранные образы из Docker Hub и не содержит `build`.

Запуск:

```bash
docker compose -f docker-compose.production.yml up -d
```

## CI/CD

Workflow находится в `.github/workflows/main.yml`.

Что делает пайплайн:

- запускает линтеры `ruff`, `ruff format --check` и `pycodestyle`;
- запускает тесты бэкенда и фронтенда;
- при push в `main` или `master` собирает Docker-образы;
- отправляет образы `kittygram_backend`, `kittygram_frontend`, `kittygram_gateway` в Docker Hub;
- после успешной публикации отправляет уведомление в Telegram.

### GitHub Secrets

Для работы CI/CD в репозитории должны быть заданы секреты:

- `DOCKERHUB_USERNAME`
- `DOCKERHUB_PASSWORD`
- `TELEGRAM_TO`
- `TELEGRAM_TOKEN`

## Проверка проекта

Для локальной проверки инфраструктурных тестов заполните `tests.yml`:

```yaml
repo_owner: your-github-login
kittygram_domain: https://your-kittygram-domain
taski_domain: https://your-taski-domain
dockerhub_username: your-dockerhub-login
```

Содержимое workflow дополнительно продублировано в `kittygram_workflow.yml`, как требует автопроверка.
