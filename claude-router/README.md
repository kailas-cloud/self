# Claude Router

Proxy для маршрутизации Anthropic API запросов к Ollama моделям через [claude-code-router](https://github.com/musistudio/claude-code-router).

## Возможности

- Полная совместимость с Anthropic API
- Маршрутизация запросов к локальным моделям Ollama
- Mapping моделей Claude на модели в кластере
- Аутентификация через Bearer token

## Модели

| Claude Model | Ollama Model | Назначение |
|--------------|--------------|------------|
| claude-3-5-sonnet | deepseek-r1:32b | Reasoning и сложные задачи |
| claude-3-opus | qwen3-coder:30b | Кодинг задачи |
| claude-3-haiku | devstral-small-2:24b | Быстрые ответы |
| claude-3-sonnet | gpt-oss:20b | Альтернативная модель |

## Деплой

```bash
# Сгенерировать токен и добавить в .env
echo 'CLAUDE_ROUTER_TOKEN="sk-your-token-here"' >> ../self/.env

# Задеплоить
just deploy-claude-router

# Проверить статус
kubectl get pods -n llm -l app=claude-router
```

## Использование

```bash
# Тест через curl
curl -X POST https://cr.kailas.cloud/v1/messages \
  -H "x-api-key: sk-your-token-here" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{
    "model": "claude-3-5-sonnet-20241022",
    "max_tokens": 100,
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

## Конфигурация

Конфигурация находится в `configmap.yaml`. Для изменения маршрутизации отредактируйте `Router` и `ModelMapping` секции в ConfigMap.

## Endpoint

- **Внешний**: https://cr.kailas.cloud
- **Внутренний**: http://claude-router.llm.svc.cluster.local
- **Health check**: https://cr.kailas.cloud/health

## Архитектура

```
Claude Code / API Client
         ↓
    cr.kailas.cloud (Ingress + TLS)
         ↓
   claude-router Service
         ↓
   claude-router Pod (Bun + @musistudio/claude-code-router)
         ↓
   ollama.llm.svc.cluster.local
         ↓
   Ollama StatefulSet (Models: deepseek-r1, qwen3-coder, devstral, gpt-oss)
```
