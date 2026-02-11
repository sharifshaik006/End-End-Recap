# Helm – Kubernetes Package Management at Scale

Purpose: Understand how Helm works, why it exists, how real teams use it in production, and all major concepts (charts, templates, values, releases, upgrades, rollbacks, dependencies).

---

# 1️⃣ Simple Definition

Helm is a package manager for Kubernetes.

It helps you:

- Template Kubernetes YAML
- Manage application releases
- Upgrade safely
- Roll back versions
- Reuse configurations

Think of Helm like:

apt for Linux  
npm for Node  
but for Kubernetes apps.

---

# 2️⃣ Why Helm Exists

Problem without Helm:

- Too many YAML files
- Environment differences (dev, stage, prod)
- Hardcoded values
- Manual updates
- Repetitive manifests
- No versioned releases

Helm solves:

- YAML templating
- Environment-specific configs
- Release management
- Dependency management
- Reusability

---

# 3️⃣ Core Helm Concepts

---

## Chart

A Helm chart is a package of Kubernetes manifests.

It contains:

- Templates
- Default values
- Metadata

Structure:

my-app/
├── Chart.yaml
├── values.yaml
├── templates/
│ ├── deployment.yaml
│ ├── service.yaml
│ ├── ingress.yaml
│ └── _helpers.tpl
└── charts/


---

## Chart.yaml

Defines metadata:

```yaml
apiVersion: v2
name: my-app
version: 1.0.0
appVersion: "1.2.3"


version → Helm chart version

appVersion → application version

values.yaml

Default configuration file.

Example:

replicaCount: 2

image:
  repository: myrepo/app
  tag: v1.2.0

service:
  type: ClusterIP
  port: 80


Environment overrides use different values files.

Templates

Kubernetes YAML files with placeholders.

Example:

apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
        - name: app
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"


Helm replaces placeholders during install/upgrade.

Release

A deployed instance of a chart.

Example:

helm install my-app ./my-app


Now:

Release name = my-app

Stored in cluster

Can upgrade/rollback

4️⃣ Helm Workflow (Real-World)

Developer updates code

CI builds image

Tag pushed to registry

Helm chart values updated

Helm upgrade executed

Kubernetes rolls out new version

Monitor

5️⃣ Helm Commands (Must Know)

Install:

helm install my-app ./chart


Upgrade:

helm upgrade my-app ./chart


Upgrade with values:

helm upgrade my-app ./chart -f values-prod.yaml


Rollback:

helm rollback my-app 1


List releases:

helm list


Uninstall:

helm uninstall my-app


Dry-run:

helm upgrade my-app ./chart --dry-run --debug


Template render only:

helm template my-app ./chart

6️⃣ Environment Strategy

Use multiple values files:

values-dev.yaml
values-stage.yaml
values-prod.yaml


Deploy:

helm upgrade my-app ./chart -f values-prod.yaml


Never hardcode environment values in templates.

7️⃣ Helm in CI/CD

Pipeline example:

Build Docker image
↓
Push image to ECR
↓
Update values file (image tag)
↓
helm upgrade --install
↓
Deployment rolled out


Advanced:

Use GitOps (ArgoCD) to apply Helm charts automatically.

8️⃣ Helm Dependencies

Charts can depend on other charts.

Example:

dependencies:
  - name: redis
    version: 17.3.0
    repository: https://charts.bitnami.com/bitnami


Run:

helm dependency update


Used for:

Databases

Caching

Monitoring stacks

9️⃣ Template Helpers (_helpers.tpl)

Reusable functions.

Example:

{{- define "my-app.fullname" -}}
{{ .Release.Name }}-{{ .Chart.Name }}
{{- end }}


Prevents duplication.

🔟 Production Issues with Helm

Wrong values file

Overwriting manual changes

Drift between environments

Upgrading without testing

No rollback strategy

Secrets in plain text

Chart version mismatch

1️⃣1️⃣ Best Practices

Separate chart version from app version

Use semantic versioning

Use environment-specific values

Never commit secrets

Use sealed-secrets or external secrets

Use GitOps for production

Pin chart dependencies

Validate templates before deploy

1️⃣2️⃣ Helm vs kubectl apply

kubectl apply:

Direct YAML

No release history

Hard to manage upgrades

Helm:

Templated YAML

Versioned releases

Rollback support

Scalable structure

Helm is preferred at scale.

1️⃣3️⃣ Real-World Enterprise Pattern

Infra provisioned via Terraform
↓
Cluster ready
↓
Helm installs:

ingress controller

monitoring stack

application services
↓
ArgoCD monitors chart repo
↓
GitOps manages drift

1️⃣4️⃣ Troubleshooting Helm

Check release:

helm status my-app


Check history:

helm history my-app


Render YAML:

helm template


Check diff:

helm diff upgrade

1️⃣5️⃣ Quick Cheat Sheet

Chart = package

values.yaml = config

templates = YAML with variables

Release = deployed instance

upgrade = update release

rollback = revert release

dependencies = other charts

--dry-run = safe preview

Helm is how serious teams manage Kubernetes applications at scale.

End of Document


---

Now next level options:

1️⃣ GitOps with Helm (ArgoCD/Flux)  
2️⃣ Advanced Kubernetes (HPA, probes, autoscaling, PDB, affinity, tolerations)  
3️⃣ Kubernetes networking deep dive (CNI, kube-proxy, iptables)  
4️⃣ Production architecture patterns (blue-green, canary, zero-downtime)  
