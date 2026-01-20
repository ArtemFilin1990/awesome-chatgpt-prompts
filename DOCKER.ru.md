# Руководство по развертыванию Docker

Запустите свой собственный экземпляр prompts.chat одной командой.

## Быстрый старт

```bash
docker run -d \
  --name prompts \
  -p 4444:3000 \
  -v prompts-data:/data \
  ghcr.io/f/prompts.chat
```

**Первый запуск:** Контейнер клонирует репозиторий и соберет приложение (~3-5 минут).  
**Последующие запуски:** Запускается немедленно, используя кэшированную сборку.

Откройте http://localhost:4444 в вашем браузере.

## Пользовательский брендинг

Настройте ваш экземпляр с помощью переменных окружения:

```bash
docker run -d \
  --name my-prompts \
  -p 4444:3000 \
  -v prompts-data:/data \
  -e PCHAT_NAME="Acme Промпты" \
  -e PCHAT_DESCRIPTION="Библиотека AI-промптов нашей команды" \
  -e PCHAT_COLOR="#ff6600" \
  -e PCHAT_AUTH_PROVIDERS="github,google" \
  -e PCHAT_LOCALES="ru,en,es" \
  ghcr.io/f/prompts.chat
```

> **Примечание:** Брендинг применяется во время первой сборки. Чтобы изменить брендинг позже, удалите том и запустите заново:
> ```bash
> docker rm -f my-prompts
> docker volume rm prompts-data
> docker run ... # с новыми переменными окружения
> ```

## Переменные конфигурации

Все переменные имеют префикс `PCHAT_` во избежание конфликтов.

#### Брендинг (`branding.*` в prompts.config.ts)

| Переменная окружения | Путь конфигурации | Описание | По умолчанию |
|--------------|-------------|-------------|---------|
| `PCHAT_NAME` | `branding.name` | Название приложения в UI | `My Prompt Library` |
| `PCHAT_DESCRIPTION` | `branding.description` | Описание приложения | `Collect, organize...` |
| `PCHAT_LOGO` | `branding.logo` | Путь к логотипу (в public/) | `/logo.svg` |
| `PCHAT_LOGO_DARK` | `branding.logoDark` | Логотип темного режима | То же, что `PCHAT_LOGO` |
| `PCHAT_FAVICON` | `branding.favicon` | Путь к favicon | `/logo.svg` |

#### Тема (`theme.*` в prompts.config.ts)

| Переменная окружения | Путь конфигурации | Описание | По умолчанию |
|--------------|-------------|-------------|---------|
| `PCHAT_COLOR` | `theme.colors.primary` | Основной цвет (hex) | `#6366f1` |
| `PCHAT_THEME_RADIUS` | `theme.radius` | Радиус границ: `none\|sm\|md\|lg` | `sm` |
| `PCHAT_THEME_VARIANT` | `theme.variant` | Стиль UI: `default\|flat\|brutal` | `default` |
| `PCHAT_THEME_DENSITY` | `theme.density` | Плотность: `compact\|default\|comfortable` | `default` |

#### Аутентификация (`auth.*` в prompts.config.ts)

| Переменная окружения | Путь конфигурации | Описание | По умолчанию |
|--------------|-------------|-------------|---------|
| `PCHAT_AUTH_PROVIDERS` | `auth.providers` | Провайдеры: `github,google,credentials` | `credentials` |
| `PCHAT_ALLOW_REGISTRATION` | `auth.allowRegistration` | Разрешить публичную регистрацию | `true` |

#### Интернационализация (`i18n.*` в prompts.config.ts)

| Переменная окружения | Путь конфигурации | Описание | По умолчанию |
|--------------|-------------|-------------|---------|
| `PCHAT_LOCALES` | `i18n.locales` | Поддерживаемые локали (через запятую) | `en` |
| `PCHAT_DEFAULT_LOCALE` | `i18n.defaultLocale` | Локаль по умолчанию | `en` |

#### Функции (`features.*` в prompts.config.ts)

| Переменная окружения | Путь конфигурации | Описание | По умолчанию |
|--------------|-------------|-------------|---------|
| `PCHAT_FEATURE_PRIVATE_PROMPTS` | `features.privatePrompts` | Включить приватные промпты | `true` |
| `PCHAT_FEATURE_CHANGE_REQUESTS` | `features.changeRequests` | Включить версионирование | `true` |
| `PCHAT_FEATURE_CATEGORIES` | `features.categories` | Включить категории | `true` |
| `PCHAT_FEATURE_TAGS` | `features.tags` | Включить теги | `true` |
| `PCHAT_FEATURE_COMMENTS` | `features.comments` | Включить комментарии | `true` |
| `PCHAT_FEATURE_AI_SEARCH` | `features.aiSearch` | Включить AI-поиск | `false` |
| `PCHAT_FEATURE_AI_GENERATION` | `features.aiGeneration` | Включить AI-генерацию | `false` |
| `PCHAT_FEATURE_MCP` | `features.mcp` | Включить функции MCP | `false` |

## Системные переменные окружения

| Переменная | Описание | По умолчанию |
|----------|-------------|---------|
| `AUTH_SECRET` | Секрет для токенов аутентификации | Авто-генерируется |
| `PORT` | Внутренний порт контейнера | `3000` |
| `DATABASE_URL` | Строка подключения PostgreSQL | Внутренняя БД |

## Настройка для продакшена

Для продакшена установите `AUTH_SECRET` явно:

```bash
docker run -d \
  --name prompts \
  -p 4444:3000 \
  -v prompts-data:/data \
  -e AUTH_SECRET="$(openssl rand -base64 32)" \
  -e PCHAT_NAME="Промпты моей компании" \
  ghcr.io/f/prompts.chat
```

### С OAuth провайдерами

```bash
docker run -d \
  --name prompts \
  -p 4444:3000 \
  -v prompts-data:/data \
  -e AUTH_SECRET="ваш-секретный-ключ" \
  -e PCHAT_AUTH_PROVIDERS="github,google" \
  -e AUTH_GITHUB_ID="ваш-github-client-id" \
  -e AUTH_GITHUB_SECRET="ваш-github-client-secret" \
  -e AUTH_GOOGLE_ID="ваш-google-client-id" \
  -e AUTH_GOOGLE_SECRET="ваш-google-client-secret" \
  ghcr.io/f/prompts.chat
```

### С AI-функциями (OpenAI)

```bash
docker run -d \
  --name prompts \
  -p 4444:3000 \
  -v prompts-data:/data \
  -e PCHAT_FEATURE_AI_SEARCH="true" \
  -e OPENAI_API_KEY="sk-..." \
  ghcr.io/f/prompts.chat
```

## Пользовательский логотип

Смонтируйте ваш файл логотипа:

```bash
docker run -d \
  --name prompts \
  -p 4444:3000 \
  -v prompts-data:/data \
  -v ./my-logo.svg:/data/app/public/logo.svg \
  -e PCHAT_NAME="Мое приложение" \
  ghcr.io/f/prompts.chat
```

## Сохранение данных

### All-in-One образ

Данные хранятся в `/data` внутри контейнера:
- `/data/postgres` - Файлы базы данных PostgreSQL

Смонтируйте том для сохранения данных:

```bash
docker run -d \
  -v prompts-data:/data \
  ghcr.io/f/prompts.chat
```

### Резервное копирование

```bash
# Резервная копия базы данных
docker exec prompts pg_dump -U prompts prompts > backup.sql

# Восстановление базы данных
docker exec -i prompts psql -U prompts prompts < backup.sql
```

## Локальная сборка

Сборка и запуск локально:

```bash
docker build -f docker/Dockerfile -t prompts.chat .
docker run -p 4444:3000 -v prompts-data:/data prompts.chat
```

## Проверка здоровья

Контейнер включает endpoint проверки здоровья:

```bash
curl http://localhost:4444/api/health
```

Ответ:
```json
{
  "status": "healthy",
  "timestamp": "2024-01-01T00:00:00.000Z",
  "database": "connected"
}
```

## Устранение неполадок

### Просмотр логов

```bash
# Все логи
docker logs prompts

# Следовать за логами
docker logs -f prompts

# Логи PostgreSQL (внутри контейнера)
docker exec prompts cat /var/log/supervisor/postgresql.log

# Логи Next.js (внутри контейнера)
docker exec prompts cat /var/log/supervisor/nextjs.log
```

### Доступ к базе данных

```bash
# Подключение к PostgreSQL
docker exec -it prompts psql -U prompts -d prompts

# Выполнение SQL-запроса
docker exec prompts psql -U prompts -d prompts -c "SELECT COUNT(*) FROM \"Prompt\""
```

### Shell контейнера

```bash
docker exec -it prompts bash
```

### Распространенные проблемы

**Контейнер не запускается:**
- Проверьте логи: `docker logs prompts`
- Убедитесь, что порт 4444 доступен: `lsof -i :4444`

**Ошибки подключения к базе данных:**
- Дождитесь инициализации PostgreSQL (может занять 30-60 секунд при первом запуске)
- Проверьте логи базы данных: `docker exec prompts cat /var/log/supervisor/postgresql.log`

**Проблемы с аутентификацией:**
- Убедитесь, что `AUTH_SECRET` установлен для продакшена
- Для OAuth проверьте, что callback URLs настроены правильно

## Требования к ресурсам

Минимум:
- 1 ядро CPU
- 1GB RAM
- 2GB дискового пространства

Рекомендуется:
- 2 ядра CPU
- 2GB RAM
- 10GB дискового пространства

## Обновление

```bash
# Получить последний образ
docker pull ghcr.io/f/prompts.chat

# Остановить и удалить старый контейнер
docker stop prompts && docker rm prompts

# Запустить новый контейнер (данные сохраняются в томе)
docker run -d \
  --name prompts \
  -p 4444:3000 \
  -v prompts-data:/data \
  -e AUTH_SECRET="ваш-секретный-ключ" \
  ghcr.io/f/prompts.chat
```

## Соображения безопасности

1. **Всегда устанавливайте AUTH_SECRET** в продакшене
2. **Используйте HTTPS** - поместите обратный прокси (nginx, Caddy, Traefik) перед контейнером
3. **Ограничьте открытые порты** - открывайте только необходимое
4. **Регулярные обновления** - регулярно получайте последний образ
5. **Резервное копирование данных** - регулярно делайте резервные копии тома `/data`

## Пример: Запуск за Nginx

```nginx
server {
    listen 443 ssl http2;
    server_name prompts.example.com;

    ssl_certificate /etc/letsencrypt/live/prompts.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/prompts.example.com/privkey.pem;

    location / {
        proxy_pass http://localhost:4444;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

## Лицензия

MIT
