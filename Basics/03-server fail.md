# How Servers Fail in Production

---

## 1. Simple Definition

Server failure occurs when a server cannot correctly provide its intended service to clients.

Failure does not always mean the machine is down.  
Sometimes the server is running, but the service is unavailable.

---

## 2. Why Server Failures Happen

Servers fail due to:

- Resource exhaustion
- Misconfiguration
- Application crash
- Network issues
- Dependency failures
- Security restrictions
- Hardware problems

In production, most failures are configuration or resource related — not hardware.

---

## 3. What Actually Fails

Failure can happen at different layers:

### Network Layer
- Server unreachable
- DNS resolution failure
- Firewall blocking traffic

### Transport Layer
- Port closed
- Service not listening

### Application Layer
- Process crashed
- Deadlock
- Memory leak

### Infrastructure Layer
- Disk full
- CPU 100%
- RAM exhausted

### Cluster Layer (Kubernetes)
- Node NotReady
- Pod CrashLoopBackOff
- CNI failure

---

## 4. Common Production Failure Scenarios

### Scenario 1: SSH Works but App Not Responding

Meaning:
- Server is reachable
- Application process likely down

Possible Causes:
- Service stopped
- Crash due to memory issue
- Configuration error

Action:
Check service status and logs.

---

### Scenario 2: Ping Works but Port Closed

Meaning:
- Network reachable
- Application not listening

Possible Causes:
- Service not running
- Firewall blocking port
- Wrong port configured

Action:
Check listening ports and firewall rules.

---

### Scenario 3: High Latency

Meaning:
- Service responds slowly

Possible Causes:
- CPU bottleneck
- Memory pressure
- I/O wait
- Database slow queries

Action:
Check CPU, memory, disk I/O metrics.

---

### Scenario 4: Kubernetes Node NotReady

Meaning:
- Node unhealthy in cluster

Possible Causes:
- Kubelet crash
- Resource exhaustion
- Network plugin failure

Action:
Check node status and kubelet logs.

---

## 5. Step-by-Step Troubleshooting Flow

Always troubleshoot bottom-up.

### Step 1: Is Server Reachable?
- Can you ping?
- Can you SSH?

If not → Network issue.

---

### Step 2: Is Port Open?
- Is the application listening?
- Is firewall blocking?

If port closed → Service or firewall issue.

---

### Step 3: Is Service Running?
- Check process
- Check system service status
- Review logs

If crashed → Investigate root cause.

---

### Step 4: Are Resources Available?
- CPU usage
- Memory usage
- Disk space
- File descriptors

If exhausted → Scale or optimize.

---

### Step 5: What Changed Recently?

- New deployment?
- Config change?
- Firewall update?
- Certificate expired?

Most production failures are change-related.

---

## 6. Telecom / Cloud Production Context

In 5G/Open RAN environments:

Failure can cause:
- Dropped sessions
- Registration failures
- Latency spikes
- Data path interruption

Mitigation strategies:
- Redundant servers
- Load balancing
- Auto-scaling
- Health checks
- Rolling updates
- Alerting

Telecom systems must assume failure will happen.

---

## 7. Production Prevention Principles

- Never run single instance services
- Always monitor CPU/RAM/Disk
- Use health checks
- Automate deployments
- Avoid manual configuration drift
- Log everything
- Alert early

Failure prevention is part of architecture.

---

## 8. Quick Failure Diagnosis Map

Cannot connect?
→ Network or firewall

Can connect but no response?
→ Service not running

Slow response?
→ Resource bottleneck

Pod restarting?
→ Application crash

Node NotReady?
→ Infrastructure or kubelet issue

---

End Section
