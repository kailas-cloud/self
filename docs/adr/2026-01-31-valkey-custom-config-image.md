# ADR: Custom valkey.conf + entrypoint for memory layer

## Status
Accepted

## Контекст
Официальный `hyperspike/valkey-operator` не применяет кастомные module args из CR для `valkey-search`. Для стабильного запуска с нужными параметрами требуется фиксированный `valkey.conf` и entrypoint в образе.

## Решение
- Собрать memory-layer образ на базе `valkey/valkey-bundle:9.0.0-alpine` с подменой `valkey.conf` и `ENTRYPOINT`.
- Перенести конфигурацию модулей в `self/memory/valkey/valkey.conf`.

## Последствия
- `spec.modules` в Valkey CR больше не используется для настройки модулей в memory layer.
- Модульные аргументы закреплены в образе и управляются через `valkey.conf`.
