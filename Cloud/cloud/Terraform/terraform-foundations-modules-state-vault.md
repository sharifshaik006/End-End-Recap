# Terraform in Production: Files, Modules, Providers, State, Remote Backend, Vault

Purpose: Understand Terraform end-to-end in a real DevOps workflow: **what it is**, **why it exists**, **how the files fit together**, **modules**, **providers**, **state**, **remote backends**, and **Vault** usage.

---

## 1️⃣ Simple Definition
Terraform is an Infrastructure-as-Code tool that lets you **define infrastructure as code** and then **create/update/destroy** it consistently.

It turns code into real cloud/on-prem resources.

---

## 2️⃣ Why It Exists
Real-world problem:
- Manual infrastructure creation is slow and inconsistent
- Changes are hard to track and repeat
- Teams need safe rollouts, history, and automation

Terraform was created to:
- Make infra repeatable and version-controlled
- Reduce human error
- Enable automation (CI/CD)
- Manage changes safely via plan/apply

---

## 3️⃣ What It Actually Does
Core responsibilities:
- Reads your `.tf` files (desired state)
- Talks to providers (AWS/Azure/vSphere/etc.)
- Calculates a change plan (diff)
- Applies changes to reach desired state
- Tracks what it created using **state**

Where it sits:
- **Infra layer** (cloud resources, networking, IAM, VMs, LB, DNS, etc.)
- Often triggered from **CI/CD**
- Often paired with **Ansible** (Terraform provisions, Ansible configures)

---

## 4️⃣ How It Works Step-by-Step Flow
Terraform operates like this:

1. You write `.tf` code (desired infrastructure)
2. Terraform loads providers + backend config
3. Terraform reads existing state (local or remote)
4. Terraform refreshes reality (provider APIs)
5. Terraform builds a plan (what to add/change/destroy)
6. You approve it
7. Terraform applies changes
8. Terraform updates state

Mental model:
**Code (desired) + State (known) + Provider (reality) → Plan → Apply**

---

## 5️⃣ General Real-Life Analogy
Terraform is like a **construction manager with a blueprint**.

- Blueprint = your `.tf` files
- Current building record = state file
- Workers = provider APIs
- Plan = list of changes needed to match blueprint
- Apply = actual construction work

---

## 6️⃣ Telecom / DevOps Production Use Case
Example: Creating an Open RAN lab environment:
- VPC/VLANs, subnets, routing
- security groups/firewall rules
- VM instances for CU/DU, monitoring stack, jump hosts
- DNS records, load balancers
- IAM roles/service accounts

Production concerns:
- **Reliability:** stable state management + locking
- **Scaling:** modules for reuse across sites/environments
- **Failure handling:** roll back via versioned code + re-apply plan
- **Drift:** detect and correct manual changes

---

## 7️⃣ Kubernetes/Cloud Architecture View (if applicable)
Terraform commonly manages:
- EKS cluster and node groups
- VPC/subnets/route tables
- IAM roles (IRSA), security groups
- ECR repos, S3 buckets
- DNS (Route53), ACM certs

Terraform does NOT replace Kubernetes manifests.
- Terraform builds infra (cluster, networking)
- Helm/ArgoCD/kubectl deploy apps into the cluster

---

## 8️⃣ SDLC + CI/CD Placement
Where Terraform fits:

- Code: `.tf` changes in Git
- Build/Test: `terraform fmt`, `terraform validate`, `tflint`, `tfsec/checkov`
- Plan: generated in CI, reviewed
- Apply: usually controlled (manual approval for prod)
- Monitor: cloud monitoring + drift detection

Common pipeline stages:
1) fmt/validate  
2) plan (PR)  
3) apply (merge + approval)

---

## 9️⃣ Simple Workflow Diagram (ASCII)

Developer PR
  ↓
CI: terraform fmt + validate + lint + security scan
  ↓
CI: terraform plan (artifact)
  ↓
Review/Approval
  ↓
CD: terraform apply (protected env)
  ↓
Cloud resources updated
  ↓
Monitoring + drift detection

---

## 🔟 Terraform File Roles (main.tf, variables.tf, locals.tf, outputs.tf, providers.tf, etc.)

Terraform doesn’t require specific filenames, but teams standardize.

### `main.tf`
- Main resources and module calls live here.

### `variables.tf`
- Input variables (things you pass in).

### `terraform.tfvars` / `*.tfvars`
- Values for variables (env-specific).
- Don’t commit secrets.

### `locals.tf`
- Derived values / computed constants to reduce repetition.
- Example: naming conventions, merged maps.

### `outputs.tf`
- Values you want to export (IP, DNS, IDs).
- Used by other modules/pipelines.

### `providers.tf`
- Provider configuration and versions.

### `versions.tf` (common practice)
- Terraform version + provider version constraints.

### `backend.tf` (common practice)
- Remote state backend config (S3, AzureRM, etc.)

---

## 1️⃣1️⃣ Folder Structure (Production-Grade Example)

### Option A: One repo, environment folders (common)

terraform/
modules/
vpc/
eks/
iam/
envs/
dev/
main.tf
variables.tf
locals.tf
outputs.tf
providers.tf
backend.tf
dev.tfvars
prod/
main.tf
variables.tf
locals.tf
outputs.tf
providers.tf
backend.tf
prod.tfvars


### Option B: Separate repo per environment (high control, more overhead)
- `infra-dev` repo, `infra-prod` repo

---

## 1️⃣2️⃣ Modules (What they are + Why)
### Simple Definition
A module is a reusable Terraform package.

Why modules exist:
- reuse patterns (VPC, EKS, VM)
- standard naming/tags
- reduce copy/paste
- enforce guardrails

### Example module call (root `main.tf`)
```hcl
module "vpc" {
  source = "../../modules/vpc"

  name       = var.name
  cidr_block = var.vpc_cidr
  tags       = local.common_tags
}

Inside a module

modules/vpc/ contains resources, variables, outputs.

# modules/vpc/main.tf
resource "aws_vpc" "this" {
  cidr_block = var.cidr_block
  tags       = var.tags
}

1️⃣3️⃣ Providers (What they are)

A provider is the plugin that talks to an API.

Examples:

aws → AWS APIs

azurerm → Azure APIs

vsphere → VMware APIs

kubernetes → K8s APIs

Example providers.tf:

terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

1️⃣4️⃣ State File (Critical Concept)
What is Terraform state?

State is Terraform’s “memory” of what it created.

State includes:

resource IDs

attributes

dependencies

outputs

Why state exists:

Terraform must know what already exists to compute diffs.

Why local state is risky

Local terraform.tfstate on a laptop:

no team sharing

easy to lose

no locking

high risk of corruption

1️⃣5️⃣ Remote State Backend (Best Practice)

Remote backend stores state centrally + supports locking.

Common choices:

AWS S3 + DynamoDB lock

Terraform Cloud/Enterprise

Azure Storage + locks

GCS + locking

Example: S3 backend (concept)
terraform {
  backend "s3" {
    bucket         = "my-tf-state"
    key            = "envs/prod/terraform.tfstate"
    region         = "eu-west-2"
    dynamodb_table = "my-tf-locks"
    encrypt        = true
  }
}


Benefits:

team collaboration

state locking (prevents two applies at once)

versioning (S3 object versioning)

disaster recovery

1️⃣6️⃣ Vault (HashiCorp Vault) in Terraform

Important: Vault is not the same as Terraform state.

Vault is for:

secrets (DB passwords, API tokens, dynamic creds)

short-lived credentials

encryption as a service

How Terraform uses Vault:

Terraform reads secrets from Vault at runtime

Or Terraform creates dynamic secrets engines, policies, etc.

Example (conceptual):

provider "vault" {
  address = var.vault_addr
}

data "vault_generic_secret" "db" {
  path = "secret/data/prod/db"
}

# Use secret in a resource (be careful; may still end up in state)

CRITICAL WARNING (state + secrets)

If you put secrets directly into Terraform-managed resources, they can end up in state.
That’s why:

remote state must be encrypted + access controlled

prefer patterns that avoid storing plaintext secrets in state

use runtime injection (K8s secrets operator, app pulling from Vault, etc.)

1️⃣7️⃣ Key Terraform Commands (Must Know)
Initialize (downloads providers, configures backend)
terraform init

Format code
terraform fmt -recursive

Validate syntax and basic correctness
terraform validate

Plan changes (shows what will happen)
terraform plan -var-file=dev.tfvars

Apply changes
terraform apply -var-file=dev.tfvars

Destroy (danger)
terraform destroy -var-file=dev.tfvars

Show state and resources
terraform state list
terraform state show <resource>

Output values
terraform output

1️⃣8️⃣ Common Beginner Mistakes

Keeping state locally for team projects

No locking → two applies at once → state corruption

Hardcoding values (regions, CIDRs, names)

No module reuse → copy/paste drift

Putting secrets into .tfvars and committing to Git

Using latest provider versions (unexpected breakage)

Manually changing infra in console → drift

1️⃣9️⃣ Production Risks

Blast radius: wrong workspace/env → wrong infra changed

Drift: manual changes cause surprises

State exposure: secrets inside state, weak access controls

No locking: concurrent apply corrupts infra/state

Bad module design: changes ripple across many environments

Provider/API changes: breaking upgrades

Mitigations:

remote state + locking + encryption

least privilege IAM

environment separation

mandatory plan review

CI checks (fmt/validate/lint/security scans)

module versioning

2️⃣0️⃣ Quick Cheat Sheet Recap

main.tf = resources/module calls

variables.tf = inputs

locals.tf = computed helpers

outputs.tf = exported values

providers.tf = provider config

state = Terraform memory (critical)

use remote backend + locking

Vault is for secrets, not state

always: fmt → validate → plan → apply

End of Document