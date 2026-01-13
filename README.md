## ============DOCKERFILE =============
```python
FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

 #Install minimal system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

 #Create non-root user
RUN useradd --create-home appuser

 #Copy and install dependencies first (cache-friendly)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

 #Copy application code
COPY . .

 #Set ownership and switch user
RUN chown -R appuser:appuser /app
USER appuser

EXPOSE 8000

 #Production ASGI server
CMD ["gunicorn", "-k", "uvicorn.workers.UvicornWorker", "main:app", "--bind", "0.0.0.0:8000"]
```
## =========DOCKER-COMPOSE.YML ==========
```python
version: "3.9"

services:
  api:
    build: .
    container_name: fastapi_service
    ports:
      - "8000:8000"
    environment:
      - ENV=production
      - LOG_LEVEL=info
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 5s
      retries: 3
```

## =======SUBMISSION.MD=======

**Name: Chinomso Daniel**  
Time spent (minutes): 90  

**What I delivered (1–2 lines):**
A production-minded Dockerfile and docker-compose setup for running a FastAPI service securely and reliably using a standard ASGI server configuration.

**How to run (commands):**
- docker compose build
- docker compose up -d

**Assumptions I made:**
- The FastAPI application entry point is main.py with `app = FastAPI()`
- A `/health` endpoint exists for container health checks
- No secrets are required at build time

**How configuration is handled:**
- All runtime configuration is provided via environment variables
- No secrets or credentials are hardcoded into the Docker image
- This follows 12-factor app principles and allows configuration changes without rebuilding the image

**How scaling would work:**
- The application can be scaled horizontally by running multiple containers
- Gunicorn manages multiple worker processes within each container
- In a real production environment, a load balancer or orchestrator (e.g. Kubernetes) would distribute traffic across replicas

**How updates would be deployed without downtime:**
- Updates would be deployed using a rolling update strategy
- New containers would be started before old ones are stopped
- Health checks ensure traffic is only routed to healthy instances

**One production risk and how to reduce it:**
- Risk: Application crash or unresponsive process
- Mitigation: Gunicorn process management, container health checks, and a restart policy ensure automatic recovery

**One thing kept intentionally simple and why:**
- No external services (database, cache, CI/CD) were included to keep the focus on containerization and deployment fundamentals, as required by the assessment
