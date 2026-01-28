# cert-manager (platform)

This folder captures the cert-manager setup that already exists in the cluster.

## Install (Helm)

```bash
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm upgrade --install cert-manager jetstack/cert-manager \
  --namespace cert-manager --create-namespace \
  --version v1.19.1 \
  --set installCRDs=true \
  -f values.yaml
```

## Hetzner DNS webhook

```bash
helm repo add vadimkim https://vadimkim.github.io/cert-manager-webhook-hetzner
helm repo update
helm upgrade --install cert-manager-webhook-hetzner vadimkim/cert-manager-webhook-hetzner \
  --namespace cert-manager \
  --version 1.4.0 \
  -f webhook-hetzner-values.yaml
```

## Issuers

```bash
kubectl apply -k self/platform/cert-manager/issuers
```

### Token secret (do not commit)

Create the Hetzner DNS token secret in `cert-manager` namespace:

```bash
kubectl -n cert-manager create secret generic cert-manager-hetzner-token \
  --from-literal=api-token="${HETZNER_DNS_TOKEN}"
```
