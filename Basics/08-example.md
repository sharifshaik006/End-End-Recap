# E2E Cloud-Native E-Commerce Example (Buy a Product) + What You’re Missing

This document ties together: **Software, Server, Networking basics, Forward Proxy, Reverse Proxy, Client-Server, Three-Tier, OSI**, and the **CI/CD supply-chain** (Git → Build → Quality → Artifact/Image → Registry → EKS deploy → Monitor).  
It also lists **your current gaps** and what to improve in a real production setup.

---

## 0) The Target System (Simple E-Commerce)

**Use case:** user opens `shop.example.com`, browses products, adds to cart, checks out.

**Architecture:** Three-Tier
- **Presentation tier:** Frontend (React/Next.js) serving UI
- **Application tier:** Backend API (Go or Java) serving `/api/*`
- **Data tier:** Database (PostgreSQL) storing users/products/orders

---

## 1) Where Each Concept Fits

### Software
- Frontend UI code (JS/TS)
- Backend service code (Go/Java)
- Proxy software (Nginx/Envoy/ALB)
- Kubernetes control plane + node agents
- Database engine (Postgres)

### Server
- In AWS: EKS worker nodes (EC2 instances) are “servers”
- DB server could be RDS (managed) or StatefulSet (cluster-managed)

### Client-Server
- Browser (client) → Frontend (server)
- Frontend (client) → Backend API (server)
- Backend (client) → Database (server)

### Reverse Proxy
- ALB / Nginx Ingress sits **in front of servers**
- Routes `/` → frontend, `/api` → backend
- Handles TLS termination, load balancing, routing, rate limiting

### Forward Proxy
- Sits **between your internal systems and the internet**
- Used by CI runners / build nodes / cluster egress for:
  - dependency downloads (Go modules, npm, Maven)
  - base image pulls
  - security feed pulls
  - controlled outbound access (allowlist + audit)

### Networking Basics
- IP identifies a machine
- Port identifies a service (443 HTTPS, 5432 Postgres)
- DNS resolves name → load balancer
- Firewalls/Security Groups control reachability
- Routing/NAT handles private ↔ public connectivity

---

## 2) OSI Mapping (What’s Happening During a Web Request)

User calls `https://shop.example.com`:

- **L7 Application:** HTTP/HTTPS requests, JSON APIs
- **L6 Presentation:** TLS encryption formats
- **L5 Session:** TLS sessions
- **L4 Transport:** TCP (443), connection management
- **L3 Network:** IP routing, subnets
- **L2 Data Link:** Ethernet/Wi-Fi frames
- **L1 Physical:** cable/radio

DevOps debugging most often hits: **L7, L4, L3**.

---

## 3) End-to-End Runtime Flow (User → System → Infra)

### A) Load the website
1. Browser asks **DNS** for `shop.example.com`
2. DNS returns **ALB hostname / IP**
3. Browser connects to **443**
4. Traffic enters **ALB** (reverse proxy / LB)
5. ALB forwards to **Ingress (Nginx)** in EKS
6. Ingress routes `/` → **frontend service**
7. Frontend returns HTML/JS/CSS

### B) Add to cart / checkout
1. Browser calls `POST /api/cart`
2. ALB → Ingress routes `/api` → **backend service**
3. Backend validates request
4. Backend connects to Postgres `:5432`
5. DB responds
6. Backend returns JSON
7. Frontend updates UI

---

## 4) Where Nginx Lives (Reverse Proxy Layer)

Common EKS path:

`Internet → ALB → Nginx Ingress → K8s Service → Pods`

Nginx typically handles:
- path routing (`/api`, `/`)
- TLS (sometimes ALB does TLS)
- load balancing across pods
- rate limiting / headers / basic protections

---

## 5) CI/CD Supply-Chain Flow (Real World)

### The clean lifecycle
1. **Code** in Git (Bitbucket/GitHub)
2. **Build** binary/package
3. **Test** (unit/integration)
4. **Code quality** (SonarQube / lint)
5. **Artifact management** (optional) JFrog/Nexus/CodeArtifact
6. **Docker build** image
7. **Push image** to registry (ECR/GHCR/Docker Hub)
8. **Deploy** to EKS (Helm/ArgoCD/kubectl)
9. **Observe** (logs/metrics/alerts)

### Key definitions
- `pom.xml` / `go.mod` = **build instructions** (stored in Git)
- `.jar` or compiled binary = **artifact output**
- Docker image = **runtime artifact**
- Registry (ECR/GHCR/DockerHub) stores **images**
- JFrog/Nexus stores **many artifact types** (including images, optional)

---

## 6) Where JFrog/Nexus/SonarQube Fit

### SonarQube (Quality Gate)
**After build, before release/publish**
- Blocks bad code from shipping
- Doesn’t store artifacts

### JFrog / Nexus (Artifact Repo)
**After build/test, before (or alongside) image creation**
- Stores compiled outputs, dependencies cache, promotion workflows
- Optional in a pure container-native flow

### Container Registry (ECR/GHCR/DockerHub)
**After Docker build**
- Stores deployable container images

---

## 7) Your Current Pipeline (Go + Docker Hub + K8s Manifest Update)

You have:
- Build Go binary
- Unit tests
- Lint (golangci-lint)
- Build & push Docker image to Docker Hub
- Update k8s manifest with new tag

This is a valid *basic* container-native pipeline.

---

# ✅ Where You’re Lacking (What to Improve Next)

Below are your key gaps based on the questions and the pipeline you shared.

---

## A) Reproducibility & Release Traceability (Major Gap)

### What’s missing
- You tag image with `${{ github.run_id }}` (not portable, not meaningful outside Actions)
- You don’t record immutable **image digest** in deployment
- You’re using `ubuntu-latest` (changes over time → non-reproducible builds)
- Base images in Dockerfile might be `latest` (non-reproducible)

### Why it matters
If Docker Hub deletes an image, “rebuild from source” might not produce the **same** output unless everything is pinned.

### Fixes
- Use tags like:
  - `${{ github.sha }}` (commit SHA)
  - semantic versioning (v1.2.3) for releases
- Pin runner:
  - `ubuntu-22.04`
- Pin base images by version (and ideally digest)

---

## B) GitOps / Deployment Safety (Major Gap)

### What’s missing
- You are force pushing to `main` (`git push ... -f`) from CI
- You update deployment manifests inside the same repo in a way that can overwrite changes
- No environment separation (dev/stage/prod)

### Why it matters
Force push + CI writing to main increases blast radius and can corrupt history.

### Safer alternatives
- Use **GitOps** tool (ArgoCD / Flux) watching a dedicated “deploy” repo or folder
- Or keep manifests versioned and deploy via Helm chart with values override
- If you must commit:
  - open PR from CI instead of pushing to main
  - no force push

---

## C) Security & Secrets Handling (Major Gap)

### What’s missing
- No mention of secrets management pattern (DB creds, registry creds, API keys)
- Docker Hub credentials in GitHub secrets is OK, but for EKS production prefer IAM/ECR
- No mention of RBAC, NetworkPolicies, Pod security context

### Fixes
- For EKS: use **ECR + IAM roles** (IRSA) to avoid long-lived creds
- Use AWS Secrets Manager / External Secrets Operator / sealed-secrets
- Apply least privilege:
  - RBAC for namespaces
  - restrict service accounts
- Add NetworkPolicy for tier isolation

---

## D) Observability & Production Readiness (Gap)

### What’s missing
- No monitoring/alerts step
- No readiness/liveness probes guidance
- No SLO/SLI metrics for latency/error rate

### Fixes
- Add:
  - liveness/readiness probes
  - structured logging
  - Prometheus/Grafana dashboards
  - alerts for 5xx rate, latency, pod crash loops

---

## E) Dependency Control / Proxy Strategy (Optional but Important in Enterprise)

### What’s missing
- No forward proxy / dependency caching approach
- No artifact manager rationale (air-gapped/telecom labs often need this)

### Fix options
- If enterprise / restricted egress:
  - JFrog/Nexus as proxy for Go modules + Docker base images + package mirrors
- If open internet:
  - keep it simple; rely on public repos but pin versions and add SBOM scanning

---

## F) Environment Strategy (Gap)

### What’s missing
- Single pipeline + single branch + single deployment manifest
- No “dev/stage/prod” controls

### Fixes
- Separate environments:
  - different namespaces or clusters
  - different values files (Helm) or overlays (Kustomize)
- Promotion model:
  - build once → promote same image digest across environments

---

# 8) Recommended “Production-Grade” Target Flow (Minimal Changes)

**Best practical path for your setup (Go + EKS):**
- GitHub/Bitbucket stores code
- CI builds **one image**
- Push image to **ECR**
- Deploy via GitOps (ArgoCD) using **image digest** or SHA tag
- Add quality gate + security scans + observability

---

## 9) Failure Modes & Troubleshooting (End-to-End)

### Common failures
1. **DNS fails** → wrong record, TTL issues
2. **LB reachable but 502/504** → backend unhealthy / wrong routes
3. **Pods CrashLoopBackOff** → bad config, missing secret, OOM
4. **DB timeouts** → connection pool, network policy, DB overload
5. **CI fails pulling deps** → proxy/egress issue, repo down

### Troubleshooting flow (always)
1. DNS resolves?
2. LB reachable?
3. Ingress routes correct?
4. Service has endpoints?
5. Pods Ready?
6. App logs show errors?
7. Resources OK?
8. Dependencies reachable (DB)?
9. What changed recently?

---

# 10) Clean E2E Diagram (Runtime + Supply Chain)

## Runtime
User Browser
↓ HTTPS :443
DNS → ALB (Reverse Proxy / LB)
↓
Nginx Ingress (Reverse Proxy)
↓
Service → Frontend Pods
↓ (API calls)
Service → Backend Pods
↓
PostgreSQL (DB)


## CI/CD
Git (source code)
↓
CI Build + Test + Lint
↓
(Option A) Publish artifact to JFrog/Nexus (optional)
↓
Docker build
↓
Push image (ECR/GHCR/DockerHub)
↓
Deploy to EKS (Helm/ArgoCD/kubectl)
↓
Monitoring + Alerts


---

# 11) Quick Cheat Sheet

- **Git** stores source + build configs (`go.mod`, `pom.xml`)
- **SonarQube** checks code quality (blocks bad changes)
- **JFrog/Nexus** stores artifacts and proxies dependencies (optional in pure container-native)
- **Registry** stores container images (ECR best for EKS)
- **Reverse proxy** protects/controls inbound traffic (ALB/Nginx Ingress)
- **Forward proxy** controls outbound traffic (CI + cluster egress)
- **Three-tier** separates UI, logic, data
- **Client-server** is everywhere (browser→api, api→db)
- **Production** needs reproducible builds, safe deploy, secrets, monitoring

--- 

End of Document


