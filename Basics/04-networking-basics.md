# Networking Basics

Purpose: Build a foundational understanding of how computers communicate in production environments (Cloud, Kubernetes, Telecom, Enterprise Infrastructure).

---

# 1. Simple Definition

Networking is the system that allows computers to communicate with each other using IP addresses, ports, and protocols.

Without networking, systems cannot exchange data.

---

# 2. Why Networking Exists

Problem:
Computers need to share data, services, and resources.

Networking was created to:
- Connect devices
- Transfer data
- Enable remote communication
- Support distributed systems
- Allow internet access

Modern systems are distributed.  
Distributed systems require networking.

---

# 3. Core Building Blocks

## IP Address
Identifies a device on a network.

Example:
10.0.0.10  
192.168.1.5  

Public IP → Accessible from internet  
Private IP → Internal network only  

---

## Port
Identifies a specific service on a machine.

Examples:
22 → SSH  
80 → HTTP  
443 → HTTPS  
3306 → MySQL  

IP identifies machine.  
Port identifies service.

---

## Protocol
Rules that define how communication happens.

TCP → Reliable communication  
UDP → Fast, connectionless  
HTTP → Web communication  
HTTPS → Secure web communication  
ICMP → Ping  

---

## Router
Connects different networks together.

---

## Switch
Connects devices inside the same network.

---

## Firewall
Controls which traffic is allowed or blocked.

---

# 4. How Networking Works (Step-by-Step)

Example: Accessing a website.

1. User types URL in browser.
2. DNS translates domain → IP address.
3. Browser sends request to IP:Port.
4. Request travels through:
   - Local router
   - ISP network
   - Internet backbone
5. Server receives request.
6. Server responds.
7. Response travels back.

Flow:

Client → Router → Internet → Server → Response → Client

---

# 5. Private vs Public IP

## Private IP
Used inside local networks.
Not reachable directly from internet.

Examples:
10.0.0.0/8  
172.16.0.0/12  
192.168.0.0/16  

## Public IP
Globally unique.
Reachable over internet.

Routers use NAT (Network Address Translation)
to map private IPs to public IP.

---

# 6. LAN vs WAN

LAN (Local Area Network)
→ Inside office/home network

WAN (Wide Area Network)
→ Connects multiple LANs (Internet)

Telecom networks operate at massive WAN scale.

---

# 7. Networking in Cloud & Kubernetes

In Cloud:

- Each VM gets private IP.
- Load balancer exposes public IP.
- Security groups act as firewall.

In Kubernetes:

Node
→ Has IP

Pod
→ Has internal IP

Service
→ Virtual IP that routes traffic to pods

Ingress
→ Exposes services externally

Cluster networking enables pod-to-pod communication.

---

# 8. Telecom Production Example (5G)

Example: User accessing internet via 5G.

UE (Phone)
→ gNB
→ Transport Network
→ Core Network (AMF/SMF/UPF)
→ Internet

Networking ensures:
- Packet routing
- QoS enforcement
- Low latency
- Session continuity

Failure in routing = dropped sessions.

---

# 9. OSI Model (Simplified)

Physical → Cables  
Data Link → MAC  
Network → IP  
Transport → TCP/UDP  
Application → HTTP/DNS  

You mostly work at:
- Network layer (IP)
- Transport layer (TCP/UDP)
- Application layer (HTTP)

---

# 10. Networking Workflow Diagram

User
  ↓
Client Device
  ↓
Local Router
  ↓
Internet / WAN
  ↓
Public IP
  ↓
Server IP:Port
  ↓
Application
  ↓
Response

---

# 11. Common Beginner Mistakes

- Confusing IP and Port
- Ignoring firewall rules
- Not understanding private vs public IP
- Assuming ping means application works
- Ignoring DNS issues

---

# 12. Production Risks

- Packet loss
- High latency
- Misconfigured routing
- Firewall blocking traffic
- DNS failure
- NAT issues
- Port conflicts

In telecom:
Networking failure = service outage.

---

# 13. How Networking Fails in Production

Examples:

1. Server reachable via ping, but app unreachable  
   → Port closed or firewall blocking  

2. DNS resolution failing  
   → Wrong DNS config  

3. High latency  
   → Network congestion  

4. Packet drops  
   → Interface overload  

5. Pod cannot reach another pod  
   → CNI misconfiguration  

---

# 14. Troubleshooting Approach

Step 1: Check DNS  
Step 2: Check IP reachability (ping)  
Step 3: Check port connectivity (telnet / nc)  
Step 4: Check firewall rules  
Step 5: Check routing table  
Step 6: Check logs  

Always troubleshoot bottom-up:
DNS → Network → Port → Application.

---

# 15. Quick Cheat Sheet

- IP = Machine
- Port = Service
- Protocol = Communication rule
- Router = Connect networks
- Switch = Connect devices
- Firewall = Security gate
- Private IP = Internal
- Public IP = Internet-facing

Networking is the foundation of distributed systems.

---

End of Document

