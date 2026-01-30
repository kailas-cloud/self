FROM docker.openhands.dev/openhands/openhands:1.2

USER root

# Install tmux for local runtime; support both Alpine and Debian/Ubuntu bases.
RUN if command -v apk >/dev/null 2>&1; then \
      apk add --no-cache tmux bash ca-certificates git; \
    elif command -v apt-get >/dev/null 2>&1; then \
      apt-get update && apt-get install -y --no-install-recommends tmux bash ca-certificates git && \
      rm -rf /var/lib/apt/lists/*; \
    else \
      echo "Unsupported base image: no apk or apt-get found" && exit 1; \
    fi

RUN chmod 0755 /app/entrypoint.sh
RUN chmod +x /app/entrypoint.sh

USER 1000

