# GitOps with Helm (ArgoCD & Flux)

Purpose: Understand GitOps principles, how Helm integrates with GitOps, and how ArgoCD/Flux manage Kubernetes deployments safely at scale.

---

# 1️⃣ Simple Definition

GitOps is a deployment model where:

Git is the single source of truth for your infrastructure and application state.

Instead of running:

kubectl apply  
helm upgrade  

manually,

You push changes to Git,
and a controller (ArgoCD/Flux) automatically syncs the cluster to match Git.

---

# 2️⃣ Why GitOps Exists

Problem with traditional CI/CD:

- CI pushes directly to cluster
- No clear audit trail
- Hard to track cluster state
- Manual changes cause drift
- Risky production access

GitOps solves:

- Declarative cluster state
- Full audit via Git history
- Automatic reconciliation
- Drift detection
- Safer production access

---

# 3️⃣ Core GitOps Principle

Desired State lives in Git.

Controller ensures:

Cluster State = Git State

If someone manually changes cluster,
controller reverts it.

---

# 4️⃣ GitOps Architecture

Developer PR
↓
Merge to Git repo
↓
GitOps controller detects change
↓
Renders Helm chart
↓
Applies to cluster
↓
Continuously monitors drift

---

# 5️⃣ How Helm Fits into GitOps

Helm = templating engine  
ArgoCD/Flux = reconciliation engine

Flow:

Helm chart stored in Git
↓
values.yaml defines environment config
↓
ArgoCD pulls chart
↓
Renders templates
↓
Applies manifests
↓
Monitors for drift

Helm does packaging.
GitOps does enforcement.

---

# 6️⃣ ArgoCD Overview

ArgoCD is a GitOps controller for Kubernetes.

Components:

- Application CRD
- Repo server
- Controller
- UI
- API server

ArgoCD continuously compares:

Git → Live Cluster

---

## Example ArgoCD Application

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
spec:
  source:
    repoURL: https://github.com/org/repo
    targetRevision: main
    path: helm/my-app
  destination:
    server: https://kubernetes.default.svc
    namespace: prod
  syncPolicy:
    automated:
      prune: true
      selfHeal: true


This means:

Watch repo

Auto deploy

Remove deleted resources

Fix drift

7️⃣ Flux Overview

Flux is another GitOps controller.

Works similarly to ArgoCD.

Components:

Source controller

Helm controller

Kustomize controller

Notification controller

Flux is more lightweight and CLI-driven.

8️⃣ Real-World GitOps Workflow

Developer updates app code

CI builds image

Image pushed to registry

CI updates Helm values (image tag)

PR created in GitOps repo

Merge PR

ArgoCD detects change

ArgoCD syncs cluster

Monitor deployment

CI builds.
GitOps deploys.

Separation of concerns.

9️⃣ Repo Structure (Production Pattern)

Common structure:

infra-repo/
├── clusters/
│   ├── dev/
│   ├── prod/
├── apps/
│   ├── my-app/
│   │   ├── values-dev.yaml
│   │   ├── values-prod.yaml


Application repo:

Contains source code

Contains Helm chart

Infra repo:

Contains deployment configuration

🔟 Automated Sync Policies

Options:

Manual sync
→ Human approves deployment

Auto sync
→ Immediate deployment after merge

Enterprise production:

Dev auto

Prod manual approval

1️⃣1️⃣ Drift Detection

If someone runs:

kubectl edit deployment my-app

ArgoCD detects mismatch
and restores original state from Git.

This enforces immutability.

1️⃣2️⃣ Rollback Strategy

Rollback via Git:

git revert commit


Or:

helm rollback


Or:

Switch to previous image tag in values.yaml.

ArgoCD syncs back.

No SSH access required.

1️⃣3️⃣ Security Advantages

No direct cluster access for developers

No kubectl in production laptops

RBAC enforced via Git

Clear audit history

Reduced blast radius

1️⃣4️⃣ Production Risks

Wrong repo targeted

Incorrect environment values

Accidental auto-sync in prod

Secrets in Git

Multiple controllers conflicting

Mitigation:

Strict repo separation

Namespace scoping

External secret management

RBAC policies

Manual sync for prod

1️⃣5️⃣ GitOps vs Traditional CI/CD

Traditional:
CI → kubectl apply → cluster

GitOps:
CI → update Git → controller syncs cluster

Git becomes control plane.

1️⃣6️⃣ Observability in GitOps

Monitor:

ArgoCD sync status

Helm release status

Pod rollout status

Health checks

Alerts

ArgoCD UI shows:

Synced

OutOfSync

Degraded

1️⃣7️⃣ Quick Cheat Sheet

Git = source of truth
Helm = template engine
ArgoCD/Flux = reconciliation engine
Cluster must match Git
Drift auto-corrected
Rollbacks via Git revert
Prod deploy via PR

GitOps reduces operational risk dramatically.

End of Document


---

Now next maturity levels:

1️⃣ Advanced GitOps architecture (multi-cluster strategy)  
2️⃣ ArgoCD security & RBAC design  
3️⃣ Blue/Green & Canary deployments with GitOps  
4️⃣ Advanced Kubernetes production hardening  

Which direction do you want next?
