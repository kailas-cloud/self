# ADR: Official valkey-bundle + valkey.conf entrypoint for self/shared/valkey

## Status
Accepted

## Контекст
В `self/shared/valkey` оператор запускает `valkey-server /valkey/etc/valkey.conf`, поэтому все модули и их аргументы берутся из `valkey.conf`, который формируется из `spec.modules` в Valkey CR. Отдельный Python entrypoint и переменные `*_MODULE_ARGS` из кастомного valkey-bundle больше не нужны для работы: valkey-server сам читает конфиг.

## Решение
- Перейти на официальный образ `valkey/valkey-bundle:9.0.0-alpine` для `self/shared/valkey`.
- Считать кастомный `self/shared/valkey/bundle` устаревшим (legacy) для `self/shared/valkey`; модульные аргументы задаются только через `spec.modules` в CR и попадают в `valkey.conf`.

## Последствия
- Больше не поддерживаем `*_MODULE_ARGS` и кастомный entrypoint для `self/shared/valkey`.
- Документация и тесты по кастомному bundle остаются как legacy-материалы и не являются источником правды для shared-инстанса.
- Все настройки модулей (включая coordinator) должны задаваться через `spec.modules` и конфиг.
