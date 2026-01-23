FROM oven/bun:1.1-alpine

WORKDIR /app

# Install envsubst and claude-code-router
RUN apk add --no-cache gettext && \
    bun install -g @musistudio/claude-code-router

# Create config directory
RUN mkdir -p /root/.claude-code-router

# Expose the default port
EXPOSE 3456

# Start script that substitutes env vars and starts router
CMD ["sh", "-c", "envsubst < /root/.claude-code-router/config.json.template > /root/.claude-code-router/config.json && bunx @musistudio/claude-code-router start"]
