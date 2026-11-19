
# Stage 1: Build application dependencies
FROM python:3.13-alpine AS builder

WORKDIR /build

# Create virtual environment
RUN python -m venv /venv

# Install application dependencies
COPY app/requirements.txt .

RUN /venv/bin/pip install --no-cache-dir -r requirements.txt

# Remove pip from the virtual environment
RUN rm -rf \
    /venv/lib/python3.13/site-packages/pip \
    /venv/lib/python3.13/site-packages/pip-*.dist-info \
    /venv/bin/pip \
    /venv/bin/pip3 \
    /venv/bin/pip3.13


# Stage 2: Runtime image
FROM python:3.13-alpine

WORKDIR /app

# Remove pip bundled in the base runtime image
RUN rm -rf \
    /usr/local/lib/python3.13/site-packages/pip \
    /usr/local/lib/python3.13/site-packages/pip-*.dist-info \
    /usr/local/bin/pip \
    /usr/local/bin/pip3 \
    /usr/local/bin/pip3.13

# Copy application environment and source code
COPY --from=builder /venv /venv
COPY app/ .

# Runtime environment
ENV PATH="/venv/bin:$PATH"
ENV PYTHONUNBUFFERED=1

# Create non-root application user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup \
    && chown -R appuser:appgroup /app /venv

USER appuser

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
