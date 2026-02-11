# Kubernetes Fundamentals: Architecture, Networking, Storage & Core Manifests

Purpose: Build a strong mental model of Kubernetes architecture, core components, networking, storage, ReplicaSets, labels, annotations, and how real-world YAML manifests work.

---

# 1️⃣ Simple Definition

Kubernetes (K8s) is a container orchestration platform that:

- Deploys containers
- Scales them
- Self-heals them
- Manages networking
- Manages storage
- Handles rolling updates

It turns many servers into one logical system.

---

# 2️⃣ Why Kubernetes Exists

Problem before K8s:

- Docker runs containers on one machine.
- No multi-node orchestration.
- No automatic failover.
- No scaling automation.
- No built-in service discovery.

Kubernetes solves:

- Multi-node container orchestration
- Auto-scaling
- Self-healing
- Service discovery
- Rolling updates
- Resource management

---

# 3️⃣ Kubernetes Architecture

Cluster
│
├── Control Plane
│     ├── API Server
│     ├── Scheduler
│     ├── Controller Manager
│     └── etcd
│
└── Worker Nodes
      ├── kubelet
      ├── kube-proxy
      └── Container Runtime

---

## Control Plane Components

### API Server
- Entry point to cluster
- All commands go through it

### Scheduler
- Decides which node runs a pod

### Controller Manager
- Maintains desired state
- Recreates failed pods
- Ensures replicas match spec

### etcd
- Key-value store
- Stores cluster state

---

## Worker Node Components

### kubelet
- Talks to control plane
- Starts/stops containers

### kube-proxy
- Handles networking rules

### Container Runtime
- Runs containers (containerd)

---

# 4️⃣ Core Kubernetes Objects

---

## Pod

Smallest deployable unit.

Contains:
- One or more containers
- Shared network
- Shared storage

Example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
    - name: app
      image: nginx:1.25
      ports:
        - containerPort: 80
Pods are ephemeral.

ReplicaSet

Ensures desired number of pod replicas.

Example:

apiVersion: apps/v1
kind: ReplicaSet
spec:
  replicas: 3


If 1 pod dies → recreated automatically.

Deployment

Most common workload controller.

Manages:

ReplicaSets

Rolling updates

Rollbacks

Example:

apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: app:v1.2.0

5️⃣ Labels & Selectors

Labels are key-value pairs.

Used for:

Grouping

Selection

Service targeting

Example:

labels:
  app: web
  env: prod


Service selects pods via:

selector:
  app: web


If labels mismatch → traffic fails.

6️⃣ Annotations

Metadata not used for selection.

Used for:

Ingress configuration

Monitoring tools

External integrations

Descriptive metadata

Example:

annotations:
  nginx.ingress.kubernetes.io/rewrite-target: /


Labels = identification
Annotations = additional info

7️⃣ Kubernetes Networking Model

Key rule:

Every Pod gets its own IP.

Pods can communicate with each other without NAT.

Service

Abstracts pods.

Types:

ClusterIP (default)

Internal access only.

NodePort

Exposes service on node IP.

LoadBalancer

Cloud load balancer.

Example:

apiVersion: v1
kind: Service
spec:
  type: ClusterIP
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 8080

Ingress

Layer 7 routing.

Example:

apiVersion: networking.k8s.io/v1
kind: Ingress
spec:
  rules:
    - host: app.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: app
                port:
                  number: 80


Ingress requires controller (Nginx/ALB).

8️⃣ Storage & Persistence

Containers are ephemeral.

Use Persistent Volumes.

PersistentVolume (PV)

Cluster-level storage.

PersistentVolumeClaim (PVC)

Request for storage.

Example:

apiVersion: v1
kind: PersistentVolumeClaim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi


Attach to pod:

volumes:
  - name: data
    persistentVolumeClaim:
      claimName: my-pvc


Never store DB data inside container layer.

9️⃣ ConfigMaps & Secrets
ConfigMap

Stores non-sensitive config.

Secret

Stores sensitive data (base64 encoded).

Example:

env:
  - name: DB_HOST
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: host


Never hardcode secrets in images.

🔟 Resource Management

Always define:

resources:
  requests:
    cpu: "200m"
    memory: "256Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"


Without limits:

Pods may consume all node memory.

OOM issues.

1️⃣1️⃣ Rolling Updates

Deployment handles:

Zero-downtime updates

Gradual rollout

Rollback support

Update image:

kubectl set image deployment/app app=app:v1.3.0

1️⃣2️⃣ Common Production Issues

CrashLoopBackOff

OOMKilled

ImagePullBackOff

Service not routing

PVC pending

Node NotReady

Label mismatch

1️⃣3️⃣ Troubleshooting Flow

Check pod:

kubectl get pods


Describe:

kubectl describe pod <name>


Logs:

kubectl logs <pod>


Events:

kubectl get events


Check service:

kubectl get svc


Check endpoints:

kubectl get endpoints

1️⃣4️⃣ Manifest Anatomy

Every YAML has:

apiVersion:
kind:
metadata:
  name:
  labels:
spec:


spec defines desired state.

1️⃣5️⃣ Quick Cheat Sheet

Pod = container wrapper

ReplicaSet = replica controller

Deployment = rolling updates

Service = stable access

Ingress = HTTP routing

Labels = grouping

Annotations = metadata

PVC = storage

ConfigMap = config

Secret = sensitive data

Resources = limits/requests

Kubernetes is a reconciliation system.

You declare desired state.
Controllers continuously enforce it.

End of Document


---

Now next logical progression:

1️⃣ Helm (how real teams manage K8s YAML at scale)  
2️⃣ Kubernetes networking deep dive (CNI, iptables, kube-proxy, service mesh)  
3️⃣ Advanced topics (HPA, probes, readiness, liveness, autoscaling)  
4️⃣ Production-grade architecture patterns