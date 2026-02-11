# Server Foundations

---

# 1. Simple Definition

A server is a computer (physical or virtual) that provides services, data, or applications to other computers over a network.

It waits for requests and responds.

---

# 2. Why Servers Exist

Problem:
If every device stored everything locally, nothing could be shared or centralized.

Servers were created to:
- Host applications centrally
- Store shared data
- Handle multiple users simultaneously
- Enforce security and access control
- Enable scalability

Instead of 1,000 devices storing a database,
one server hosts it and serves everyone.

---

# 3. What a Server Actually Does

Core Responsibilities:
- Listen on an IP address
- Wait on a port (e.g., 80, 443, 22)
- Accept client requests
- Process logic
- Send back responses

A server is defined by role, not size.

Examples:
- Web server (NGINX, Apache)
- Application server
- Database server (MySQL, PostgreSQL)
- DNS server
- File server
- API server

---

# 4. How a Server Works (Step-by-Step)

1. Server powers on.
2. Operating system (usually Linux) boots.
3. It receives an IP address.
4. An application starts (example: web service).
5. The application listens on a port.
6. A client sends a request to IP:Port.
7. The server processes the request.
8. The server sends back a response.

Example:

User Browser
→ Sends request to 10.0.0.10:443
→ Server receives request
→ Application processes logic
→ Response returned
→ Browser displays result

---

# 5. Types of Servers

## Physical Server
Bare-metal hardware in a data center.

## Virtual Machine (VM)
Server running on hypervisor (vSphere, KVM, ESXi).

## Cloud Server
Virtual server provisioned in AWS/Azure/GCP.

## Kubernetes Node
Server participating in container orchestration.

---

# 6. Core Components of a Server

CPU  
→ Processes instructions  

RAM  
→ Temporary working memory  

Disk  
→ Persistent storage  

Network Interface (NIC)  
→ Sends/receives traffic  

Operating System  
→ Manages hardware  

Application Process  
→ Provides service  

Firewall  
→ Controls incoming/outgoing traffic  

Monitoring Agent  
→ Tracks health and metrics  

---

# 7. Server in Cloud & Kubernetes

In modern infrastructure:

VM = Server  
Kubernetes Node = Server  

Inside Kubernetes:

Control Plane
→ Manages servers

Node
→ Is the server

Pod
→ Runs application inside server

Service
→ Exposes pods across servers

Everything still runs on a server somewhere.

Cloud did not remove servers.
It automated provisioning and scaling.

---

# 8. Telecom Production Example (5G)

Example: UPF (User Plane Function)

UE (Phone)
→ gNB
→ Core Network Server (AMF/SMF)
→ UPF Server handles data traffic

Production Characteristics:
- Multiple servers deployed
- Load balancing enabled
- Health checks active
- Failover configured
- Auto-scaling enabled

If one server fails:
- Traffic shifts to another
- Sessions re-established
- Monitoring alerts triggered

Server reliability directly affects telecom uptime.

---

# 9. DevOps Lifecycle Placement

1. Provision server (VM/Bare Metal/Cloud)
2. Install OS (Linux)
3. Configure networking
4. Install application
5. Expose ports
6. Add monitoring
7. Configure backup
8. Scale horizontally if needed

---

# 10. Server Workflow Diagram

User
  ↓
Client (Browser / App)
  ↓
Internet / LAN
  ↓
Server (IP + Port)
  ↓
Application Process
  ↓
Database
  ↓
Response

---

# 11. Common Beginner Mistakes

- Thinking server must be huge hardware
- Ignoring firewall rules
- Running all services on one server
- No backups
- No monitoring
- Hardcoding credentials
- Manual configuration (causes drift)

---

# 12. Production Risks

Single Point of Failure  
→ Only one server running  

Resource Exhaustion  
→ CPU/RAM full  

Disk Full  
→ Services crash  

Security Exposure  
→ Open ports  

Configuration Drift  
→ Manual changes not tracked  

Network Misconfiguration  
→ Service unreachable  

---

# 13. How Servers Fail in Production

Common Failures:

1. SSH reachable, but app not responding  
2. Ping works, but port closed  
3. High latency due to CPU bottleneck  
4. Kubernetes node NotReady  
5. Service crash due to memory leak  

---

# 14. Troubleshooting Approach

Step 1: Is server reachable? (ping / network check)
Step 2: Is port open? (netstat / ss / firewall)
Step 3: Is service running? (systemctl / process check)
Step 4: Check logs.
Step 5: Check CPU/RAM/disk.
Step 6: Check recent changes.

Always troubleshoot bottom-up:
Network → OS → Application.

---

# 15. Quick Cheat Sheet

- Server = machine that responds to requests
- Has IP + Port
- Runs application software
- Can be physical or virtual
- Must be monitored
- Must be secured
- Must avoid single point of failure
- In cloud, servers are programmable

---

End of Document



