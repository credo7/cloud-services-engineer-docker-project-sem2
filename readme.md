# Docker Project - Momo Store

Веб-приложение интернет-магазина с микросервисной архитектурой на Docker.

## 🚀 Быстрый запуск

```bash
git clone https://github.com/credo7/cloud-services-engineer-docker-project-sem2.git
cd cloud-services-engineer-docker-project-sem2
docker compose up -d
```


## 🏗️ Архитектура

- **Frontend**: Vue.js + Nginx (порт 80)
- **Backend**: Go API (порт 8081)
- **Оркестрация**: Docker Compose
- **CI/CD**: GitHub Actions

## 📦 Размеры итоговых образов

| Сервис | Размер | Обоснование |
|--------|--------|-------------|
| **Backend** | **28.9MB** | Использование multi-stage сборки с Alpine Linux. Статическая компиляция Go без CGO, минимальный runtime образ содержит только исполняемый файл. |
| **Frontend** | **50MB** | Multi-stage сборка: Node.js для сборки Vue.js приложения, затем копирование статических файлов в легковесный nginx:alpine образ. |

### Оптимизации размера:

- **Multi-stage builds** - сборка в полнофункциональном образе, runtime в минимальном
- **Alpine Linux** - облегченная ОС (5-10MB вместо 100MB+ Ubuntu)
- **Статическая компиляция** - Go binary без внешних зависимостей
- **Минимальные runtime зависимости** - только необходимые пакеты

## 🐳 Docker команды

```bash
# Запуск
docker compose up -d

# Остановка
docker compose down

# Логи
docker compose logs -f

# Статус
docker compose ps
```

## 📁 Структура

```
├── backend/           # Go API сервер
├── frontend/          # Vue.js + Nginx
├── docker-compose.yml # Конфигурация оркестрации
└── .github/workflows/ # CI/CD pipeline
```

## 🔧 CI/CD

GitHub Actions автоматически:
- Собирает Docker образы
- Публикует в Docker Hub  
- Тестирует с Docker Compose

## Health Checks

- Frontend: http://localhost/
- Backend: http://localhost:8081/health