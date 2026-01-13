Name: Chinomso Daniel  
Time spent (minutes): 90  

What I delivered (1–2 lines):
A production-minded Dockerfile and docker-compose setup for running a FastAPI service securely and reliably using a standard ASGI server configuration.

How to run (commands):
- docker compose build
- docker compose up -d

Assumptions I made:
- The FastAPI application entry point is main.py with `app = FastAPI()`
- A `/health` endpoint exists for container health checks
- No secrets are required at build time

How configuration is handled:
- All runtime configuration is provided via environment variables
- No secrets or credentials are hardcoded into the Docker image
- This follows 12-factor app principles and allows configuration changes without rebuilding the image

How scaling would work:
- The application can be scaled horizontally by running multiple containers
- Gunicorn manages multiple worker processes within each container
- In a real production environment, a load balancer or orchestrator (e.g. Kubernetes) would distribute traffic across replicas

How updates would be deployed without downtime:
- Updates would be deployed using a rolling update strategy
- New containers would be started before old ones are stopped
- Health checks ensure traffic is only routed to healthy instances

One production risk and how to reduce it:
- Risk: Application crash or unresponsive process
- Mitigation: Gunicorn process management, container health checks, and a restart policy ensure automatic recovery

One thing kept intentionally simple and why:
- No external services (database, cache, CI/CD) were included to keep the focus on containerization and deployment fundamentals, as required by the assessment
