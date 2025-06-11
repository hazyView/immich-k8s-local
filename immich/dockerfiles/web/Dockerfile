ARG IMMICH_VERSION=release
ARG NODE_VERSION=18-alpine

FROM node:${NODE_VERSION} AS builder
WORKDIR /app
RUN apk add --no-cache git
RUN npm install -g pnpm
RUN git clone --depth 1 --branch "v${IMMICH_VERSION}" https://github.com/immich-app/immich.git .
# The web UI might have a different build process, e.g., outputting static files
RUN pnpm install --filter @immich/web --prod
RUN pnpm --filter @immich/web run build

# Serve static files using a lightweight web server like Caddy or Nginx
FROM caddy:2-alpine
WORKDIR /srv
COPY --from=builder /app/packages/web/build . # Adjust path to built static assets
# Basic Caddyfile to serve static files, Immich might need specific proxying for API
# For a simple SPA:
# EXPOSE 80
# CMD ["caddy", "file-server", "--browse"]
# More likely, Immich web needs to be configured to talk to the backend.
# The official Immich web image uses Nginx.
# For simplicity, we'll assume the build produces a 'build' directory.
# This part needs to be aligned with how Immich web is served.
# The official image uses nginx, so let's mimic that.

FROM nginx:1.25-alpine
RUN addgroup -S appgroup && adduser -S appuser -G appgroup --no-create-home
# Remove default Nginx config
RUN rm /etc/nginx/conf.d/default.conf
# Copy custom Nginx config (you'd need to create this based on Immich's requirements)
# COPY nginx.conf /etc/nginx/nginx.conf
COPY --from=builder /app/packages/web/build /usr/share/nginx/html
# Ensure Nginx runs as non-root if possible (requires changes to nginx.conf and permissions)
# USER appuser
EXPOSE 80
# Default Nginx CMD will run
