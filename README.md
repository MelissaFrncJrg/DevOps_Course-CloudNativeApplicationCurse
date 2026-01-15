## Git workflow

- Main branches: `main`, `develop`
- Feature branches: `feature/<name>`
- All new work is pushed on feature branches
- Pull requests are required to merge into `develop`
- No direct commits or pushes should be made to `main` or `develop` (use PRs instead)

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
