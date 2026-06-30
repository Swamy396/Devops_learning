# ── Stage 1: build deps ──────────────────────────────────────────────────────
FROM python:3.12-slim AS builder

WORKDIR /build

# Install deps into an isolated prefix so the final image copies only them
COPY requirements.txt .
RUN pip install --upgrade pip \
 && pip install --no-cache-dir --prefix=/install -r requirements.txt


# ── Stage 2: runtime ─────────────────────────────────────────────────────────
FROM python:3.12-slim AS runtime

# Non-root user for security
RUN addgroup --system appgroup && adduser --system --ingroup appgroup appuser

WORKDIR /app

# Copy installed packages from builder
COPY --from=builder /install /usr/local

# Copy application source
COPY app/ ./app/

# Switch to non-root user
USER appuser

EXPOSE 8000

# Graceful reload in dev; use --workers N in production behind a proxy
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
