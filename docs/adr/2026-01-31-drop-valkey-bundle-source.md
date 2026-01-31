# ADR: Drop local valkey-bundle source

## Status
Accepted

## Контекст
Поддержка локального исходника `valkey-bundle` больше не нужна. Memory-layer образ строится поверх опубликованного `ghcr.io/kailas-cloud/valkey-bundle:9.0-coordinator`.

## Решение
- Удалить `self/memory/valkey/bundle` из репозитория.
- Сборка memory-layer образа использует базовый `ghcr.io/kailas-cloud/valkey-bundle:9.0-coordinator` и фиксированный `valkey.conf`.

## Последствия
- Исходники bundle не хранятся в репозитории.
- Обновления bundle приходят только через опубликованный образ.
