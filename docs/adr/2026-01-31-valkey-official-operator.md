# ADR: Use official hyperspike/valkey-operator

## Status
Accepted

## Контекст
Ранее использовали форк `valkey-operator` с ребрендингом API group на `kailas.cloud` и отдельным CI/CD пайплайном. Для AI-first memory layer важнее совместимость с upstream и скорость обновлений.

## Решение
- Использовать официальный `hyperspike/valkey-operator` (CRD: `hyperspike.io/v1`).
- Устанавливать оператор из официальных релизов (`install.yaml`).
- Форк оператора и связанные пайплайны считаются legacy и не используются для деплоя.

## Последствия
- Все Valkey CR должны использовать `apiVersion: hyperspike.io/v1`.
- CI/CD больше не билдит и не публикует кастомный оператор.
- Документация ссылается на официальный оператор и его CRD.
- Настройка модулей выполняется через custom `valkey.conf` в образе.
