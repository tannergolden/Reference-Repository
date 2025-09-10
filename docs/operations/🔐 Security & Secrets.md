# 🔐 Security & Secrets

Practical guardrails for keeping credentials out of Git, minimizing blast radius, and shipping safely across **Experimental → Development → Preview → Release**.

> [!CAUTION]
> Treat supply-chain alerts and secret findings as **release blockers**. If a secret leaks, **revoke/rotate** immediately and follow the incident steps below.

---

## 🎯 Objectives
- **Prevent leaks**: no secrets in code, commits, Pull Requests (PRs), or build logs.
- **Least privilege**: short-lived tokens, scoped permissions, environment isolation.
- **Detect fast**: automated secret scans, code scanning, dependency health.
- **Recover fast**: rotation playbooks, hotfix path, auditable promotions.

---

## 🧱 Golden Rules

1. **No secrets in Git**: use `.env.example` (placeholders only), never `.env` with real values.  
2. **Short-lived credentials** via **OIDC** in Continuous Integration/Continuous Delivery (CI/CD) instead of long-lived keys.  
3. **Separate environments** (Development/Preview/Release) with distinct secrets and IAM roles.  
4. **Minimum scopes**: only grant what a workflow or service needs (RBAC).  
5. **Automate detection**: secret scanning on every PR; fail the build for real leaks.  
6. **Rotate & expire**: define lifetimes; automate rotation where possible.  
7. **Redact on output**: never echo secrets; scrub logs and artifacts.  
8. **Encrypt everywhere**: Transport Layer Security (TLS) in transit; platform-managed keys at rest.

---

## 📦 Where Secrets Live (and don’t)

| Location | Allowed? | Notes |
|---|:--:|---|
| Source code, commits, PRs, issues | ❌ | Never. Treat as public. |
| GitHub Environments (Development/Preview/Release) | ✅ | Use environment-scoped secrets with reviewers/approvals. |
| Cloud Secret Manager (e.g., GCP Secret Manager, AWS Secrets Manager) | ✅ | Prefer for runtime apps; fetch at startup with IAM. |
| Local `.env` (developer machine) | ⚠️ | Allowed for dev only; **never commit**; use `.env.example` for placeholders. |
| Build logs/artifacts | ❌ | Redact; mark sensitive; avoid printing secret values. |

> [!TIP]
> Keep a **`/config`** or **`/secrets`** README describing how secrets are provisioned, rotated, and injected per environment.

---

## 🧰 Managing Secrets in CI/CD

**Preferred pattern: OIDC (OpenID Connect) → cloud role → short-lived token**

- GitHub workflow requests an **OIDC token** (`id-token: write` permission).  
- Cloud IAM trusts the GitHub OIDC provider for specific repos/branches.  
- Workflow exchanges OIDC for a short-lived credential (minutes), scoped to deploy/read-only.

**Minimal GitHub Actions example:**
```yaml
permissions:
  id-token: write
  contents: read

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: Preview
    steps:
      - uses: actions/checkout@v4
      - name: Authenticate to cloud via OIDC
        run: |
          echo "Exchange OIDC for short-lived token here (provider-specific)."
      - name: Deploy
        run: ./scripts/deploy-preview.sh
````

**If you must store static secrets** (avoid when possible): put them in **GitHub Environments** with required reviewers and audit access; never in repository-level secrets shared across all branches.

---

## 🧭 Access & Rotation Policy

| Secret Type               | Scope             | Lifetime                             | Rotation                    |
| ------------------------- | ----------------- | ------------------------------------ | --------------------------- |
| Preview deploy token      | Preview only      | ≤ 24h (short-lived)                  | On demand / auto            |
| Production deploy token   | Release only      | ≤ 1h (short-lived)                   | On promote                  |
| API keys (3rd-party)      | Env-specific      | Provider default (≤ 90d recommended) | Calendar rotation + on leak |
| Service account (runtime) | Workload-specific | Short-lived via OIDC if possible     | Not applicable (ephemeral)  |

> \[!NOTE]
> Document **who can approve** access to each environment and how to request secrets. Keep the list in CODEOWNERS or a `SECURITY.md`.

---

## 🔍 Automated Scanning

Enable the following in CI (non-interactive, fast):

* **Secret scanning** (e.g., Gitleaks/TruffleHog) on every PR.
* **SCA (Software Composition Analysis)** for vulnerable dependencies.
* **SAST (Static Application Security Testing)** (e.g., CodeQL) for code flows.
* **Container/IaC** scans if you build images or provision infrastructure.

**Example (Gitleaks)**

```yaml
- name: Secret scan
  uses: gitleaks/gitleaks-action@v2
  with:
    args: detect --redact --source .
```

**Example (informational SCA)**

```yaml
- name: Audit (informational)
  run: npm audit --audit-level=high || true
```

> \[!CAUTION]
> A **confirmed** secret finding is a blocker. Revoke the secret, rotate, and force-push a purge only if legally required (history rewriting has trade-offs).

---

## 🧾 PII & Logging

* **Never log PII (Personally Identifiable Information)**, secrets, or tokens.
* Prefer **structured logs** and explicit redaction.
* For web apps, apply **Content Security Policy (CSP)** and **HTTP Strict Transport Security (HSTS)**; keep **TLS** everywhere.

---

## 🧱 Firebase & Front-End Considerations (if applicable)

* **Firestore/Storage rules**: enforce least privilege, validate auth context, and test rules in CI with emulators.
* **Firebase client config** is public by design; **it is not a secret**. Protect data via rules and backend checks.
* Keep Cloud Functions secrets in a **secret manager**, not in code or environment files committed to Git.
* For Single-Page Apps (SPAs), keep keys **server-side** when they grant privileged access; use backend proxies.

---

## 🔐 Example: `.env.example` (placeholders only)

```ini
# Example placeholders (do not commit real values)
API_BASE_URL=https://api.example.com
FIREBASE_PROJECT_ID=<project-id>
SENTRY_DSN=<dsn>
```

---

## 🧯 Incident Response (secret exposure)

1. **Contain**

   * Revoke exposed token/key in provider.
   * Rotate credentials and update affected environments.

2. **Remediate**

   * If needed, **invalidate dependent sessions/tokens**.
   * Remove the secret from the repo history only if mandated; prefer revocation + new secrets.

3. **Communicate**

   * Open an incident ticket; document timeline/impact.
   * Notify affected owners; coordinate hotfix if production impacted.

4. **Prevent**

   * Add/adjust patterns in secret scanner.
   * Tighten RBAC, shorten token lifetimes, increase approvals.

**Hotfix path:** use `hotfix/<ticket>` → PR to `Release` → tag patch → **back-merge** to `Development` (and `Preview` if diverged).

---

## ✅ Security Readiness Checklist

* [ ] Secrets stored only in GitHub Environments or cloud secret manager.
* [ ] OIDC enabled for deployments (no long-lived cloud keys).
* [ ] Branch protections + required checks (secret scan, build, tests).
* [ ] SCA and code scanning enabled; alerts triaged.
* [ ] Rotation schedule documented; break-glass steps defined.
* [ ] Logs redacted; no PII or secrets; TLS enforced.

---

## ❓ Frequently Asked Questions (FAQ)

* **Are Firebase config values secrets?**
  No. Treat them as public; protect data with rules and server-side enforcement.

* **Can we store `.env` in Git with encryption?**
  Prefer environment or secret manager. If using encrypted files (e.g., Mozilla SOPS), restrict keys and audit decryption use.

* **How do we verify no secrets are printed?**
  Mask known patterns in CI; add a “grep deny list” step and fail on matches.

---

## 🔗 See also
> [!IMPORTANT]
> The links below point to files in /docs/. If you rename or move a file, update every reference across the repo to prevent link drift.

### Introduction
Get set up quickly and understand how this repo is organized and why. This section orients new contributors, aligns everyone on our principles, and ensures your local environment matches what CI expects. By the end, you’ll know the repo layout, standards, and how to make your first safe change.

- [🎯 About This Repository](/README.md)
- [🚀 Project Initialization (Day 0)](/docs/introduction/%F0%9F%9A%80%20Project%20Initialization%20%28Day%200%29.md)
- [🛠️ Environment & Technologies](/docs/introduction/%F0%9F%9B%A0%EF%B8%8F%20Environment%20%26%20Technologies.md)
- [🌟 Guiding Principles](/docs/introduction/%F0%9F%8C%9F%20Guiding%20Principles.md)
- [🧠 GitHub Concepts Recap](/docs/introduction/%F0%9F%A7%A0%20GitHub%20Concepts%20Recap.md)

### Workflow
Show how code moves from idea to production through small, reviewable changes. This is our end-to-end flow: branch naming, commit habits (including AI assist), PR etiquette, CI gates, testing layers, and how we cut and version releases. Follow this to keep changes fast, traceable, and low-risk.

- [🌿 Branching Strategy & Workflow](/docs/distribution/%F0%9F%8C%BF%20Branching%20Strategy%20%26%20Workflow.md)
- [🤝 Pull Requests & Code Reviews](/docs/distribution/%F0%9F%A4%9D%20Pull%20Requests%20%26%20Code%20Reviews.md)
- [⚙️ Continuous Integration & Delivery](/docs/distribution/%E2%9A%99%EF%B8%8F%20Continuous%20Integration%20%26%20Delivery.md)
- [🧪 Testing Strategy](/docs/distribution/%F0%9F%A7%AA%20Testing%20Strategy.md)
- [🚢 Releases & Versioning](/docs/distribution/%F0%9F%9A%A2%20Releases%20%26%20Versioning.md)
- [🤖 AI-Driven Commit Process](/docs/distribution/%F0%9F%A4%96%20AI%E2%80%91Driven%20Commit%20Process.md)

### Operations
Keep the repository healthy over time. These practices harden security (branch protections, secret handling), reduce supply-chain risk (deps), and maintain repo quality (hygiene, troubleshooting). Use these docs when changing guardrails, rotating secrets, upgrading dependencies, or diagnosing issues in production pipelines.

- [🛡️ Branch Protection](/docs/operations/%F0%9F%9B%A1%EF%B8%8F%20Branch%20Protection.md)
- [🔐 Security & Secrets](/docs/operations/%F0%9F%94%90%20Security%20%26%20Secrets.md)
- [📦 Dependency Management](/docs/operations/%F0%9F%93%A6%20Dependency%20Management.md)
- [🧩 Repository Hygiene](/docs/operations/%F0%9F%A7%A9%20Repository%20Hygiene.md)
- [🧯 Troubleshooting](/docs/operations/%F0%9F%A7%AF%20Troubleshooting.md)

### References
Fast lookups you’ll reuse daily—keep these open in a tab. Commands and definitions that support the above processes without re-explaining the “why.” Use this section to unblock yourself quickly while working through Introduction, Workflow, or Operations.

- [⌨️ Git Commands](/docs/references/%E2%8C%A8%EF%B8%8F%20Git%20Commands.md)
- [📘 Glossary](/docs/references/%F0%9F%93%98%20Glossary.md)
