set dotenv-load := true

create:
	@echo "==> Creating Kailas cluster..."
	@hetzner-k3s create \
		--config kailas.yaml \
		--hetzner-token $HCLOUD_TOKEN

delete:
	@echo "==> Deleting Kailas cluster..."
	@hetzner-k3s delete \
		--config kailas.yaml \
		--hetzner-token $HCLOUD_TOKEN
