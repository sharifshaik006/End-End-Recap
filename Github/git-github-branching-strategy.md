# Git, GitHub & Real-World Branching Strategies

Purpose: Understand how Git works, how GitHub workflows operate in production teams, common push issues, and real-world branching strategies including Netflix-style trunk-based development.

---

# 1. What Is Git?

Git is a distributed version control system.

It tracks:
- Code changes
- File history
- Branches
- Merges
- Contributors

Every developer has a full copy of repository history.

---

# 2. What Is GitHub?

GitHub is:
- A Git hosting platform
- Collaboration tool
- PR (Pull Request) manager
- CI/CD integration layer
- Code review platform

Git = version control  
GitHub = collaboration + automation platform

---

# 3. Core Git Commands (Production Critical)

## Initialize repo

git init


## Clone repo


git clone <repo-url>


## Check status


git status


## Add files


git add .


## Commit


git commit -m "message"


## Pull latest changes


git pull origin main


## Push changes


git push origin branch-name


## Create branch


git checkout -b feature/login


## Switch branch


git checkout main


## Merge branch


git merge feature/login


---

# 4. Common Git Push Issues (Real World)

## 1️⃣ Non-Fast-Forward Error

Error:


! [rejected] main -> main (non-fast-forward)


Cause:
Remote branch has commits you don’t have locally.

Fix:


git pull --rebase origin main
git push origin main


---

## 2️⃣ Authentication Failure

Cause:
Wrong token / expired token.

Fix:
- Use Personal Access Token (PAT)
- Configure SSH properly

---

## 3️⃣ Force Push Danger



git push -f


This overwrites history.

In production:
Avoid force push to shared branches like `main`.

---

## 4️⃣ Detached HEAD

Happens when checking out commit directly.

Fix:


git checkout branch-name


---

# 5. Branching Strategies in Real World

Branching strategy defines:

- How teams collaborate
- How releases are managed
- How hotfixes are handled
- How CI/CD is triggered

---

# 6. Git Flow (Traditional Enterprise)

Branches:
- main (production)
- develop (integration)
- feature/*
- release/*
- hotfix/*

Pros:
- Structured
- Clear release phases

Cons:
- Heavy
- Slow merges
- Large integration conflicts
- Not ideal for microservices

---

# 7. Trunk-Based Development (Netflix-Style)

Netflix uses a trunk-based strategy.

Core idea:
- One main branch (trunk)
- Short-lived feature branches
- Frequent merges to main
- Heavy automation + feature flags

Flow:

Developer creates small feature branch
↓
Open Pull Request
↓
Automated tests run
↓
Code review
↓
Merge to main
↓
CI builds + deploys

Branches live hours or days, not weeks.

---

# 8. Netflix-Style Principles

- Main branch always deployable
- No long-lived branches
- Continuous integration
- Automated testing required
- Feature flags for incomplete work
- Fast feedback cycles

They rely on:
- Strong CI
- Canary deployments
- Observability
- Rollback automation

---

# 9. Example Netflix-Style Workflow



main
↑
feature/payment-api
↑
PR → tests → review → merge


No develop branch.

Production releases are just tagged commits on main.

---

# 10. Feature Flags (Critical for Trunk-Based)

Instead of long branches:

You merge incomplete code behind a flag.

Example:


if featureFlag("new_checkout"):
useNewCheckout()
else:
useOldCheckout()


This allows:
- Safe merging
- Gradual rollout
- A/B testing
- Instant disable without redeploy

---

# 11. CI/CD Integration with Branching

GitHub Actions typically:

On PR:
- Run tests
- Run lint
- Run security scan

On merge to main:
- Build image
- Push to registry
- Deploy to dev/stage
- Optional manual approval for prod

---

# 12. Real-World Best Practice (Cloud Native Teams)

Recommended:

- main (production)
- feature/*
- hotfix/*
- release tags (v1.2.3)

Avoid:
- Long-lived develop branch
- Massive feature branches
- Manual production edits

---

# 13. Tagging Strategy

Use semantic versioning:



v1.0.0
v1.0.1
v1.1.0


Or commit SHA:



app:abcd123


Best practice:
Use image tags based on git SHA.

---

# 14. Common Beginner Mistakes

- Working directly on main
- Force pushing shared branches
- Large feature branches
- Not rebasing before PR
- Ignoring merge conflicts
- Not writing clear commit messages
- No PR reviews

---

# 15. Safe Push Workflow (Recommended)

1. Create branch:


git checkout -b feature/payment


2. Commit changes:


git add .
git commit -m "Add payment validation"


3. Push branch:


git push origin feature/payment


4. Open PR on GitHub

5. Wait for CI checks

6. Merge after review

---

# 16. Production Risks in Git Management

- Broken main branch
- Accidental deletion
- History rewrite
- Secrets committed
- No branch protection rules

Always enable:
- Required PR reviews
- Required status checks
- Prevent force push
- Prevent direct push to main

---

# 17. GitHub Branch Protection (Important)

Enable:

- Require pull request before merge
- Require status checks
- Require review approval
- Restrict who can push
- Require signed commits (optional)

This prevents production damage.

---

# 18. Summary Comparison

| Strategy | Complexity | Speed | Best For |
|-----------|------------|--------|----------|
| Git Flow | High | Slow | Legacy enterprise |
| Trunk-Based | Medium | Fast | Cloud-native teams |
| Netflix-style | Optimized | Very Fast | High-scale microservices |

---

# 19. Quick Cheat Sheet

- Git = version control
- GitHub = collaboration platform
- main should always be stable
- Use short-lived feature branches
- Avoid force push
- Use PR reviews
- Protect main branch
- Automate everything
- Use feature flags for incomplete work

---

End of Document