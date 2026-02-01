# ADR: Argo CD архитектура для kailas-cloud

## Status
Accepted

## Контекст
Ранее деплой ресурсов из репозитория `self` выполнялся вручную или через CI (GitHub Actions) с прямым `kubectl apply -k`. Это плохо масштабируется, не даёт единого источника правды, и затрудняет аудит/самовосстановление. Нужен GitOps-подход и разделение ответственности между платформой (cloud abstractions) и приложениями/тенантами.

## Решение
- Использовать Argo CD как GitOps-контроллер для репозитория `self`.
- Bootstrap Argo CD хранится в `self/argo/install` и устанавливается вручную через `kubectl apply -k`.
- Админка Argo CD доступна по `https://argo.kailas.cloud`, TLS терминируется на ingress (Traefik), `argocd-server` работает с `server.insecure: "true"`.
- Вводится AppProject `kailas` (namespace `argocd`) с ограничениями по репозиториям и namespace. Проект предназначен для cloud/platform слоёв.
- В проекте `kailas` создаются два приложения:
  - `self` — агрегирует platform/infra (кроме k3s bootstrap) через `self/argo/self/kustomization.yaml`.
  - `memory` — отдельная application-абстракция в namespace `memory`.
- k3s bootstrap и низкоуровневый infra (`self/infra/k3s`) остаётся вне Argo CD.

## Последствия
- Деплой `self` больше не выполняется через GitHub Actions; состояние кластера управляется Argo CD.
- Любые изменения platform/infra (кроме k3s) должны попадать в `self/argo/self/kustomization.yaml`.
- Для новых cluster-scoped ресурсов потребуется обновление `AppProject` (`clusterResourceWhitelist`).
- Тенантские приложения из отдельных репозиториев будут оформляться как отдельные AppProject и Application (вне `kailas`).
- Первичная установка Argo CD остаётся ручной операцией, после чего всё управляется через GitOps.
