# DNS – Domain Name System (Simple + Real World Production View)

Purpose: Understand what DNS is, why it exists, how it works internally, and how it is used in real-world cloud and Kubernetes environments.

---

# 1️⃣ Simple Definition

DNS (Domain Name System) translates human-friendly domain names into IP addresses.

Example:

example.com → 93.184.216.34

It is the phonebook of the internet.

---

# 2️⃣ Why DNS Exists

Computers communicate using IP addresses.

Humans remember names.

DNS bridges this gap.

Without DNS:

You would type:
http://142.250.74.78

Instead of:
https://google.com

DNS makes the internet usable.

---

# 3️⃣ Where DNS Is Used

DNS is used:

- When you open a website
- When your app connects to a database
- When Kubernetes services resolve names
- When CI/CD pulls images from registries
- When cloud services communicate

Every networked system depends on DNS.

---

# 4️⃣ How DNS Works (Step-by-Step Flow)

When you type:

https://app.example.com

Step 1:
Your browser checks local cache.

Step 2:
If not cached, it asks your system resolver.

Step 3:
Resolver asks recursive DNS server (ISP/Cloud/Corporate).

Step 4:
Recursive server queries:
- Root servers
- TLD servers (.com)
- Authoritative nameserver

Step 5:
Authoritative server responds with IP.

Step 6:
Browser connects to returned IP.

This usually happens in milliseconds.

---

# 5️⃣ Real-World DNS Records

DNS has multiple record types.

---

## A Record

Maps domain → IPv4

example.com → 1.2.3.4

---

## AAAA Record

Maps domain → IPv6

---

## CNAME

Alias record.

app.example.com → example.com

Used when pointing to load balancers.

---

## MX Record

Mail server record.

example.com → mail server

---

## TXT Record

Used for:

- Domain verification
- SPF/DKIM
- Ownership proofs

---

## NS Record

Defines authoritative nameservers.

---

# 6️⃣ DNS in Cloud (AWS / Azure / GCP)

Managed DNS services:

- AWS → Route53
- Azure → Azure DNS
- GCP → Cloud DNS

Typical production flow:

User
↓
Public DNS (Route53)
↓
Load Balancer (ALB)
↓
Kubernetes Ingress
↓
Service
↓
Pod

DNS points to Load Balancer.

---

# 7️⃣ DNS in Kubernetes

Kubernetes has internal DNS.

Example:

backend-service.default.svc.cluster.local

Pods can call services using:

http://backend-service

KubeDNS / CoreDNS resolves service names.

---

# 8️⃣ Internal vs External DNS

## External DNS

Publicly accessible domains.
Example:
api.company.com

## Internal DNS

Used inside VPC or cluster.
Example:
db.internal.local

Production often uses:

- Private hosted zones
- Split DNS (internal vs external views)

---

# 9️⃣ TTL (Time To Live)

TTL defines how long DNS is cached.

Example:

TTL = 300 seconds

Impacts:

- Failover speed
- Propagation delay
- Traffic routing

Low TTL:
- Faster failover
- More DNS queries

High TTL:
- Faster resolution
- Slower updates

Production must balance this.

---

# 🔟 DNS Load Balancing

Multiple A records:

example.com → 1.1.1.1
example.com → 2.2.2.2

DNS can distribute traffic.

Cloud often uses:

DNS → Load Balancer → Autoscaled backend

---

# 1️⃣1️⃣ Real Production Example

Deploying EKS application:

1. Create ALB
2. ALB gets DNS name:
   internal-alb-123.eu-west-2.elb.amazonaws.com
3. Create Route53 record:
   api.company.com → CNAME → ALB DNS
4. Users access api.company.com
5. Traffic flows correctly

DNS connects user to load balancer.

---

# 1️⃣2️⃣ Common Production DNS Problems

## 1️⃣ DNS Not Resolving

- Wrong nameserver
- Missing A record
- Incorrect zone

## 2️⃣ Wrong IP Returned

- Stale cache
- TTL not expired

## 3️⃣ Internal Service Not Resolving

- CoreDNS down
- Wrong namespace
- Network policy blocking

## 4️⃣ Slow Website

- High DNS lookup latency
- DNS server overload

---

# 1️⃣3️⃣ Troubleshooting DNS

On Linux:

Check resolution:

nslookup example.com
dig example.com


Check specific server:


dig @8.8.8.8 example.com


Check Kubernetes service:


kubectl exec -it pod -- nslookup backend-service


Check nameservers:


cat /etc/resolv.conf


---

# 1️⃣4️⃣ DNS Security Concepts

- DNS spoofing
- Cache poisoning
- DNSSEC
- Private hosted zones
- Internal-only resolution

Production best practice:

- Restrict internal DNS zones
- Use private DNS for databases
- Enable DNS query logging
- Monitor unusual DNS patterns

---

# 1️⃣5️⃣ DNS in Multi-Region Architecture

Example:

api.company.com
→ Route53 latency routing
→ Region A or Region B

Used for:

- High availability
- Disaster recovery
- Geo routing

---

# 1️⃣6️⃣ End-to-End Flow Diagram

User Browser
↓
Local DNS Cache
↓
Recursive Resolver
↓
Authoritative DNS
↓
Load Balancer
↓
Kubernetes Ingress
↓
Service
↓
Pod

DNS is the first step of every request.

---

# 1️⃣7️⃣ Quick Cheat Sheet

DNS = name → IP  
A record = IPv4  
CNAME = alias  
TTL = cache duration  
Route53/AzureDNS/CloudDNS = managed DNS  
K8s uses CoreDNS  
Internal DNS for services  
DNS usually points to Load Balancer  

If DNS breaks, everything breaks.

---

End of Document