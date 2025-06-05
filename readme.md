# Docker Project - Momo Store

Полнофункциональное веб-приложение с архитектурой микросервисов, развернутое с помощью Docker и автоматизированной CI/CD через GitHub Actions.

## 📋 Описание проекта

Этот проект представляет собой интернет-магазин "Momo Store" с современной архитектурой:
- **Frontend**: Vue.js приложение с Nginx
- **Backend**: Go API сервер
- **Контейнеризация**: Docker с multi-stage сборкой
- **Оркестрация**: Docker Compose
- **CI/CD**: GitHub Actions для автоматической сборки и деплоя

## 🏗️ Архитектура

```
┌─────────────────┐    ┌─────────────────┐
│    Frontend     │    │     Backend     │
│   Vue.js + Nginx │◄──►│   Go API Server │
│     Port: 80    │    │   Port: 8081    │
└─────────────────┘    └─────────────────┘
```

## 🚀 Быстрый старт

### Предварительные требования

- Docker Engine 20.10+
- Docker Compose 2.0+
- Git

### Локальный запуск

1. **Клонируйте репозиторий:**
   ```bash
   git clone <repository-url>
   cd docker-project
   ```

2. **Создайте файл окружения:**
   ```bash
   echo "DOCKER_USERNAME=ваш_docker_username" > .env
   ```

3. **Запустите приложение:**
   ```bash
   docker-compose up -d
   ```

4. **Проверьте статус:**
   ```bash
   docker-compose ps
   ```

5. **Откройте в браузере:**
   - Frontend: http://localhost
   - Backend API: http://localhost:8081/health

### Остановка приложения

```bash
docker-compose down
```

## 📁 Структура проекта

```
docker-project/
├── backend/
│   ├── Dockerfile
│   ├── go.mod
│   ├── go.sum
│   ├── cmd/
│   │   └── api/
│   │       ├── app/
│   │       │   ├── app.go
│   │       │   ├── controller_*.go
│   │       │   └── middleware.go
│   │       ├── dependencies/
│   │       ├── main.go
│   │       └── router.go
│   └── internal/
│       ├── logger/
│       └── store/
├── frontend/
│   ├── Dockerfile
│   ├── nginx.conf
│   ├── package.json
│   ├── babel.config.js
│   ├── vue.config.js
│   ├── tsconfig.json
│   ├── public/
│   │   ├── index.html
│   │   └── favicon.ico
│   └── src/
│       ├── App.vue
│       ├── main.ts
│       ├── components/
│       │   ├── cart/
│       │   ├── catalog/
│       │   ├── navbar/
│       │   └── profile/
│       ├── views/
│       ├── router/
│       ├── store/
│       ├── services/
│       └── models/
├── docker-compose.yml
├── .github/workflows/
│   └── deploy.yml
└── readme.md
```

## 🔧 Конфигурация

### Frontend (Vue.js + Nginx)

- **Порт**: 80
- **Технологии**: Vue.js, Nginx 1.27
- **Особенности**:
  - Single Page Application с поддержкой роутинга
  - Проксирование API запросов на backend
  - Кеширование статических ресурсов
  - Заголовки безопасности

### Backend (Go API)

- **Порт**: 8081
- **Технологии**: Go 1.22.7, Alpine Linux
- **Особенности**:
  - RESTful API
  - Health check endpoint: `/health`
  - Статическая компиляция без CGO
  - Минималистичный образ

## 🐳 Docker конфигурация

### Multi-stage сборка

Оба сервиса используют multi-stage сборку для оптимизации размера образов:

**Frontend:**
- Builder stage: Node.js 20 Alpine
- Runtime stage: Nginx 1.27 Alpine

**Backend:**
- Builder stage: Go 1.22.7 Alpine
- Runtime stage: Alpine 3.20

### Безопасность

- Запуск от непривилегированного пользователя
- Read-only файловая система
- Минимальный набор пакетов
- Регулярные обновления безопасности

## 🔄 CI/CD Pipeline

GitHub Actions автоматически выполняет:

1. **Сборка образов** при push в main
2. **Публикация в Docker Hub**
3. **Тестирование с Docker Compose**
4. **Сканирование безопасности** с Trivy
5. **Проверка работоспособности** приложения

### Настройка CI/CD

Добавьте в GitHub Secrets:
- `DOCKER_USER` - логин Docker Hub
- `DOCKER_PASSWORD` - токен доступа Docker Hub

## 📊 Мониторинг и логи

### Health checks

- **Frontend**: `curl -f http://localhost`
- **Backend**: `wget -q --spider http://localhost:8081/health`

### Просмотр логов

```bash
# Логи всех сервисов
docker-compose logs -f

# Логи конкретного сервиса
docker-compose logs -f frontend
docker-compose logs -f backend
```

### Мониторинг ресурсов

```bash
# Использование ресурсов
docker stats

# Информация о контейнерах
docker-compose ps
```

## 🛠️ Разработка

### Локальная разработка

1. **Frontend разработка:**
   ```bash
   cd frontend
   npm install
   npm run serve
   ```

2. **Backend разработка:**
   ```bash
   cd backend
   go mod tidy
   go run cmd/api/*.go
   ```

### Пересборка образов

```bash
# Пересборка с очисткой кеша
docker-compose build --no-cache

# Пересборка конкретного сервиса
docker-compose build frontend
```

## 🔒 Безопасность

- Все контейнеры работают от непривилегированных пользователей
- Настроены заголовки безопасности (CSP, HSTS и др.)
- Регулярное сканирование уязвимостей
- Минимальные образы с обновлениями безопасности

## 🚨 Устранение неполадок

### Проблемы с запуском

1. **Проверьте статус контейнеров:**
   ```bash
   docker-compose ps
   ```

2. **Просмотрите логи:**
   ```bash
   docker-compose logs
   ```

3. **Перезапустите сервисы:**
   ```bash
   docker-compose restart
   ```

### Очистка системы

```bash
# Остановка и удаление контейнеров
docker-compose down -v

# Очистка неиспользуемых ресурсов
docker system prune -f
```

## 📈 Производительность

### Оптимизации

- Кеширование слоев Docker
- Многоплатформенная сборка (AMD64, ARM64)
- Кеширование статических ресурсов
- Оптимизированные образы Alpine

### Ограничения ресурсов

- **Frontend**: 256MB RAM, 0.5 CPU
- **Backend**: 512MB RAM, 0.5 CPU

## 🤝 Участие в разработке

1. Форкните репозиторий
2. Создайте ветку для новой функции
3. Внесите изменения
4. Запустите тесты
5. Создайте Pull Request

## 📄 Лицензия

Этот проект распространяется под лицензией MIT.

## 📞 Поддержка

Если у вас возникли вопросы или проблемы:
- Создайте Issue в GitHub
- Проверьте документацию
- Просмотрите логи приложения