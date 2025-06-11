ARG IMMICH_VERSION=release
ARG NODE_VERSION=18-alpine

FROM node:${NODE_VERSION} AS builder
WORKDIR /app
RUN apk add --no-cache git python3 make g++
RUN npm install -g pnpm
RUN git clone --depth 1 --branch "v${IMMICH_VERSION}" https://github.com/immich-app/immich.git .
RUN pnpm install --filter @immich/microservices --prod
RUN pnpm --filter @immich/microservices run build

FROM node:${NODE_VERSION}
WORKDIR /usr/src/app
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --chown=appuser:appgroup --from=builder /app/node_modules ./node_modules
COPY --chown=appuser:appgroup --from=builder /app/packages/microservices/dist ./dist
COPY --chown=appuser:appgroup --from=builder /app/packages/microservices/package.json .
USER appuser
ENV NODE_ENV=production
CMD ["node", "dist/main.js"]
