# Telegram Brand Mention Monitoring Agent

Агент на Dify, который отслеживает упоминания бренда в Telegram-чатах, оценивает их с помощью LLM и отправляет владельцу уведомление. Сделан во время стажировки в ЮФУ.

## Как это работает

```
Telegram (новое сообщение)
        │
        ▼
Trigger: Message Received            (плагин langgenius/telegram_trigger)
        │
        ▼
Code: разбор сообщения               (чат, автор, текст, дата)
        │
        ▼
If: текст не пустой
        │
        ▼
LLM: оценка упоминания               (Gemini Flash, ответ строго в JSON)
        │   relevant / sentiment / summary
        ▼
Code: разбор JSON-ответа модели
        │
        ▼
If: relevant == true
        │
        ▼
Airtable: запись в таблицу           (плагин chenzeng/airtable)
        │
        ▼
HTTP: уведомление в Telegram         (Bot API, sendMessage)
```

- **Релевантность.** Модель отличает реальное упоминание бренда от случайного совпадения слова.
- **Тональность.** `positive` / `neutral` / `negative`.
- **Краткая суть.** Одно предложение о том, что именно сказано.
- Бренд задаётся переменной окружения `BRAND_KEYWORD` (в примере указан `Valve`).

## Стек

Dify (Workflow), плагины Telegram Trigger и Airtable, Gemini Flash, Python (узлы Code), Telegram Bot API.

## Как запустить у себя

1. Импортируйте `dify/app.yaml` в Dify (Create → Import App). В пакете нет ключей и учётных данных, их нужно настроить заново.
2. Установите недостающие плагины: Telegram Trigger, Airtable, провайдер Gemini.
3. Подключите модель Gemini в настройках провайдера.
4. Создайте бота у @BotFather и подключите токен в Telegram Trigger.
5. В Airtable создайте таблицу с полями `date`, `chat_name`, `author`, `text_message`, `sentiment`, `summary`. В узле `airtable_insert` подставьте свои Base ID и Table ID (вместо `<AIRTABLE_BASE_ID>` и `<AIRTABLE_TABLE_ID>`).
6. В узле «HTTP-запрос» подставьте токен бота (`<TELEGRAM_BOT_TOKEN>`) и свой chat id (`<YOUR_TELEGRAM_CHAT_ID>`).
7. Измените `BRAND_KEYWORD` на нужный бренд.

## Вторая версия
- Отдельный бэкенд (FastAPI + PostgreSQL) как альтернатива Airtable: начат, не завершён.
- Docker

