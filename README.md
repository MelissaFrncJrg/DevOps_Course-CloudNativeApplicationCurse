[![CI](https://github.com/MelissaFrncJrg/DevOps_Course-CloudNativeApplicationCurse/actions/workflows/ci.yml/badge.svg)](https://github.com/MelissaFrncJrg/DevOps_Course-CloudNativeApplicationCurse/actions/workflows/ci.yml)
[![Quality gate](https://sonarcloud.io/api/project_badges/quality_gate?project=MelissaFrncJrg_DevOps_Course-CloudNativeApplicationCurse)](https://sonarcloud.io/summary/new_code?id=MelissaFrncJrg_DevOps_Course-CloudNativeApplicationCurse)

## Git workflow (TP1 + TP2)

- Main branches: `main`, `develop`
- Feature branches: `feature/<name>`
- All new work is pushed on feature branches
- Pull requests are required to merge into `develop`
- No direct commits or pushes should be made to `main` or `develop` (use PRs instead)
- CI (lint, build, test, sonar-backend) and SonarCloud Quality Gate must pass before merging a PR into develop.

## Commit convention

This project uses Conventional Commits enforced by Commitlint.  
Common types:

- `feat: add authentication`
- `fix: fix Postgres connection`
- `chore: update NestJS dependencies`
- `docs: update documentation`
- `refactor: refactor user service`

## Git hooks (Husky)

The following Husky hooks are configured at the repository root:

- `pre-commit`: runs `npm run scan:secrets` (Gitleaks) to block commits that contain hardcoded secrets, and can be extended to run `lint:front` and `lint:back`.
- `commit-msg`: runs Commitlint to ensure commit messages follow the Conventional Commits format.
- `pre-push`: runs `npm run check:prepush` to execute frontend and backend lint commands before pushing.

## CI pipeline (TP2 + TP3)

Complete CI/CD pipeline running on a self-hosted runner:

- **Job `lint`**: runs `npm run lint:front` and `npm run lint:back`.
- **Job `build`**: builds frontend and backend (`npm run build` in each folder).
- **Job `test`**: runs backend tests (`npm test` in `backend`).
- **Job `sonar-backend`**: runs SonarCloud analysis on the backend and checks the Quality Gate.
- **Job `docker`**: builds Docker images, runs healthchecks, and pushes to GitHub Container Registry.

```text
lint ──▶ build ──▶ test ──▶ sonar-backend
         └────▶ docker
```

### Pipeline Requirements

- **Self-hosted runner**: Required for all jobs
- **GitHub Secrets**:
  - `SONAR_TOKEN`: SonarCloud authentication token
  - `VITE_API_BASE_URL`: Frontend API base URL (e.g., `http://localhost:3000/api`)
  - `GITHUB_TOKEN`: Automatically provided by GitHub Actions for GHCR authentication

All jobs must pass before merging into develop.

## Docker (TP3)

### Quick Start with Docker Compose

Launch the entire stack (frontend, backend, postgres) with a single command:

```bash
# Copy environment file
cp .env.example .env

# Start all services
docker compose up --build
```

### Accessible URLs

Once the stack is running:

- **Frontend**: http://localhost:8080
- **Backend API**: http://localhost:3000
- **PostgreSQL**: localhost:5432 (accessible only locally, not exposed to the web)

### Docker Images

Pre-built images are available on GitHub Container Registry:

- **Backend**: [`ghcr.io/melissafrncjrg/cloudnative-backend:latest`](https://github.com/MelissaFrncJrg/DevOps_Course-CloudNativeApplicationCurse/pkgs/container/cloudnative-backend)
- **Frontend**: [`ghcr.io/melissafrncjrg/cloudnative-frontend:latest`](https://github.com/MelissaFrncJrg/DevOps_Course-CloudNativeApplicationCurse/pkgs/container/cloudnative-frontend)

Pull and run:

```bash
# Pull images
docker pull ghcr.io/melissafrncjrg/cloudnative-backend:latest
docker pull ghcr.io/melissafrncjrg/cloudnative-frontend:latest

# Or use docker-compose which pulls automatically
docker compose up
```

### Docker Architecture

The `docker-compose.yml` defines three services:

- **postgres**: PostgreSQL 15 database with health checks
- **backend**: Node.js API (waits for Postgres, runs migrations, then starts)
- **frontend**: Vue.js app served by nginx (waits for Backend)

All services communicate via an internal network (`gym-internal-network`).

### Manual Docker Builds

Build individual images:

```bash
# Backend
docker build -t backend-ci ./backend

# Frontend (requires build argument)
docker build --build-arg VITE_API_BASE_URL=http://localhost:3000/api -t frontend-ci ./frontend
```

For more details, see [DOCKER_SETUP.md](DOCKER_SETUP.md).
