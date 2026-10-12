# VK Launch Params Validator (P9)

Cloudflare Worker для валидации подписи параметров запуска VK Mini Apps.
Закрывает п. 1.2.2 правил VK Mini Apps (ред. 17.03.2026).

## Разовая настройка

### 1. Установить wrangler CLI (Node.js 18+)
    npm install -g wrangler

### 2. Авторизоваться в Cloudflare
    npx wrangler login
    # Откроется браузер → войти в аккаунт Cloudflare → разрешить wrangler.

### 3. Задать APP_SECRET (защищённый ключ приложения)
    cd cloudflare-worker
    npx wrangler secret put APP_SECRET
    # Вставить ключ из VK Console → Ваше приложение → Настройки → Ключи доступа → Защищённый ключ.

### 4. Задеплоить
    npx wrangler deploy
    # В выводе будет URL вида: https://lota-vk-validator.<subdomain>.workers.dev

### 5. Скопировать URL в engine.js
    Найти константу VK_VALIDATOR_URL и вставить URL из шага 4.

## Проверка

### Ручной тест (после деплоя)
    curl -X POST https://lota-vk-validator.<subdomain>.workers.dev/validate \
      -H "Content-Type: application/json" \
      -d '{"query":"vk_user_id=123&vk_ts=1700000000&sign=INVALID"}'
    # Ожидается: {"valid":false,"reason":"sign_mismatch"}

### Реальный тест
    Открыть игру в VK → DevTools Console → должно появиться:
      [LOTA] Launch params validation: OK
    или при подмене sign в URL:
      [LOTA] Launch params validation FAILED: sign_mismatch

## Обновление кода
    npx wrangler deploy

## Обновление APP_SECRET (ротация ключа)
    npx wrangler secret put APP_SECRET