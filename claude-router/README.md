# Claude Router

This directory deploys `musistudio/claude-router` and exposes it via Traefik.

## Required secret

Create the basic auth secret separately (do **not** commit it):

```bash
kubectl -n llm create secret generic claude-router-auth \
  --from-literal=users='token:$(openssl passwd -apr1 <YOUR_TOKEN>)'
```

Update the token as needed and keep it out of Git.
