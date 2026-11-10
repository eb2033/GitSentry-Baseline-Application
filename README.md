# GitSentry Baseline App

**Intentionally weak baseline for comparison.** Application code is byte-identical to
`GitSentry-Hardened-App`; only the pipeline differs. Do not use this pipeline as a model.

## Differences from the hardened repo

| Area | Hardened | Baseline |
|------|----------|----------|
| `.pre-commit-config.yaml` | Gitleaks hook | absent |
| `.gitleaks.toml` | extended default ruleset | absent |
| `sonar-project.properties` | present | absent |
| `.github/CODEOWNERS` | owner on workflows | absent |
| `.github/dependabot.yml` | actions, pip, docker weekly | absent |
| Workflow permissions | `permissions: {}` + per-job minimum | none (broad default token) |
| Job structure | gitleaks, zizmor, sonarqube, build matrix, bump-infra | single job, all steps |
| Secrets scope | per job; SONAR_TOKEN only in sonarqube, app key only in bump-infra | AWS keys and INFRA_PAT exposed at job level |
| Infra auth | short-lived GitHub App token scoped to infra repo | long-lived `INFRA_PAT` |
| Cloud auth | none | static AWS access keys |
| Action pinning | full commit SHA | version tags |
| Checkout | `persist-credentials: false` | default (credentials persisted) |
| Timeouts | `timeout-minutes` on every job | none |
| Secret scanning | Gitleaks full history, first | Gitleaks current tree only, last |
| Workflow lint / SAST | zizmor, SonarQube | none |
| Image scanning | Trivy (secrets, HIGH/CRITICAL) before push | none |
| Install of scanners | pinned, checksum-verified binary | unverified download |
| Image reference in infra | `@sha256` digest | tag (commit SHA) |
