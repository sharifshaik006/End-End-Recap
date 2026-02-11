# Cloud Fundamentals – AWS, Azure, GCP Core Concepts & Services

Purpose: Understand the major cloud concepts used in real-world production systems and map equivalent services across AWS, Azure, and GCP.

---

# 1️⃣ What Is Cloud Computing?

Cloud computing is renting:

- Compute
- Storage
- Networking
- Databases
- Managed services

On-demand via API.

Instead of buying servers, you provision them via code.

---

# 2️⃣ Core Cloud Service Categories

Every cloud platform provides similar service layers:

1. Compute
2. Networking
3. Storage
4. Databases
5. Identity & Access Management
6. Containers & Kubernetes
7. Serverless
8. Monitoring & Logging
9. DevOps & CI/CD
10. Security & Compliance

---

# 3️⃣ Compute Services

Virtual machines.

| Concept | AWS | Azure | GCP |
|---------|------|-------|------|
| Virtual Machine | EC2 | Virtual Machines | Compute Engine |
| Autoscaling | ASG | VM Scale Sets | Managed Instance Groups |

Used for:
- App servers
- Worker nodes
- Self-managed services

---

# 4️⃣ Managed Kubernetes

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| Managed K8s | EKS | AKS | GKE |

These provide:
- Control plane managed
- Worker nodes configurable
- Integrated IAM

Most modern workloads use these.

---

# 5️⃣ Container Registry

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| Registry | ECR | ACR | Artifact Registry |

Used to store Docker images.

---

# 6️⃣ Object Storage

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| Object Storage | S3 | Blob Storage | Cloud Storage |

Used for:
- Backups
- Static websites
- Logs
- Terraform state

Highly durable.

---

# 7️⃣ Block Storage

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| Block Disk | EBS | Managed Disks | Persistent Disks |

Used for:
- VM storage
- Databases
- Stateful apps

---

# 8️⃣ Networking

Every cloud has similar networking primitives.

## Virtual Network

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| Virtual Network | VPC | VNet | VPC |

Includes:
- Subnets
- Route tables
- NAT
- Security rules

---

## Load Balancer

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| L4/L7 LB | ALB/NLB | Azure Load Balancer / App Gateway | Cloud Load Balancing |

Used to expose applications.

---

## Firewall / Security Rules

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| Firewall | Security Groups / NACL | NSG | Firewall Rules |

Controls inbound/outbound traffic.

---

# 9️⃣ Identity & Access Management (IAM)

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| IAM | IAM Roles & Policies | Azure AD + RBAC | IAM Policies |

Used for:
- User permissions
- Service accounts
- Role-based access
- API authentication

Critical for security.

---

# 🔟 Managed Databases

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| Relational | RDS | Azure SQL | Cloud SQL |
| NoSQL | DynamoDB | CosmosDB | Firestore |
| Cache | ElastiCache | Azure Cache | Memorystore |

Avoid running DB inside Kubernetes unless necessary.

---

# 1️⃣1️⃣ Serverless

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| Serverless Compute | Lambda | Azure Functions | Cloud Functions |

Used for:
- Event-driven workloads
- Short tasks
- Lightweight APIs

---

# 1️⃣2️⃣ Infrastructure as Code

| Tool | Cloud |
|------|--------|
| Terraform | All |
| CloudFormation | AWS only |
| ARM/Bicep | Azure only |
| Deployment Manager | GCP |

Terraform is multi-cloud.

---

# 1️⃣3️⃣ Monitoring & Logging

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| Monitoring | CloudWatch | Azure Monitor | Cloud Monitoring |
| Logs | CloudWatch Logs | Log Analytics | Cloud Logging |

Production must monitor:
- CPU
- Memory
- Errors
- Latency
- Scaling events

---

# 1️⃣4️⃣ CI/CD & DevOps

| Concept | AWS | Azure | GCP |
|----------|------|-------|------|
| CI/CD | CodePipeline | Azure DevOps | Cloud Build |

But many teams use:
- GitHub Actions
- Jenkins
- GitLab CI

---

# 1️⃣5️⃣ Multi-Region & Availability Zones

Each cloud offers:

- Regions (geographical areas)
- Availability Zones (isolated data centers)

Best practice:
- Deploy across AZs
- Avoid single-AZ design

---

# 1️⃣6️⃣ Production Cloud Architecture (Typical Pattern)

Internet
↓
Load Balancer
↓
Kubernetes (EKS/AKS/GKE)
↓
Services
↓
Pods
↓
Database (Managed)
↓
Object Storage

Everything built on:

VPC
Subnets
IAM
Security Groups

---

# 1️⃣7️⃣ Common Production Risks

- Misconfigured IAM
- Open security groups
- No encryption
- Single AZ deployment
- No backup strategy
- Hardcoded secrets
- Over-provisioning cost

---

# 1️⃣8️⃣ Cloud Cost Awareness

Costs driven by:

- Compute hours
- Storage usage
- Data transfer
- Load balancer usage
- Managed services

Always monitor:
- Unused resources
- Idle volumes
- Orphaned load balancers
- Snapshots

---

# 1️⃣9️⃣ Cloud Security Principles

- Least privilege IAM
- Private subnets
- No public DB
- Encrypt storage
- Enable audit logs
- Rotate secrets
- Use WAF for public apps

---

# 2️⃣0️⃣ Quick Cloud Mapping Summary

Compute → EC2 / VM / Compute Engine  
K8s → EKS / AKS / GKE  
Object Storage → S3 / Blob / GCS  
Block Storage → EBS / Disk / PD  
IAM → IAM / AAD / IAM  
Load Balancer → ALB / App Gateway / LB  
Database → RDS / Azure SQL / Cloud SQL  

Concepts are same.
Names differ.

---

# Final Insight

Cloud platforms differ in implementation.
But core architecture patterns remain the same:

- Network isolation
- Identity control
- Managed compute
- Persistent storage
- Observability
- Automation

If you understand the concepts,
you can work in any cloud.

---

End of Document


Do you want next:

1️⃣ Deep dive into AWS architecture specifically
2️⃣ Multi-cloud design strategy
3️⃣ Cloud networking deep dive (VPC, routing, NAT, peering, transit gateways)
4️⃣ Cloud security architecture
5️⃣ Production-grade EKS architecture end-to-end