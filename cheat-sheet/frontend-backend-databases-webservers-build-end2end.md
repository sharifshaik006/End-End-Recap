# Frontend + Backend + Databases + Nginx/Apache + Tomcat + Build/Compile/Package (End-to-End Wide)

Purpose: Understand **what each component is**, **why it exists**, **where it sits**, and **how it flows end-to-end** from developer code to production runtime.

---

## 1️⃣ Simple Definition (Whole System)
A modern application is usually split into:
- **Frontend** (what user sees)
- **Backend** (business logic + APIs)
- **Database** (persistent data)
- **Web server / Reverse proxy** (entrypoint, routing, TLS)
- **App server** (for some runtimes like Java/Tomcat)
- **Build tools + CI/CD** (convert code into deployable artifacts)

---

## 2️⃣ Where Each Part Sits (Architecture Placement)

User (Browser/Mobile)
  ↓
DNS (name → IP)
  ↓
Reverse Proxy / Load Balancer (Nginx/Apache/ALB/Ingress)
  ↓
Backend API (Go/Java/Python/Node/.NET)
  ↓
Database (Postgres/MySQL/etc.)
  ↓
Response → back to user

Frontend can be served either:
- From CDN/static hosting OR
- Via Nginx/Apache OR
- Embedded in backend (less common for modern apps)

---

# A) FRONTEND (UI Layer)

## A1) What it is
Frontend is the user-facing part of the app:
- web pages
- buttons
- forms
- UI logic

## A2) Why it exists
Users need a visual interface to interact with your system.

## A3) Where it runs
- In the **browser** (Chrome/Safari/Edge) OR
- In a mobile app (iOS/Android)

## A4) Common Frontend Languages/Frameworks
### Languages
- **HTML** (structure)
- **CSS** (style)
- **JavaScript** (logic)
- **TypeScript** (JavaScript + types for safer large projects)

### Frameworks (common)
- React
- Angular
- Vue
- Svelte
- Next.js (React framework; SSR/SSG)

## A5) How frontend is built
Frontend code is not “compiled to machine code”.
It is usually:
- **bundled** and **transpiled**
- optimized for browsers
- output as static files

Output examples:
- `dist/` or `build/` directory containing:
  - `.html`
  - `.css`
  - `.js`
  - assets/images

Build tools:
- npm scripts (webpack/vite/rollup)
- yarn/pnpm
- Next.js build pipeline

---

# B) BACKEND (API + Business Logic Layer)

## B1) What it is
Backend is server-side code that:
- validates requests
- applies business rules
- talks to databases
- returns API responses (JSON, gRPC, etc.)

## B2) Why it exists
Frontend should not directly handle:
- DB credentials
- business rules
- payments
- privileged logic

Backend is the trusted layer.

## B3) Where it runs
- On servers/VMs
- In containers (Docker)
- In Kubernetes pods
- Serverless functions (Lambda/Functions)

## B4) Common Backend Languages (with typical “build output”)

### Go
- Compiled language
- Output: single binary (native machine code)
- Build: `go build`

### Java / Kotlin (JVM)
- Compiled to **bytecode**
- Runs on JVM
- Output: `.jar` or `.war`
- Build: Maven/Gradle

### Node.js (JavaScript/TypeScript)
- JS runs on Node runtime
- TypeScript transpiles to JS
- Output: JS bundle + node_modules (or bundled)
- Build: npm/yarn/pnpm

### Python
- Interpreted (bytecode internally, but not like Java artifacts)
- Output: package (`.whl`) or container image
- Build: pip/poetry/setuptools; sometimes pyinstaller for standalone binary

### .NET (C#)
- Compiled to IL (intermediate language)
- Runs on CLR
- Output: dll/exe
- Build: `dotnet build/publish`

---

# C) DATABASES (Data Layer)

## C1) What it is
A database stores persistent data reliably.

## C2) Why it exists
Apps need consistent storage for:
- users
- orders
- inventory
- sessions
- logs (sometimes)

## C3) Where it runs
- Managed DB service (recommended): RDS/Azure SQL/Cloud SQL
- Dedicated VM
- Kubernetes StatefulSet + PVC (only if needed)

## C4) Types of databases (common)
### Relational (SQL)
- PostgreSQL, MySQL
- Strong consistency, joins, transactions

### NoSQL
- MongoDB (document)
- Cassandra (wide-column)
- DynamoDB/Firestore (managed)
- Redis (cache/key-value)

---

# D) NGINX / APACHE (Web Server + Reverse Proxy)

## D1) What it is
Nginx/Apache can serve:
- static content (frontend)
- act as reverse proxy
- handle TLS termination
- route traffic to backend

## D2) Why it exists
- one stable public entrypoint
- security controls
- traffic routing
- performance (caching, compression)
- protects backend services

## D3) Where it sits
Internet
  ↓
Nginx/Apache
  ↓
Backend API / App server

## D4) When to use Nginx vs Apache
### Nginx
- high concurrency
- reverse proxy is common
- popular for K8s ingress controller style

### Apache
- widely used traditional web server
- good for certain legacy workloads

In modern cloud-native:
Nginx is more common as reverse proxy.

---

# E) TOMCAT (Java App Server)

## E1) What it is
Tomcat is a Java application server (Servlet container) that runs Java web apps.

It hosts:
- `.war` applications
- servlets (Java web API model)

## E2) Why it exists
Java web apps historically run inside an application server that manages:
- request lifecycle
- threads
- session management
- servlet/JSP support

## E3) Where it sits
Internet
  ↓
Nginx (reverse proxy)
  ↓
Tomcat (runs Java web app)
  ↓
Database

## E4) Modern note
Today many Java apps run as Spring Boot `.jar` directly (embedded server),
but Tomcat is still widely used in enterprises.

---

# F) Compile vs Build vs Package (Clear Definitions)

## Compile
Convert source code into:
- machine code (Go/C/C++)
OR
- intermediate representation (Java bytecode, .NET IL)

Examples:
- Go: source → native binary
- Java: source → `.class` bytecode
- C#: source → IL

## Build
Build is broader than compile:
- compile (if needed)
- fetch dependencies
- run tests
- link libraries
- generate artifacts
- package output

## Package
Output format:
- `.jar` / `.war` (Java)
- binary (Go)
- `.whl` (Python)
- `.dll`/`.exe` (.NET)
- container image (Docker)

In container-native systems, the final “package” is often the **Docker image**.

---

# G) Build Tools by Language (Practical View)

## Java
### Maven
- Build file: `pom.xml`
- Handles: dependencies, build lifecycle, packaging
- Output: `.jar` or `.war`

Common commands:
- `mvn test`
- `mvn package`

### Gradle
- Build file: `build.gradle`
- Output: `.jar`/`.war`
- Faster, more flexible

---

## Go
- Build files: `go.mod`, `go.sum`
- Commands:
  - `go test ./...`
  - `go build`

Output:
- native binary

---

## Node.js / TypeScript
- Build file: `package.json` (+ lockfile)
- Commands:
  - `npm ci`
  - `npm run build`
  - `npm test`

Output:
- static build (frontend) OR compiled JS (backend)

---

## Python
- Build config: `pyproject.toml` (modern) or `setup.py`
- Dependencies: `requirements.txt` or poetry
- Commands:
  - `pip install -r requirements.txt`
  - `pytest`

Packaging:
- `.whl` (wheel)
- or container image

Special:
- `pyinstaller` can make standalone executables (rare in cloud-native)

---

## .NET
- Build file: `.csproj` / `.sln`
- Commands:
  - `dotnet build`
  - `dotnet test`
  - `dotnet publish`

Output:
- dll/exe + runtime pack

---

# H) Where Artifacts Are Stored (Artifact Repo vs Registry)

## Artifact repository (language packages)
Stores:
- Java jars/wars
- Python wheels
- Node packages (private npm)
- Maven packages

Tools:
- JFrog Artifactory
- Sonatype Nexus
- AWS CodeArtifact
- GitHub Packages (limited)

## Container registry (Docker images)
Stores:
- container images

Tools:
- AWS ECR
- GHCR
- Docker Hub
- Azure ACR
- GCP Artifact Registry

Important:
They are not “the same”, but both store **deployables**.
Artifact repo = language/package artifacts
Registry = container images

---

# I) End-to-End Real Workflow (Clone → Compile → Build → Package → Deploy)

## 1) Clone code
- Developer/CI pulls source code from Git
Example:
- GitHub/Bitbucket/GitLab

## 2) Install dependencies
- from package repos (Maven Central, npm registry, PyPI)
- sometimes proxied/cached by JFrog/Nexus

## 3) Compile (if applicable)
- Go/Java/.NET compile step happens
- Node/JS might transpile TS → JS

## 4) Build (compile + test + package)
- build tool runs pipelines and produces artifact

## 5) Package
Option A: Output language artifact
- jar/war/binary/wheel

Option B: Build container image
- Dockerfile packages runtime + app into image

## 6) Store deliverable
- Artifacts → JFrog/Nexus
- Images → ECR/GHCR/DockerHub

## 7) Deploy
- VM deploy: copy artifact + run service
- K8s deploy: update image tag in manifest/Helm values

## 8) Route traffic
- DNS → Load Balancer/Ingress → Nginx → service → pod

## 9) Observe
- Metrics (Prometheus)
- Dashboards (Grafana)
- Logs (ELK/Loki)
- Alerts (Alertmanager)

---

# J) End-to-End Wide Example (Simple E-Commerce)
User buys a product:

1. User opens `shop.example.com`
2. DNS resolves domain → load balancer IP
3. Load balancer forwards to Nginx/Ingress
4. Frontend static files served (HTML/JS/CSS)
5. Frontend calls API: `api.example.com/orders`
6. Nginx routes `/orders` to backend service
7. Backend validates request + business logic
8. Backend queries DB (Postgres)
9. DB returns result
10. Backend returns JSON response
11. Frontend shows success message
12. Metrics/logs/traces captured for visibility

---

# K) Quick Cheat Sheet (One Screen)

### Frontend
- HTML/CSS/JS/TS
- build output = static files

### Backend
- Go (binary), Java (jar/war), Node (JS), Python (packages), .NET (dll)

### Database
- SQL (Postgres/MySQL) vs NoSQL (Mongo/Redis)
- must be backed up and monitored

### Nginx/Apache
- reverse proxy + TLS + routing + caching

### Tomcat
- Java app server for WAR apps

### Compile vs Build
- compile = code → executable/bytecode
- build = compile + deps + tests + package

### Artifact repo vs Registry
- JFrog/Nexus = packages (jar/wheel)
- ECR/GHCR = container images

---

End of Document
