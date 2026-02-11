# End-to-End DevOps + Cloud-Native Concepts Cheat Sheet
A single reference for **what it is, why it exists, where it’s used, how to use it**, plus **step-by-step workflows** across the concepts we discussed: software → servers → networking → proxies → architectures → Linux → scripting/idempotency → Ansible → databases/programming → CI/CD → artifacts/registries → Git/GitHub/tags/releases → Terraform/state/Vault → containers/Docker → Kubernetes → Helm → GitOps → Cloud → Observability (Prometheus/Grafana/ELK).

---

## 0) The One Big Picture
### What you’re building in real life
A production system is **code + infrastructure + deployment + operations**:

1) **Software (code)**  
2) **Built into artifacts/images**  
3) **Deployed on compute (servers/Kubernetes)**  
4) **Connected by networking + secured by IAM**  
5) **Operated with observability + incident response**

---

# 1) Software
## What it is
Instructions (code) that run on a computer to deliver functionality.

## Why it exists
To automate business logic and deliver outcomes (APIs, UIs, telecom functions).

## Where it’s used
Apps (frontend/backend), infra automation, networking tooling, telecom CNFs/NFs.

## How we use it (workflow)
- Write code → test → build → package → deploy → monitor.

---

# 2) Server
## What it is
A machine (physical or virtual) that provides services over a network.

## Why it exists
Centralized compute to run apps/services reliably for many users.

## Where used
VMs, bare metal, Kubernetes nodes, databases, CI runners.

## How to use (core checks)
- Reachability: `ping`, `traceroute`
- Ports: `ss -tulnp`, `curl`
- Services: `systemctl status <svc>`
- Resources: `top`, `free -m`, `df -h`
- Logs: `journalctl -u <svc>`

---

# 3) Networking Basics
## What it is
Rules + systems that move data between devices.

## Why it exists
To connect clients, servers, clusters, and the internet.

## Where used
Every layer: client↔server, service↔DB, nodes↔control plane.

## How we use it (mental model)
- IP = “which machine”
- Port = “which service”
- DNS = “name → IP”
- Routing/NAT = “how traffic moves”
- Firewall rules = “what traffic is allowed”

### Common production symptoms
- Ping works, port closed → firewall or service not listening  
- SSH works, app not responding → app/service down or unhealthy  
- High latency → CPU/disk/DB/network saturation

---

# 4) Forward Proxy
## What it is
A proxy that sits **in front of clients** (internal users/systems) going out to the internet.

## Why it exists
Control outbound traffic: security, caching, allowlisting, audit, egress control.

## Where used
Enterprises, telecom labs, CI runners, locked-down environments.

## How we use it
- Configure clients/build tools to use proxy (HTTP_PROXY/HTTPS_PROXY)
- Allowlist domains for dependency downloads and image pulls

---

# 5) Reverse Proxy
## What it is
A proxy that sits **in front of servers/apps** handling incoming traffic.

## Why it exists
Routing, TLS termination, load balancing, WAF/rate-limit, stable entrypoint.

## Where used
Nginx/Envoy, ALB/Ingress, API gateways, edge routing.

## How we use it
- Route `/` to frontend, `/api` to backend
- Terminate TLS at LB/Ingress
- Apply headers, auth, rate limit, retries

### Forward vs Reverse (quick)
- Forward proxy: protects **clients** (outbound)
- Reverse proxy: protects **servers** (inbound)

---

# 6) Client–Server Architecture
## What it is
Client requests; server responds.

## Why it exists
A scalable pattern to share services centrally.

## Where used
Browser→API, API→DB, monitoring→nodes, kube-apiserver→kubelet.

## How we use it
Define protocols (HTTP/gRPC), auth, retries, timeouts, load balancing.

---

# 7) Three-Tier Architecture
## What it is
Separation into:
1) Presentation (UI)
2) Application (business logic/API)
3) Data (DB)

## Why it exists
Maintainability, scaling independently, security boundaries.

## Where used
E-commerce, enterprise apps, telecom portals, service management systems.

## How we use it
- UI served via CDN/Ingress
- API deployed as stateless pods
- DB as managed service or StatefulSet + PV

---

# 8) Linux (Production Essentials)
## What it is
Operating system (kernel + userland) that runs most servers/containers.

## Why it exists
Stable multi-user system, networking, process control, security, filesystems.

## Where used
Cloud servers, Kubernetes nodes, telecom network functions, CI runners.

## How we use it (must-know filesystem)
- `/` root
- `/etc` configs
- `/var/log` logs
- `/var/lib` app data (docker, kubelet)
- `/home` users
- `/root` root user home
- `/usr/bin` binaries
- `/opt` third-party apps
- `/proc` process/kernel info

## Key commands
- CPU/Proc: `top`, `ps aux`, `uptime`
- Mem: `free -m`, `vmstat`
- Disk: `df -h`, `du -sh`, `lsblk`
- Net: `ip a`, `ip route`, `ss -tulnp`, `curl`
- Services: `systemctl status`, `journalctl -u <svc>`

## #DNS (Domain Name System) translates human-friendly domain names into IP addresses.
How DNS Works (Step-by-Step Flow)

When you type:

https://app.example.com

Step 1:
Your browser checks local cache.

Step 2:
If not cached, it asks your system resolver.

Step 3:
Resolver asks recursive DNS server (ISP/Cloud/Corporate).

Step 4:
Recursive server queries:
- Root servers
- TLD servers (.com)
- Authoritative nameserver

Step 5:
Authoritative server responds with IP.

Step 6:
Browser connects to returned IP.

This usually happens in milliseconds.
---

# 9) Scripting (Shell/Python) + Idempotency
## What it is
Automation code to enforce desired state.

## Why it exists
Repeatability, speed, reliability, safe retries in CI/ops.

## Where used
Bootstrap servers, CI steps, maintenance tasks, migrations, tooling.

## How we use it (idempotent algorithm)
**Detect → Compare → Act → Validate**
- Check if state already correct
- Change only if needed
- Verify outcome
- Log actions + exit codes

### Shell vs Python
- Shell: OS glue, small scripts, service/file ops
- Python: complex logic, APIs, parsing, maintainable automation

---

# 10) Configuration Management + Ansible
## What it is
Tooling to keep servers consistently configured (packages, users, configs).

## Why it exists
Reduce drift, scale configuration, enforce standards, idempotent changes.

## Where used
VM fleets, jump hosts, monitoring nodes, telecom lab infra, on-prem.

## How we use it
### Core building blocks
- **Inventory**: target hosts/groups
- **Playbook**: YAML plan of tasks
- **Module**: idempotent action (apt, copy, service, user)
- **Role**: reusable structure (tasks, templates, handlers)
- **Handlers**: run only when notified (restart service)
- **Vault**: encrypt secrets

### Must-know commands
- `ansible all -m ping`
- `ansible-inventory --list`
- `ansible-playbook site.yml`
- `ansible-playbook --check --diff`
- `ansible-playbook --limit host1`

### Typical repo structure
- inventories/{dev,prod}/hosts.yml
- group_vars/, host_vars/
- roles/<role>/{tasks,templates,handlers,defaults,vars}
- playbooks/site.yml
- ansible.cfg

---

# 11) Programming + Databases
## Programming
- **What**: logic that defines system behavior
- **Where**: frontend, backend, automation
- **How**: API design, validation, error handling, logging, concurrency

## Database
- **What**: persistent structured storage
- **Why**: concurrency, integrity, queries, transactions
- **Where**: RDS/Cloud SQL, StatefulSet, telecom subscriber data stores
- **How**: schema + indexes + backups + migrations + monitoring

### DB production musts
- Connection pooling
- Indexing (avoid slow queries)
- Backups + restore tests
- Migrations (Flyway/Liquibase/Alembic)
- No public DB exposure

---

# 12) CI/CD
## What it is
Automated pipeline that builds/tests/packages/deploys software.

## Why it exists
Repeatable releases, fast feedback, fewer mistakes, safe deployments.

## Where used
GitHub Actions, Jenkins, GitLab CI, Azure DevOps, etc.

## How we use it (standard stages)
1) Checkout
2) Dependencies
3) Build
4) Unit tests
5) Quality (lint/Sonar)
6) Package artifact/image
7) Push to registry
8) Deploy
9) Verify + monitor
10) Rollback strategy

---

# 13) Build Files (by language)
## What it is
Files that describe **how to build** and dependencies.

## Why
Consistent builds + dependency pinning.

## Common ones
- Java: `pom.xml` (Maven), `build.gradle`
- Go: `go.mod`, `go.sum`
- Node: `package.json` + lockfile
- Python: `pyproject.toml` / `requirements.txt`
- .NET: `.csproj`, `.sln`
- Rust: `Cargo.toml`

---

# 14) Artifacts vs Container Images + Registries
## Artifact
Build output: jar/binary/wheel.
Stored in: **JFrog, Nexus, CodeArtifact**.

## Container image
Runtime package with app+deps.
Stored in: **ECR, GHCR, Docker Hub, ACR, GAR**.

### Real-world rule
Container-native: image becomes primary artifact — but artifact repos still help with:
- dependency caching/proxy
- governance/promotion
- air-gapped environments

---

# 15) Git/GitHub + Branching + Tags/Releases
## What it is
Git tracks history; GitHub manages collaboration + PRs + CI triggers.

## Why it exists
Audit trail, safe collaboration, rollback, release traceability.

## Where used
Every engineering org.

## How we use it (Netflix-style trunk-based)
- `main` always deployable
- short-lived feature branches
- PR + CI checks + review
- merge frequently
- feature flags for incomplete work

### Tags and Versions (real use)
- Tag = immutable marker for a release commit
- Semantic versioning: `vMAJOR.MINOR.PATCH`
- Release pipeline triggers on tags:
  - build image `app:v1.2.0`
  - deploy `v1.2.0`
- Rollback by deploying previous tag

### Collaboration controls
- Teams, roles, branch protection
- Require PR reviews + status checks
- CODEOWNERS for mandatory reviewers

---

# 16) Terraform (IaC) + Modules + State + Remote Backend + Vault
## Terraform
Defines infra as code; plans & applies changes via providers.

## Core files
- `main.tf`: resources/module calls
- `variables.tf`: inputs
- `locals.tf`: computed helpers
- `outputs.tf`: exported values
- `providers.tf`: provider config
- `backend.tf`: remote state backend

## Modules
Reusable infrastructure packages (VPC, EKS, IAM, vSphere VM).

## State (critical)
Terraform “memory” of what it manages. Must be protected.

## Remote state (best practice)
S3+lock table / Terraform Cloud / Azure Storage / GCS:
- collaboration
- locking
- encryption
- versioning

## Vault vs State (important)
- Vault = secrets management (dynamic creds, encryption)
- State = infra record (may accidentally contain secrets if not careful)

## Key commands
- `terraform init`
- `terraform fmt -recursive`
- `terraform validate`
- `terraform plan -var-file=env.tfvars`
- `terraform apply -var-file=env.tfvars`
- `terraform state list`

---

# 17) Containerization
## What it is
Packaging app + dependencies into portable units (containers).

## Why it exists
Consistency, speed, efficiency, scalable deployments.

## Where used
Docker/containerd, Kubernetes, CI pipelines.

## How it works
Image build → push registry → runtime pulls → Linux isolates via namespaces/cgroups.

---

# 18) Docker (Components, Networking, Production)
## Components
- Docker CLI → Docker daemon → containerd → runc → kernel
- Registry stores images

## Networking modes
- Bridge (default)
- User-defined bridge (recommended for DNS by name)
- Host
- None

## Must commands
- `docker build -t app:tag .`
- `docker run -d -p 8080:80 app:tag`
- `docker ps`, `docker logs`, `docker exec -it`
- `docker network ls`, `docker inspect`
- Volumes: `docker volume create`, `-v vol:/path`

## Production issues
- OOM, crashes, image pull errors, port conflicts, disk full from images/logs

---

# 19) Kubernetes (Fundamentals + YAML)
## What it is
Orchestrates containers across nodes with self-healing and scaling.

## Architecture
Control plane: API server, scheduler, controllers, etcd  
Nodes: kubelet, kube-proxy, runtime, CNI

## Core objects (YAML)
- Pod: smallest unit
- Deployment: rolling updates
- ReplicaSet: maintains replicas (Deployment manages it)
- Service: stable network endpoint (ClusterIP/NodePort/LB)
- Ingress: L7 routing (needs controller)
- ConfigMap/Secret: config/secrets
- PVC/PV: persistence

## Labels vs Annotations
- Labels: selection/grouping (services/selectors)
- Annotations: metadata for tooling (ingress rules, monitoring hints)

## Persistence
- Stateful data uses PV/PVC and often StatefulSets or managed DBs.

## Troubleshooting flow
- `kubectl get pods`
- `kubectl describe pod <p>`
- `kubectl logs <p>`
- `kubectl get svc/endpoints`
- `kubectl get events`
- Node: `kubectl describe node`

---

# 20) Helm
## What it is
Kubernetes package manager (templating + release management).

## Why
Manage YAML at scale with environment values + rollback.

## Concepts
- Chart (package)
- values.yaml (config)
- templates (YAML with placeholders)
- release (installed instance)
- dependencies (other charts)
- history/rollback

## Commands
- `helm install`
- `helm upgrade --install -f values-prod.yaml`
- `helm rollback`
- `helm template --debug`

---

# 21) GitOps with Helm (ArgoCD/Flux)
## What it is
Git is source of truth; controller continuously reconciles cluster to match Git.

## Why
Auditability, drift control, safer production (no direct kubectl), easy rollback.

## Where used
Kubernetes production deployments at scale.

## How we use it (pattern)
- CI builds image
- Update Helm values in Git (or image automation)
- ArgoCD/Flux syncs cluster
- Drift auto-healed
- Rollback = git revert

---

# 22) Cloud Fundamentals (AWS/Azure/GCP)
## Core concept categories
Compute, networking, storage, IAM, managed K8s, databases, monitoring, security.

## Service mapping highlights
- VMs: EC2 / Azure VM / GCE
- K8s: EKS / AKS / GKE
- Registry: ECR / ACR / Artifact Registry
- Object storage: S3 / Blob / GCS
- IAM: IAM / AAD+RBAC / IAM
- Monitoring: CloudWatch / Azure Monitor / Cloud Monitoring

---

# 23) Observability (Monitoring + Logging + Tracing)
## Observability vs monitoring/logging
- Monitoring: “Is something wrong?” (alerts, dashboards)
- Logging: “What happened?”
- Tracing: “Where did time go across services?”
- Observability: “Why is it happening?” using metrics+logs+traces (+profiles)

## Golden signals
Latency, traffic, errors, saturation.

---

# 24) Prometheus, Grafana, ELK (Real World)
## Prometheus (metrics)
- Scrapes `/metrics`
- Stores time-series
- Alerting via Alertmanager
- PromQL queries

## Grafana (dashboards)
- Visualizes Prometheus/Elasticsearch/Loki/etc.
- Dashboards + alert views

## ELK (logs)
- Collect logs (FluentBit/Filebeat)
- Store/search in Elasticsearch
- View in Kibana

### Typical K8s stack
- Metrics: Prometheus → Grafana → Alertmanager
- Logs: FluentBit → Elasticsearch → Kibana (or Loki)
- Traces: OpenTelemetry → Jaeger/Tempo (optional)

---

# 25) End-to-End “Buy a Product” Workflow (Full Stack)
1) User enters domain → DNS resolves
2) Traffic hits Load Balancer / Ingress (reverse proxy)
3) Ingress routes to frontend service → frontend pods
4) Frontend calls backend API `/api/...`
5) Ingress routes to backend service → backend pods
6) Backend queries DB (managed or stateful) via service/endpoint
7) Response returns to user
8) Metrics/logs/traces are emitted throughout
9) Alerts trigger if SLOs violated

---

# 26) End-to-End Delivery Workflow (Dev → Prod)
## A) Code path
1) Developer creates feature branch
2) Commit changes + push branch
3) PR opens → CI runs tests/lint/scans
4) Review + merge to main

## B) Release path with tags
5) Create tag `v1.2.0` on main
6) CI builds image `app:v1.2.0` and pushes to registry
7) GitOps updates desired state (values/image tag) in Git
8) ArgoCD/Flux syncs to cluster
9) Observe rollout (health checks, metrics)
10) Rollback by deploying previous version/tag (or git revert)

---

# 27) “How It Fails in Production” Universal Troubleshooting Ladder
Use this order across servers/K8s/cloud:

1) **Reachable?** (DNS, ping, route, LB health)
2) **Port open?** (`ss`, security groups, network policy)
3) **Service running?** (`systemctl`, pod Ready)
4) **Resources OK?** CPU/mem/disk
5) **Dependencies OK?** DB, cache, queues, external APIs
6) **What changed?** latest deploy, config, secret, firewall
7) **Logs/Traces** for root cause
8) **Mitigate** (scale, rollback, feature-flag off, restart)
9) **Prevent** (alerts, runbook, tests, limits, SLO)

---

# 28) Ultra-Short “Quick Recap” (one screen)
- Git: history + PRs + tags/releases
- CI/CD: build/test/package/push/deploy
- Artifact repo: jars/binaries/packages
- Registry: container images
- Terraform: provision infra (+ remote state + locking)
- Vault: secrets (not state)
- Ansible: configure servers (idempotent)
- Docker: build/run containers + networking/volumes
- Kubernetes: orchestrate containers (deploy/service/ingress/pvc)
- Helm: manage K8s YAML at scale
- GitOps: Git as truth, ArgoCD/Flux syncs
- Observability: metrics/logs/traces
- Prometheus/Grafana/ELK: metrics dashboards + centralized logs

---
End of Document
