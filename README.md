# kailas-cloud/self

Monorepo for **infra + platform + shared** resources of the Kailas Cloud cluster.
Apps live in **separate repositories** (for example, the `llm` app repo).

## Layout

- `infra/` - cluster bootstrap and IaaS automation (k3s, Hetzner, etc.)
- `platform/` - cluster-level services (monitoring, ingress, operators)
- `platform/cert-manager/` - cert-manager Helm values + issuers (DNS/HTTP)
- `shared/` - shared service templates (valkey/postgres/redis) used per-namespace
- `shared/valkey/` includes operator + bundle repos (nested) and instance manifests
- `clusters/` - ArgoCD entrypoints for the `kailas-cloud` cluster
- `docs/adr/` - architecture decisions

## Shared services model

Shared services are **per-namespace instances** managed by operators.
`self/shared/<svc>/base` defines the CR template (no namespace).
Apps pull the base and apply overlays in their own namespace.

## Quick checks

- Validate manifests:
  - `kubectl apply -k self/ --dry-run=client`
- Monitoring Helm values:
  - `self/platform/monitoring/values.yaml`
- Traefik HelmChartConfig (k3s addon):
  - `self/infra/k3s/manifests/traefik-config.yaml`

## Cluster

Target cluster name: `kailas-cloud`.
