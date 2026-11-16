# GitSentry Baseline App

**Intentionally weak baseline for comparison.** The application (FastAPI backend and
PostgreSQL) is the same as in `GitSentry-Hardened-Application`; only the pipeline
differs. Do not use this pipeline as a model. See `docs/PLAN.md`, WP5.

The baseline promotes by committing the new image tag to `GitSentry-Baseline-IaC`
with a personal access token and stops there. Nothing is deployed from it.

## Run locally

```sh
cp .env.example .env   # fill in fake values only
docker compose up --build
curl http://localhost:8000/health
```

## Differences from the hardened repo

| Area | Hardened | Baseline |
|------|----------|----------|
| Push protection | on | off |
| `.pre-commit-config.yaml` | Gitleaks hook | absent |
| `.gitleaks.toml` | shared by hook and CI | absent (default rules) |
| `.github/dependabot.yml` | actions, pip, docker | absent |
| Workflow permissions | `permissions: {}` + per-job minimum | `write-all` |
| Job structure | lint-workflows, secret-scan, sast, iac-scan, build-scan, publish, promote | single job, all steps |
| Secrets scope | `SONAR_TOKEN` in `sast` only, App key in `promote` only | AWS keys and `INFRA_PAT` exposed to every step |
| IaC repo auth | GitHub App token, 1 hour, one repo, pull request | classic PAT, direct push |
| Cloud auth | OIDC role (Terraform in IaC repo only) | static AWS access keys |
| Action pinning | full commit SHA | version tags |
| Checkout | `persist-credentials: false` | default (credentials persisted) |
| Secret scanning | Gitleaks full history, before build | Gitleaks current tree only, last step |
| Workflow lint / SAST | zizmor, SonarQube Cloud | none |
| IaC / image scanning | Checkov, Trivy before push | none |
| Scanner install | version and SHA-256 pinned | unverified download |
| Image reference in IaC repo | `@sha256` digest | tag (commit SHA) |
