# kailas-cloud/self

---

Monorepo for **infra + platform + memory** resources of the Kailas Cloud cluster.
Apps live in **separate repositories** (for example, the `llm` app repo).

## Layout

- `infra/` - cluster bootstrap and IaaS automation (k3s, Hetzner, etc.)
- `platform/` - cluster-level services (monitoring, ingress, operators)
- `platform/cert-manager/` - cert-manager Helm values + issuers (DNS/HTTP)
- `memory/` - memory layer services
- `memory/valkey/` includes Valkey memory layer manifests
- `clusters/` - ArgoCD entrypoints for the `kailas-cloud` cluster
- `docs/adr/` - architecture decisions

## Memory layer model

Valkey runs as part of the **memory layer** under `self/memory/`.
Operator is the official `hyperspike/valkey-operator` (CRD: `hyperspike.io/v1`).

## Quick checks

- Validate manifests:
  - `kubectl apply -k self/ --dry-run=client`
- Monitoring Helm values:
  - `self/platform/monitoring/values.yaml`
- Traefik HelmChartConfig (k3s addon):
  - `self/infra/k3s/manifests/traefik-config.yaml`

## Cluster

Target cluster name: `kailas-cloud`.
