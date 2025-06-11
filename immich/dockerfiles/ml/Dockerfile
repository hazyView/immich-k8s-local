ARG IMMICH_VERSION=release
ARG PYTHON_VERSION=3.10-slim

FROM python:${PYTHON_VERSION} AS builder
WORKDIR /app
RUN apt-get update && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*
# Install poetry or pip for dependencies
RUN pip install poetry
RUN git clone --depth 1 --branch "v${IMMICH_VERSION}" https://github.com/immich-app/immich.git .
# Build/install ML component (example)
WORKDIR /app/machine-learning # Adjust path
RUN poetry config virtualenvs.create false && poetry install --no-dev --no-root

FROM python:${PYTHON_VERSION}
WORKDIR /usr/src/app
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --chown=appuser:appgroup --from=builder /app/machine-learning .
# Copy models or ensure they are downloaded on startup
# ENV TRANSFORMERS_CACHE=/app/models/.cache
# RUN mkdir -p /app/models/.cache && chown -R appuser:appgroup /app/models
USER appuser
ENV PYTHONUNBUFFERED=1
# CMD ["python", "main.py"] # Adjust to actual entrypoint
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "3003"] # Example from official
