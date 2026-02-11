# Containerization – Foundations Before Docker & Kubernetes

Purpose: Understand what containerization is, why it exists, how it works internally, and how it fits into real-world DevOps and cloud-native systems.

---

# 1️⃣ Simple Definition

Containerization is a way to package an application and everything it needs to run into an isolated, portable unit called a container.

It ensures the application runs the same everywhere.

---

# 2️⃣ Why Containerization Exists

Real-world problem before containers:

- “It works on my machine” issues
- Different OS versions
- Library conflicts
- Dependency mismatches
- Hard to scale apps
- Heavy virtual machines

Containers were created to solve:

- Portability
- Consistency
- Faster startup
- Better resource usage
- Easier scaling

---

# 3️⃣ What It Actually Does

A container:

- Packages app + dependencies
- Shares host Linux kernel
- Runs isolated processes
- Uses minimal resources
- Starts in seconds

Where it sits:

Hardware
↓
Linux Kernel
↓
Container Runtime (Docker/containerd)
↓
Containers
↓
Application

Containers are **process-level isolation**, not full virtual machines.

---

# 4️⃣ How It Works (Step-by-Step Flow)

1. Developer writes code.
2. Container image is built.
3. Image contains:
   - App
   - Runtime (Node/Java/Go)
   - Libraries
   - Config defaults
4. Image is stored in registry.
5. Container runtime pulls image.
6. Linux kernel isolates it using namespaces & cgroups.
7. App runs as a process inside container.

It is just a process — but isolated.

---

# 5️⃣ Real-Life Analogy

Think of containers like shipping containers.

Before:
- Goods packed differently for each truck.
- Hard to move between ports.

Now:
- Everything packed in standard container.
- Same container works on any ship/truck/train.

Container = standardized packaging for software.

---

# 6️⃣ Containers vs Virtual Machines

## Virtual Machine

- Full OS inside
- Own kernel
- Heavy
- Slow startup

## Container

- Shares host kernel
- Lightweight
- Fast startup
- Efficient

Comparison:

| Feature | VM | Container |
|----------|------|------------|
| Kernel | Separate | Shared |
| Boot time | Minutes | Seconds |
| Resource usage | High | Low |
| Isolation | Strong | Process-level |

---

# 7️⃣ Linux Features Behind Containers

Containers rely on Linux:

## Namespaces
Isolate:
- Processes
- Networking
- Filesystem
- Users

## cgroups
Control:
- CPU limits
- Memory limits
- Resource quotas

## Overlay Filesystem
Layered images for efficient storage.

Without Linux kernel features → no containers.

---

# 8️⃣ Container Image vs Container

Important distinction:

Container Image:
- Read-only blueprint
- Stored in registry
- Built once

Container:
- Running instance of image
- Writable runtime layer
- Ephemeral

Image → blueprint  
Container → running process

---

# 9️⃣ End-to-End Example (E-Commerce App)

Without containers:

- Install Node
- Install dependencies
- Set environment
- Run server
- Fix missing library
- Debug OS issues

With containers:

- Build image
- Push to registry
- Run anywhere

Flow:

Developer
↓
Build Image
↓
Push to Registry
↓
Server pulls image
↓
Container runs

Same everywhere.

---

# 🔟 DevOps Production Use Case

Example:

Microservices architecture:

- user-service
- payment-service
- order-service

Each packaged as container.

Benefits:

- Independent deployment
- Independent scaling
- Easy rollback
- Immutable infrastructure

---

# 1️⃣1️⃣ Kubernetes View (High Level)

Containers run on:

Node (Linux server)
↓
Container runtime
↓
Pods
↓
Services
↓
Ingress

Kubernetes orchestrates containers.

Containers are building blocks.

---

# 1️⃣2️⃣ CI/CD Placement

Pipeline:

Code
↓
Build Container Image
↓
Push Image
↓
Deploy Container
↓
Monitor

Containers standardize deployment artifact.

---

# 1️⃣3️⃣ How Containerization Fails in Production

## Scenario 1: OOMKilled
Container exceeds memory limit.

## Scenario 2: CrashLoopBackOff
App crashes repeatedly.

## Scenario 3: Image pull error
Registry access issue.

## Scenario 4: Large image
Slow deployment.

## Scenario 5: Wrong base image
Security vulnerability.

---

# 1️⃣4️⃣ Troubleshooting Flow

1. Is container running?
2. Check logs.
3. Check memory/CPU limits.
4. Check networking.
5. Check image version.
6. Check environment variables.

Always start from runtime state.

---

# 1️⃣5️⃣ Common Beginner Mistakes

- Using `latest` image tag
- Not setting resource limits
- Storing secrets in image
- Large base images
- Not scanning images for vulnerabilities
- Writing stateful apps without persistence strategy

---

# 1️⃣6️⃣ Production Risks

- Supply chain attacks (malicious base image)
- Unscanned vulnerabilities
- Registry downtime
- Poor resource planning
- No rollback versioning

Mitigation:

- Use minimal base images
- Pin versions
- Scan images (Trivy/Snyk)
- Store images in private registry
- Tag images properly

---

# 1️⃣7️⃣ Quick Cheat Sheet

- Container = isolated Linux process
- Image = blueprint
- Shares host kernel
- Lightweight vs VM
- Uses namespaces + cgroups
- Built in CI
- Deployed via Kubernetes
- Immutable artifact

Containerization is the foundation of modern cloud-native systems.

---

End of Document
