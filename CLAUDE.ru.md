# CLAUDE.md

> Краткая справка для Claude Code при работе с prompts.chat

## Обзор проекта

**prompts.chat** — социальная платформа для AI-промптов, построенная на Next.js 16 App Router, React 19, TypeScript и PostgreSQL/Prisma. Она позволяет пользователям делиться, находить и собирать промпты.

Подробные руководства для агентов см. в <a>AGENTS.md</a>.

## Быстрые команды

```bash
# Разработка
npm run dev              # Запустить dev-сервер на localhost:3000
npm run build            # Продакшн-сборка (запускает prisma generate)
npm run lint             # Запустить ESLint

# База данных
npm run db:migrate       # Запустить миграции Prisma
npm run db:push          # Применить изменения схемы
npm run db:studio        # Открыть Prisma Studio
npm run db:seed          # Заполнить базу данных

# Проверка типов
npx tsc --noEmit         # Проверить типы TypeScript
```

## Ключевые файлы

| Файл | Назначение |
|------|---------|
| `prompts.config.ts` | Основная конфигурация приложения (брендинг, тема, аутентификация, функции) |
| `prisma/schema.prisma` | Схема базы данных |
| `src/lib/auth/index.ts` | Конфигурация NextAuth |
| `src/lib/db.ts` | Singleton клиента Prisma |
| `messages/*.json` | Файлы переводов i18n |

## Структура проекта

```
src/
├── app/              # Страницы Next.js App Router
│   ├── (auth)/       # Вход, регистрация
│   ├── api/          # API маршруты
│   ├── prompts/      # CRUD страницы промптов
│   └── admin/        # Панель администратора
├── components/       # React компоненты
│   ├── ui/           # Базовые компоненты shadcn/ui
│   └── prompts/      # Компоненты, связанные с промптами
└── lib/              # Утилиты и конфигурация
    ├── ai/           # Интеграция с OpenAI
    ├── auth/         # Настройка NextAuth
    └── plugins/      # Плагины аутентификации и хранилища
```

## Паттерны кода

- **Серверные компоненты** по умолчанию, `"use client"` только при необходимости
- **Переводы:** Используйте `useTranslations()` или `getTranslations()` из next-intl
- **Стилизация:** Tailwind CSS с утилитой `cn()` для условных классов
- **Формы:** React Hook Form + Zod валидация
- **База данных:** Клиент Prisma из `@/lib/db`

## Перед коммитом

1. Запустите `npm run lint` для проверки проблем
2. Добавьте переводы для любого пользовательского текста
3. Используйте существующие UI-компоненты из `src/components/ui/`
4. Никогда не коммитьте секреты (используйте `.env`)
