# ADR: GitHub OAuth для Argo CD

## Status
Accepted

## Контекст
Нужен единый способ входа в Argo CD без локальных паролей и ручного управления пользователями. Доступ должен контролироваться членством в GitHub org и быть управляемым через RBAC.

## Решение
- Использовать Dex GitHub connector в Argo CD.
- Разрешить вход только участникам GitHub org `kailas-cloud`.
- Админ‑роль выдаётся группе `kailas-cloud:owners` через RBAC.
- Локальный admin‑логин отключён (`admin.enabled: "false"`).
- OAuth секреты (`clientID`, `clientSecret`) хранятся в `argocd-secret` и берутся из локального `.env` при установке/обновлении.
- Callback URL: `https://argo.kailas.cloud/api/dex/callback`.

## Последствия
- Вход возможен только через GitHub SSO.
- Для смены админ‑группы или расширения доступа нужно обновлять `argocd-rbac-cm`.
- Ротация OAuth секретов требует обновления `argocd-secret` и рестарта `argocd-dex-server`/`argocd-server`.
