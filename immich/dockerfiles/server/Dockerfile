ARG IMMICH_VERSION=main # Or a specific tag like v1.95.0
ARG NODE_VERSION=18-alpine

FROM node:${NODE_VERSION} AS builder
WORKDIR /app

# Install build tools: git, pnpm (if Immich uses it)
RUN apk add --no-cache git python3 make g++
RUN npm install -g pnpm

# Clone Immich repository
#RUN git clone --depth 1 --branch "v${IMMICH_VERSION}" https://github.com/immich-app/immich.git .
RUN git clone --depth 1 --branch "${IMMICH_VERSION}" https://github.com/immich-app/immich.git .

# Install dependencies and build the server package
# This depends heavily on Immich's monorepo structure and build commands
# Example:
RUN pnpm install --filter @immich/server --prod
RUN pnpm --filter @immich/server run build

# Prune dev dependencies if not done by build
# RUN pnpm --filter @immich/server prune --prod

# Runtime image
FROM node:${NODE_VERSION}
WORKDIR /usr/src/app

# Create a non-root user and group
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --chown=appuser:appgroup --from=builder /app/node_modules ./node_modules
COPY --chown=appuser:appgroup --from=builder /app/packages/server/dist ./dist
COPY --chown=appuser:appgroup --from=builder /app/packages/server/package.json .
# Copy other necessary files like .env.example if needed by startup script

USER appuser

ENV NODE_ENV=production
# Ensure this path is correct for Immich server's entrypoint
CMD ["node", "dist/main.js"]
EXPOSE 3001
