# 📦 Centralized GitHub Actions for DevOps Automation

This repository (`devops-actions-poc`) contains **centralized and reusable GitHub Actions workflows** to standardize CI/CD pipelines across projects in our organization.

All workflows here are reusable via `workflow_call` and are tested in `devops-actions-test`.

---

## 🚀 Available Workflows

### 1. `lint-golangci.yml`
**Purpose**: Lint Go code using `golangci-lint`

- **Inputs:**
  - `go-version` (required): Go version to use (e.g. `1.21`)
  - `lint-version` (optional, default=`v2.0.2`): GolangCI-Lint version

- **Usage Example:**
```yaml
jobs:
  lint:
    uses: your-org/devops-actions-poc/.github/workflows/lint-golangci.yml@main
    with:
      go-version: '1.21'
```

---

### 2. `test-go.yml`
**Purpose**: Run unit tests using `go test`

- **Inputs:**
  - `go-version` (optional with default)

- **Usage:**
```yaml
jobs:
  test:
    uses: your-org/devops-actions-poc/.github/workflows/test-go.yml@main
    with:
      go-version: '1.21'
```

---

### 3. `trivy.yml`
**Purpose**: Run [Trivy](https://github.com/aquasecurity/trivy) for vulnerability scanning

- **Inputs:**
  - `go-version` (optional with default)
  - `trivy-version` (optional with default)

- **Usage:**
```yaml
jobs:
  trivy:
    uses: your-org/devops-actions-poc/.github/workflows/trivy.yml@main
    with:
      go-version: '1.21'
```

---

### 4. `release.yml`
**Purpose**: Handle version tagging, docker image build/push to GCR, and Slack notifications

- **Inputs:**
  - `service_name`: Docker image name (required)
  - `service_title`: Friendly display name (required)
- **Secrets:**
  - `GOOGLE_CREDENTIALS_AUTH`: GCP credentials in JSON
  - `SLACK_WEBHOOK`: Slack webhook for release notification

- **Usage:**
```yaml
jobs:
  release:
    uses: your-org/devops-actions-poc/.github/workflows/release.yml@main
    with:
      service_name: my-service
      service_title: My Go Microservice
    secrets:
      GOOGLE_CREDENTIALS_AUTH: ${{ secrets.GOOGLE_CREDENTIALS_AUTH }}
      SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
```

---

### 5. 🧹 .golangci.yml Linting Configuration
Purpose: Provide a standardized and strict linting policy across all Go services using golangci-lint.

The configuration file .golangci.yml is located in the root of this repository and is used automatically by the lint-golangci.yml reusable workflow.

Linters Enabled:
Includes over 25 linters such as gosec, revive, cyclop, errcheck, wrapcheck, and more.

Custom Settings:
Tweaked thresholds for complexity, duplication, and formatting standards.

Safe Defaults:
Excludes third-party, examples, and generated code to reduce false positives.

🔧 Usage
In the consuming repository (e.g. devops-actions-test), you only need to call the centralized lint workflow:

yaml
Copy
Edit
# .github/workflows/lint.yml
name: Use Central Lint

on:
  push:
    branches: [main]
  pull_request:

jobs:
  lint:
    uses: your-org/devops-actions-poc/.github/workflows/lint-golangci.yml@main
No additional setup is needed if the root .golangci.yml is used from this repo.
```

---

## 🌲 Branching & Release Flow

```text
[feature|bugfix] ➝ devel ➝ [tag created] ➝ main ➝ deployed via Flux2
```

### Triggering a Release:
```bash
git tag v1.2.3
git push origin v1.2.3
```
This triggers the `release.yml`, builds and pushes Docker image, updates charts, and notifies Slack.

---

## ✅ Best Practices
- Always keep your reusable workflows modular and minimal
- Prefer passing inputs with defaults rather than hardcoding
- Use `@main` for testing, then switch to `@vX.Y.Z` tags for production use

---

## 📩 Questions / Improvements
If you'd like to add a new centralized workflow or suggest improvements, open a PR or contact the DevOps team.

