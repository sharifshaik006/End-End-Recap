# CI/CD in Real World + Build Files by Language + GitHub Actions vs Jenkins

Purpose: Understand how CI/CD works end-to-end, what “build files” are for different languages, and how GitHub Actions compares to Jenkins (including webhooks and similarities).

---

## 1) What CI/CD Is (Simple)

CI/CD is an automated pipeline that:
- **CI (Continuous Integration):** builds + tests every change
- **CD (Continuous Delivery/Deployment):** packages and deploys changes safely to environments

Goal:
- Ship changes faster with less risk.

---

## 2) Why CI/CD Exists

Without CI/CD:
- manual builds
- manual deployments
- human mistakes
- inconsistent environments
- slow releases

CI/CD gives:
- repeatable builds
- automated testing
- versioned artifacts
- safer deployments
- clear audit trail

---

## 3) What a CI/CD Pipeline Actually Does (Core Responsibilities)

A “real” pipeline usually covers:

1. **Checkout code**
2. **Install dependencies**
3. **Build**
4. **Test**
5. **Quality checks** (lint, static analysis, security scan)
6. **Package artifact** (jar/binary/wheel)
7. **Build container image** (optional but common)
8. **Push artifact/image to registry**
9. **Deploy**
10. **Verify + monitor**
11. **Rollback if needed**

Key concept:
- Pipeline converts **source code** → **deployable release**.

---

## 4) Build Files by Language (Most Common)

Build file = “instructions for how to build, test, package”.

### Java
- Maven: `pom.xml`
- Gradle: `build.gradle` / `build.gradle.kts`
Output:
- `.jar` / `.war`

### Go
- `go.mod` + `go.sum`
- Build command: `go build`
Output:
- compiled binary

### Node.js / TypeScript
- `package.json` (and lockfile)
  - `package-lock.json` / `yarn.lock` / `pnpm-lock.yaml`
Output:
- build folder (`dist/`) or bundled assets

### Python
- `pyproject.toml` (modern)
- `requirements.txt` (common)
- `setup.py` (older)
Output:
- `.whl` (wheel), `.tar.gz`, or installed package

### .NET
- `.csproj` / `.sln`
Output:
- `.dll` / packaged artifacts

### Rust
- `Cargo.toml`
Output:
- compiled binary

### Ruby
- `Gemfile` + `Gemfile.lock`
Output:
- gem + runtime

### PHP
- `composer.json` + `composer.lock`
Output:
- vendor dependencies + app package

---

## 5) Artifact vs Image (Clear)

### Artifact (build output)
Examples:
- Java `.jar`
- Python `.whl`
- Go binary
Stored in:
- JFrog Artifactory
- Sonatype Nexus
- AWS CodeArtifact
- GitHub Packages (limited)
- GitLab Package Registry

### Container Image (runtime package)
Contains app + runtime libs.
Stored in:
- AWS ECR (best for EKS)
- GHCR
- Docker Hub
- ACR / GAR

In container-native setups:
- The **image becomes the main artifact**.

---

## 6) Typical End-to-End CI/CD Flow (Modern)

Developer PR
↓
CI: Build + Test + Lint + Scan
↓
Build Docker Image
↓
Push Image to Registry (ECR/GHCR)
↓
CD: Deploy to Dev/Staging
↓
Smoke Tests
↓
Promote same image digest to Prod
↓
Monitor + rollback if needed


Best practice:
- **Build once** → promote same immutable image digest across environments.

---

## 7) Tools Used in CI/CD (By Job)

### Source Control / Trigger
- GitHub / Bitbucket / GitLab

### CI Orchestrator
- GitHub Actions
- Jenkins
- GitLab CI
- CircleCI
- Argo Workflows (K8s-native)
- Tekton (K8s-native)

### Quality Gates
- SonarQube (code quality)
- golangci-lint / eslint / flake8 (lint)
- SAST tools (CodeQL, Semgrep)

### Security / Supply Chain
- Trivy (image scanning)
- Snyk (dependencies)
- SBOM (Syft)
- Sigstore/cosign (signing)

### Artifact repositories
- JFrog Artifactory
- Sonatype Nexus
- AWS CodeArtifact

### Container registries
- ECR / GHCR / Docker Hub

### Deployment tools
- kubectl / Helm
- ArgoCD / Flux (GitOps)
- Terraform (infra provisioning)

---

## 8) GitHub Actions vs Jenkins (Reality Check)

### What they have in common (similarities)
Both can:
- run builds/tests
- run scripts
- build Docker images
- push to registries
- deploy to Kubernetes/cloud
- integrate with webhooks
- support secrets

Core similarity:
- They are **pipeline orchestrators**.

---

## 9) Key Differences (How they operate)

### GitHub Actions
- Pipeline-as-code stored in repo: `.github/workflows/*.yml`
- Triggers: `push`, `pull_request`, `workflow_dispatch`, schedules
- Runners: GitHub-hosted or self-hosted
- Tight integration with GitHub PRs, checks, permissions

Best for:
- GitHub-native workflow
- fast setup
- easy PR checks

Tradeoffs:
- more opinionated
- runner limits, concurrency limits depending on plan/org
- complex enterprise patterns sometimes harder than Jenkins

---

### Jenkins
- CI server you manage (self-hosted)
- Pipelines defined in `Jenkinsfile` (Pipeline-as-code) OR UI jobs
- Triggers via webhooks (GitHub/Bitbucket) or polling SCM
- Plugins add huge flexibility (and complexity)

Best for:
- enterprise control
- custom infrastructure/network environments
- complex pipelines, multi-repo orchestration
- restricted networks (telecom labs)

Tradeoffs:
- operational overhead (patching, backups, plugins)
- security hardening required
- plugin drift can break builds

---

## 10) Where Webhooks Fit (GitHub Actions and Jenkins)

### Webhook = “event notification”
Example:
- PR opened
- commit pushed
- tag created

#### GitHub Actions
GitHub itself triggers workflows on events (internally webhook-like).
You define:
- `on: push`
- `on: pull_request`

#### Jenkins
GitHub/Bitbucket sends a webhook to Jenkins:
- “new commit arrived”
Jenkins starts the job.

So:
- GitHub Actions: trigger is built-in to platform
- Jenkins: trigger is often an external webhook to your Jenkins endpoint

---

## 11) Minimal Examples

### A) GitHub Actions workflow (simplified)
```yaml
name: ci

on:
  pull_request:
    branches: [ main ]

jobs:
  build-test:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: "1.22"
      - run: |
          cd src/product-catalog
          go mod download
          go test ./...
          go build -o product-catalog-service main.go

B) Jenkinsfile (simplified)
pipeline {
  agent any
  triggers { githubPush() } // or generic webhook trigger
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build & Test') {
      steps {
        sh '''
          cd src/product-catalog
          go mod download
          go test ./...
          go build -o product-catalog-service main.go
        '''
      }
    }
  }
}

12) Common Beginner Mistakes (CI/CD)

Using latest everywhere (non-reproducible)

Not pinning runner OS (ubuntu-latest drifting)

No quality gates (shipping broken code)

Not storing artifacts/images immutably

Deploying from PR builds to production

No rollback plan

Secrets printed in logs

13) Production-Grade Practices (What you should aim for)

Tag images with git sha and/or version

Promote the same image digest dev → stage → prod

Use environment approvals for prod

Add security scanning (SAST + image scan)

Store artifacts/images in a reliable internal registry (ECR for EKS)

Avoid force-push deployment commits; prefer GitOps (ArgoCD/Flux)

Observability after deploy: smoke checks + metrics + logs

14) Quick Cheat Sheet

Build file tells pipeline how to build:

Java: pom.xml

Go: go.mod

Node: package.json

Python: pyproject.toml

Artifact repo stores build outputs (JFrog/Nexus)

Container registry stores images (ECR/GHCR)

GitHub Actions uses workflows in .github/workflows/

Jenkins uses Jenkinsfile + webhooks

Both are pipeline orchestrators; Jenkins needs more ops work

End of Document


If you want, paste your **exact repo layout** (frontend/backend/k8s folders) and tell me whether you deploy to **EKS** or **on-prem K8s**, and I’ll draft a **production-grade pipeline** that:
- tags by `sha`
- pushes to **ECR**
- deploys via **Helm or ArgoCD**
- adds **SonarQube + Trivy + SBOM**
- avoids force-pushes and reduces blast radius

