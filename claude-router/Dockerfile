FROM oven/bun:1.1-alpine

WORKDIR /app

# Install claude-code-router globally
RUN bun install -g @musistudio/claude-code-router

# Create config directory
RUN mkdir -p /root/.claude-code-router

# Config will be mounted from ConfigMap at /root/.claude-code-router/config.json
# Create a minimal default config
RUN echo '{"Providers":[]}' > /root/.claude-code-router/config.json

# Expose the default port
EXPOSE 3456

# Start the router
CMD ["bunx", "@musistudio/claude-code-router", "start"]
