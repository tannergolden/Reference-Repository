# 🚀 Project Initialization (Day 0)

Spin up a fresh repository from the template and lock in the workflow from **Day 0**—branches, protections, Continuous Integration (CI), environments, and your first deploy.

> [!IMPORTANT]
> Make **`Development`** the default branch, and **never** push directly to protected branches (`Development`, `Preview`, `Release`). Use Pull Requests (PRs) with required checks.

> [!CAUTION]
> **Secrets** must not live in Git. Store them in GitHub Actions **Environments** (with Role-Based Access Control (RBAC), Single Sign-On (SSO), and Multi-Factor Authentication (MFA)) or a cloud secret manager.

---

## 🎯 Outcome (Day 0)

| Area | State by end of Day 0 |
|---|---|
| Branches | `Development` (default), `Preview`, `Release` created and protected |
| CI | Install → Lint → Test → Build pipeline green on PRs to `Development` |
| Environments | GitHub Environments **Development**, **Preview**, **Release** configured with secrets |
| Governance | CODEOWNERS, PR template, Conventional Commits, required reviews |
| Docs | “About This Repository” updated; key docs added/linked |
| Deploy | First deploy to **Preview** validated; optional initial **Release** tag `v0.1.0` |

---

## 🧰 Prerequisites

- Access to the GitHub organization and the template repository.
- Local toolchain installed (see **Environment & Technologies**): Git, Node.js Long-Term Support (LTS) via Node Version Manager (nvm) or asdf, Firebase Command Line Interface (CLI), optional Google Cloud Software Development Kit (SDK).
- Project identifiers and secrets ready (non-production placeholders are fine for Day 0).

---

## 1) Create a repository from the template

1. Click **Use this template** → **Create a new repository**.  
2. Choose the **organization**, set a **repository name**, set **visibility**, and create.
3. Clone locally:
   ```bash
   git clone <repo-url>
   cd <repo>
   ```

> \[!TIP]
> After cloning, run `nvm use` (reads `.nvmrc`) and `npm ci` to match Continuous Integration (CI).

---

## 2) Establish the branch model

1. **Rename** the default `main` branch to **`Release`**.
2. **Create** branches **`Preview`** and **`Development`**.
3. **Set `Development` as default** branch in repository settings.

> \[!IMPORTANT]
> Day-to-day integration happens via PRs into **`Development`**. Promotion path: **Development → Preview → Release**.

---

## 3) Protect long-lived branches

Add protection rules for `Development`, `Preview`, and `Release`:

| Rule                                     | Setting                                   |
| ---------------------------------------- | ----------------------------------------- |
| Require PR before merge                  | ✅                                         |
| Required status checks                   | ✅ install, lint, test, build              |
| Require branches up to date before merge | ✅                                         |
| Required reviews                         | ✅ at least 1; CODEOWNERS where applicable |
| Restrict who can push                    | ✅ maintainers/release managers only       |
| Signed commits (optional)                | ✅                                         |

> \[!WARNING]
> Do not allow **force pushes** on `Preview` or `Release`. Preserve history for auditability and rollback.

---

## 4) Configure CI (GitHub Actions)

Create `.github/workflows/ci.yml`:

```yaml
name: ci
on:
  pull_request:
    branches: [Development]
  push:
    branches: [Development]
jobs:
  build-test:
    runs-on: ubuntu-latest
    permissions:
      contents: read
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version-file: '.nvmrc'
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm test -- --ci
      - run: npm run build
```

> \[!TIP]
> Keep local commands and CI identical (install → lint → test → build) to avoid “works on my machine”.

---

## 5) Set up GitHub Environments & secrets

Create Environments **Development**, **Preview**, **Release** and add required **secrets/variables**:

| Environment | Typical Secrets / Vars                        | Protections                                                  |
| ----------- | --------------------------------------------- | ------------------------------------------------------------ |
| Development | `FIREBASE_PROJECT_ID_DEV`, test API keys      | Optional reviewer gates                                      |
| Preview     | `FIREBASE_PROJECT_ID_PREVIEW`, service tokens | Require reviewers; restrict deployers                        |
| Release     | `FIREBASE_PROJECT_ID_PROD`, production keys   | Require reviewers; manual approval; (optional) deploy freeze |

Map your deploy workflow to these environments (e.g., merges into `Preview` deploy there; promotions to `Release` require approval).

---

## 6) Governance & repository hygiene

* **CODEOWNERS**: create `.github/CODEOWNERS` to route reviews.
* **PR template**: add `.github/pull_request_template.md` with risk/rollback/links.
* **Commit style**: enforce **Conventional Commits** (e.g., `feat(auth): add passwordless login`) with commitlint (and optionally GemCommit).
* **Branch naming**: `feature/<topic>`, `bugfix/<ticket>`, `hotfix/<issue>`, `experiment/<idea>`.

Example (JavaScript/TypeScript):

```jsonc
// commitlint.config.cjs
module.exports = { extends: ["@commitlint/config-conventional"] };
```

---

## 7) Local bootstrap (developer workstation)

```bash
# Match the toolchain
nvm use
npm ci

# Optional: start emulators
npm run serve   # or: firebase emulators:start

# Lint / test locally
npm run lint
npm test
```

---

## 8) First PR (from a feature branch)

1. Create a branch: `git switch -c feature/day0-setup`.
2. Update **About This Repository**, fill owners, confirm docs.
3. Add/confirm **CODEOWNERS**, **PR template**, **commitlint**.
4. Push and open a **Pull Request (PR)** into `Development`.
5. Ensure CI passes; request review; **squash merge** on approval.

> **Definition of Done (PR)**
> Single intent • Lint/Test/Build green • Clear title/description • Risks and rollback noted • Linked ticket/docs

---

## 9) First deploy to Preview

* Merge a change from `Development` into `Preview` via PR (or a dedicated “promote to Preview” workflow).
* CI deploys to **Preview** environment using its secrets.
* Run a quick **Quality Assurance (QA)** smoke test (basic navigation, auth, one critical flow).

---

## 10) Promote to Release and tag

* With QA approval, promote **Preview → Release** (PR or manual promotion job).
* Tag the release: `v0.1.0` and publish notes (changes since last tag).
* Back-merge `Release` into `Development` to keep lines consistent.

> \[!TIP]
> Automate changelogs from Conventional Commits. Many tools can generate release notes directly from commit history.

---

## ✅ Day 0 Checklist

* [ ] `Development` set as default; `Preview`, `Release` created
* [ ] Branch protections enforced on all long-lived branches
* [ ] CI pipeline green (install → lint → test → build)
* [ ] Environments created with secrets and protections
* [ ] CODEOWNERS and PR template in place
* [ ] Conventional Commits enforced (commitlint/GemCommit)
* [ ] First PR merged to `Development`
* [ ] First deploy validated on **Preview**
* [ ] Promotion path to **Release** tested and tagged

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
