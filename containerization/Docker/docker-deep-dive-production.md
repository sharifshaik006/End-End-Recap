# Docker Deep Dive – Components, Networking, Commands & Production Reality

Purpose: Understand Docker architecture, image lifecycle, networking models, core commands, and real-world production challenges before moving into Kubernetes.

---

# 1️⃣ Simple Definition

Docker is a container platform that builds, ships, and runs containerized applications.

It packages your application into a portable container image and runs it using the Linux kernel.

---

# 2️⃣ Docker Architecture (Core Components)

Docker consists of:

Client
↓
Docker Engine (Daemon)
↓
containerd
↓
Linux Kernel

---

## Docker Client

CLI tool:
# Docker Deep Dive – Components, Networking, Commands & Production Reality

Purpose: Understand Docker architecture, image lifecycle, networking models, core commands, and real-world production challenges before moving into Kubernetes.

---

# 1️⃣ Simple Definition

Docker is a container platform that builds, ships, and runs containerized applications.

It packages your application into a portable container image and runs it using the Linux kernel.

---

# 2️⃣ Docker Architecture (Core Components)

Docker consists of:

Client
↓
Docker Engine (Daemon)
↓
containerd
↓
Linux Kernel

---

## Docker Client

CLI tool:

docker build
docker run
docker ps


Sends commands to Docker daemon.

---

## Docker Daemon (dockerd)

- Manages images
- Manages containers
- Handles networking
- Talks to container runtime
- Runs in background

---

## containerd

Lower-level runtime.
Actually starts/stops containers.

---

## runc

Creates Linux namespaces + cgroups.
Executes container process.

---

## Docker Registry

Stores images.

Examples:
- Docker Hub
- AWS ECR
- GHCR
- Private registry

---

# 3️⃣ Docker Image Internals

An image is:

- Layered filesystem
- Read-only
- Built from Dockerfile

Example Dockerfile:



FROM node:20-alpine
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
CMD ["node", "server.js"]


Each instruction creates a new layer.

Layers are cached.

---

# 4️⃣ Image vs Container

Image:
- Blueprint
- Immutable
- Stored in registry

Container:
- Running instance
- Has writable layer
- Ephemeral by default

Image → run → container

---

# 5️⃣ Docker Networking (Critical Topic)

Docker networking controls how containers communicate.

---

## Default Bridge Network

Created automatically.

- Containers can talk via IP
- External traffic requires port mapping

Example:



docker run -p 8080:80 nginx


Host:8080 → Container:80

---

## User-Defined Bridge Network (Recommended)



docker network create mynet
docker run --network mynet ...


Benefits:
- DNS-based container name resolution
- Better isolation

---

## Host Network



docker run --network host ...


Container shares host network stack.

No port mapping.

Used rarely (performance cases).

---

## None Network



docker run --network none ...


No networking.

---

# 6️⃣ Port Mapping Explained

Example:



docker run -p 8080:80 nginx


Meaning:

Host port 8080
↓
Forwarded to container port 80

Traffic flow:

Browser → Host:8080 → Container:80

---

# 7️⃣ Volumes (Persistent Storage)

Containers are ephemeral.

If container dies → data gone.

Use volumes:



docker volume create mydata
docker run -v mydata:/var/lib/mysql mysql


Volume types:

- Named volumes
- Bind mounts
- tmpfs

Production apps must externalize storage.

---

# 8️⃣ Docker Commands (Must Know)

## Build image


docker build -t app:v1.0.0 .


## List images


docker images


## Run container


docker run -d -p 8080:80 app:v1.0.0


## List running containers


docker ps


## List all containers


docker ps -a


## Stop container


docker stop <container_id>


## Remove container


docker rm <container_id>


## Remove image


docker rmi app:v1.0.0


## View logs


docker logs <container_id>


## Exec into container


docker exec -it <container_id> /bin/sh


## Inspect container


docker inspect <container_id>


---

# 9️⃣ Docker Compose (Multi-Container Local Dev)

Example `docker-compose.yml`:



services:
app:
build: .
ports:
- "8080:8080"
db:
image: postgres:15


Used for local development.

Not production orchestration (Kubernetes preferred).

---

# 🔟 Real-World Docker in Production

Production patterns:

- Build image in CI
- Tag with git SHA
- Push to private registry (ECR)
- Deploy via Kubernetes
- Never build in production servers
- Never use "latest"

---

# 1️⃣1️⃣ Security Best Practices

- Use minimal base images (alpine, distroless)
- Do not run as root
- Scan images (Trivy)
- Pin base image versions
- Use multi-stage builds
- Avoid storing secrets in image

Example non-root:



RUN adduser -D appuser
USER appuser


---

# 1️⃣2️⃣ Common Production Issues

## OOMKilled
Container exceeds memory limit.

## CrashLoop
App exits repeatedly.

## Port conflict
Host port already used.

## Image pull error
Registry authentication issue.

## Large image
Slow deployment.

## Zombie containers
Not cleaned up.

## Disk full
Old images consuming storage.

---

# 1️⃣3️⃣ Troubleshooting Workflow

1. Check container status:


docker ps


2. Check logs:


docker logs <id>


3. Check resource usage:


docker stats


4. Inspect configuration:


docker inspect <id>


5. Check networking:


docker network ls
docker network inspect


---

# 1️⃣4️⃣ Docker vs Kubernetes

Docker:
- Runs containers
- Single-node orchestration
- Manual scaling

Kubernetes:
- Multi-node orchestration
- Auto scaling
- Self-healing
- Rolling updates

Docker is runtime.
Kubernetes is orchestrator.

---

# 1️⃣5️⃣ Production Risks

- Supply chain attack (malicious base image)
- Unpatched vulnerabilities
- Image sprawl
- No image retention policy
- Secrets baked into image
- No resource limits

Mitigation:

- Private registry
- Vulnerability scanning
- Image lifecycle policies
- Resource constraints
- CI security checks

---

# 1️⃣6️⃣ End-to-End Real Example

CI pipeline:

Code
↓
docker build -t app:v1.2.0 .
↓
docker push 123456789.dkr.ecr.eu-west-2.amazonaws.com/app:v1.2.0
↓
Kubernetes pulls image
↓
Pod runs container
↓
Service exposes app
↓
Ingress routes traffic

Everything revolves around container image.

---

# 1️⃣7️⃣ Quick Cheat Sheet

- Docker daemon manages containers
- containerd runs containers
- Images are layered
- Containers share Linux kernel
- Use volumes for persistence
- Use user-defined networks
- Tag images properly
- Never use latest in production
- Scan images
- Do not store secrets in image

Docker is the packaging engine of modern DevOps.

---

End of Document