# ADR: Restore kailas-cloud/valkey-bundle build

## Status
Accepted

## Контекст
Для memory layer нужен предсказуемый образ с собранными модулями Valkey. Ранее build `kailas-cloud/valkey-bundle` был отключён.

## Решение
- Вернуть CI сборку и публикацию `ghcr.io/kailas-cloud/valkey-bundle`.
- Использовать этот образ как базовый для memory-layer образа.

## Последствия
- CI/CD снова публикует `valkey-bundle` при изменениях в `self/memory/valkey/bundle`.
- Memory-layer образ зависит от актуальности `valkey-bundle`.
