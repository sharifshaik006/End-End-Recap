# Ansible & Configuration Management in Production

Purpose: Understand how Ansible works in real-world DevOps environments, how configuration management is structured, and how to use playbooks, roles, ad-hoc commands, and Vault securely.

---

# 1. What Is Configuration Management?

Configuration Management ensures that systems are:

- Consistent
- Predictable
- Version controlled
- Idempotent
- Reproducible

It enforces desired state across servers.

Instead of manually configuring servers, we declare how they should look.

---

# 2. What Is Ansible?

Ansible is an agentless automation tool used for:

- Configuration management
- Application deployment
- Infrastructure orchestration
- Provisioning
- Security enforcement

It connects over SSH and executes tasks on remote systems.

---

# 3. Why Ansible Exists

Problem:
Manual server configuration leads to:

- Drift
- Inconsistency
- Human error
- Scaling problems

Ansible solves this by:

- Defining desired state in YAML
- Applying it across multiple hosts
- Ensuring idempotency
- Maintaining consistency

---

# 4. Where Ansible Sits in Architecture

Infrastructure Layer
↓
Operating System (Linux)
↓
Ansible connects via SSH
↓
Tasks executed (packages, configs, services)

In CI/CD:
- Used during deploy stage
- Used to bootstrap infrastructure

---

# 5. Core Concepts

## Inventory

Defines target machines.

Example:
[web]
10.0.0.10
10.0.0.11

[db]
10.0.0.20


Inventory can be:
- Static (file)
- Dynamic (AWS, VMware, etc.)

---

## Playbook

YAML file defining tasks to run on hosts.

Example:


name: Install Nginx
hosts: web
become: yes
tasks:

name: Install nginx package
apt:
name: nginx
state: present


---

## Task

Single unit of action.

Example:
- Install package
- Copy file
- Restart service

---

## Module

Reusable unit used in tasks.

Examples:
- apt
- yum
- copy
- file
- service
- user

Modules are idempotent by design.

---

# 6. Playbook Structure (Production Grade)



project/
├── inventories/
│ ├── dev/
│ │ └── hosts.yml
│ ├── prod/
│ │ └── hosts.yml
├── group_vars/
├── host_vars/
├── roles/
├── playbooks/
│ └── site.yml
├── ansible.cfg


---

# 7. Roles (Best Practice)

Roles structure reusable configuration blocks.



roles/
└── nginx/
├── tasks/
│ └── main.yml
├── handlers/
│ └── main.yml
├── templates/
├── files/
├── defaults/
├── vars/
└── meta/


Benefits:
- Reusable
- Organized
- Clean separation of concerns
- Scalable

---

# 8. Handlers

Triggered only when a task changes something.

Example:


name: Copy config
template:
src: nginx.conf.j2
dest: /etc/nginx/nginx.conf
notify: Restart nginx

handlers:

name: Restart nginx
service:
name: nginx
state: restarted


Efficient and idempotent.

---

# 9. Ad-Hoc Commands

Quick one-liner commands.

Examples:

Ping hosts:


ansible all -m ping


Install package:


ansible web -m apt -a "name=nginx state=present" -b


Check uptime:


ansible all -a "uptime"


Useful for:
- Testing
- Quick fixes
- Validation

---

# 10. Variables

Defined in:
- group_vars
- host_vars
- playbooks
- roles

Example:


nginx_port: 8080


Referenced as:


{{ nginx_port }}


---

# 11. Ansible Vault (Secrets Management)

Used to encrypt:

- Passwords
- API tokens
- SSH keys
- Sensitive variables

Create encrypted file:


ansible-vault create secrets.yml


Encrypt existing file:


ansible-vault encrypt vars.yml


Run playbook with vault:


ansible-playbook site.yml --ask-vault-pass


Never store plain secrets in Git.

---

# 12. Idempotency in Ansible

Ansible modules ensure:

- Package not reinstalled if present
- File not recopied if unchanged
- Service not restarted unnecessarily

Idempotency is built-in.

---

# 13. Execution Flow



Inventory
↓
Playbook
↓
Tasks
↓
Modules
↓
Target Hosts


Controller machine executes logic.
Remote hosts execute modules.

---

# 14. CI/CD Integration

Pipeline:

Git Push
↓
CI Pipeline
↓
Lint (ansible-lint)
↓
Syntax check
↓
Ansible playbook run
↓
Deployment complete

---

# 15. Common Beginner Mistakes

- Hardcoding IPs
- Mixing prod and dev inventory
- No roles structure
- Storing secrets in plain text
- Running playbooks without testing
- No idempotent design thinking

---

# 16. Production Risks

- Wrong inventory → wrong servers modified
- No vault → secret leakage
- No limit flag → all hosts affected
- No dry-run before prod
- Manual changes causing drift

---

# 17. Important Commands to Learn



ansible --version
ansible-inventory --list
ansible-playbook site.yml
ansible-playbook --check
ansible-playbook --diff
ansible all -m ping
ansible-lint playbook.yml


---

# 18. Dry Run & Safety

Dry run:


ansible-playbook site.yml --check


Limit to specific host:


ansible-playbook site.yml --limit web01


Always test before production.

---

# 19. Other Configuration Management Tools

| Tool | Language | Agentless | Notes |
|------|----------|-----------|-------|
| Ansible | YAML | Yes | Simple, SSH-based |
| Puppet | Ruby DSL | No | Agent-based |
| Chef | Ruby | No | Agent-based |
| SaltStack | Python | Optional | High-speed |
| Terraform | HCL | N/A | Infrastructure provisioning |

Ansible focuses on OS-level configuration.

Terraform focuses on infrastructure provisioning.

They complement each other.

---

# 20. Quick Cheat Sheet

- Inventory = target servers
- Playbook = execution plan
- Role = reusable configuration unit
- Module = action
- Vault = encrypted secrets
- Handlers = event-driven tasks
- Ad-hoc = quick one-liner
- Idempotent by default

Ansible enforces desired state at OS level safely and predictably.

---

End of Document