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

## CI pipeline (TP2)

Simple CI pipeline running on a self-hosted runner:

- **Job `lint`**: runs `npm run lint:front` and `npm run lint:back`.
- **Job `build`**: builds frontend and backend (`npm run build` in each folder).
- **Job `test`**: runs backend tests (`npm test` in `backend`).
- **Job `sonar-backend`**: runs SonarCloud analysis on the backend and checks the Quality Gate.

```text
lint ──▶ build ──▶ test ──▶ sonar-backend
```
All jobs run on the self-hosted runner (runs-on: self-hosted) and must be green before merging into develop.
