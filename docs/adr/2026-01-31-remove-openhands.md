# ADR: OpenHands removal from kailas-cloud

## Status
Rejected / removed

## Контекст
OpenHands не подходит под наши требования для k8s-деплоя. Зафиксированы причины и попытки обхода, чтобы не возвращаться к этому пути.

## Что пробовали
- **Docker-in-Docker (dind)**: требовал privileged, шаткий запуск, проблемы с docker.sock и sandbox-образами.
- **Local runtime**: требовал `tmux`, проблемы с правами на `/app/entrypoint.sh`, конфликт UID/GID, затем ошибки файлового хранилища и `.jwt_secret`.
- **Process runtime**: в нашем образе не поддерживается (Runtime process not supported).
- **Kubernetes runtime**:
  - требовал специфичных env (`RUNTIME=kubernetes`, `KUBERNETES_*`),
  - тянул PVC/Ingress на каждую сессию,
  - упирался в storage class и сетевую конфигурацию,
  - создавал лишнюю инфраструктуру на каждый чат.

## Решение
Полностью удалить OpenHands (meeseeks UI) из кода и кластера.

## Итог
OpenHands вычищен из репозитория и из Kubernetes. Дальше для агентных задач используем другой стек.
