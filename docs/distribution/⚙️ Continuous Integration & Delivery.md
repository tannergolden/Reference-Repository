# ⚙️ Continuous Integration & Delivery

A lean, reliable pipeline that proves code is safe **before** it lands and promotes only on purpose: **Development → Preview → Release**.

> [!IMPORTANT]
> Expand acronyms on first use (e.g., Continuous Integration (CI), Continuous Delivery (CD)). Make **`Development`** the default branch, enforce required checks, and **never** push directly to protected branches.

> [!CAUTION]
> Secrets must not be committed or echoed in logs. Store them in **GitHub Environments** with reviewers/approvals and least privilege.

---

## 🎯 Goals
- **Fast feedback** on every Pull Request (PR): install → lint → test → build.
- **Deterministic builds** with pinned versions and lockfiles.
- **Intentional promotions**: `Development` (integrate) → `Preview` (validate) → `Release` (production).

---

## 🗺️ Branch → Environment Map

| Branch | Purpose | GitHub Environment | Typical Action |
|---|---|---|---|
| `Development` | Team integration line | `Development` | CI only (no deploy by default) |
| `Preview` | Staging/User Acceptance Testing | `Preview` | Deploy preview on push |
| `Release` | Production line (tagged) | `Release` | Build, tag, create release, deploy |

> [!TIP]
> If you need a dev sandbox, add a lightweight deploy on `Development`, but keep it optional to preserve speed.

---

## 🔍 Required Checks (set in Branch Protection)

| Check | Why it exists |
|---|---|
| **Install (npm ci)** | Reproducible dependency graph via lockfile |
| **Lint (ESLint/Prettier)** | Catch bugs/style drift early |
| **Test (–-ci)** | Unit/integration reliability |
| **Build** | Verifies production bundle compiles |
| **(Optional) Typecheck** | Static guarantees (e.g., `tsc --noEmit`) |
| **(Optional) Security / SCA** | Secret scan + dependency issues |

Make the combined job name (e.g., `ci / build-test`) a **required status check** on `Development`, `Preview`, and `Release`.

---

## 🧭 Pipeline Overview

```mermaid
flowchart LR
  A[PR opened to Development] --> B[CI: install / lint / test / build]
  B --> C{All checks pass?}
  C -- yes --> D[Review & approvals]
  D --> E[Squash merge into Development]
  E --> F[Promote: Development -> Preview]
  F --> G[Validate on Preview]
  G --> H[Promote: Preview -> Release]
````

*(Use PRs for promotions; never push directly.)*

---

## 📦 Standard CI (for PRs and Development)

Create `.github/workflows/ci.yml`:

```yaml
name: ci
on:
  pull_request:
    branches: [Development]
  push:
    branches: [Development]
concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true
jobs:
  build-test:
    runs-on: ubuntu-latest
    permissions:
      contents: read
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: actions/setup-node@v4
        with:
          node-version-file: '.nvmrc'
          cache: 'npm'
      - name: Install (lockfile)
        run: npm ci
      - name: Lint
        run: npm run lint --if-present
      - name: Test (CI)
        run: npm test -- --ci --reporter=default
      - name: Build
        run: npm run build --if-present
      # Optional: upload build artifacts for later jobs or manual QA
      - name: Upload artifact
        if: success()
        uses: actions/upload-artifact@v4
        with:
          name: build-output
          path: dist
          if-no-files-found: ignore
```

**Design choices**

* **`concurrency`** cancels stale CI when you push new commits.
* **`node-version-file`** reads `.nvmrc` to keep local/CI aligned.
* **`npm ci`** enforces reproducible installs from lockfile.
* Artifacts are optional; keep CI quick for fast reviews.

---

## 🌤️ Preview Deploy (on push to Preview)

Create `.github/workflows/preview-deploy.yml`:

```yaml
name: preview-deploy
on:
  push:
    branches: [Preview]
concurrency:
  group: preview-${{ github.ref }}
  cancel-in-progress: true
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: Preview
      url: ${{ steps.deploy.outputs.url }}
    permissions:
      contents: read
      id-token: write # if using cloud OIDC
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version-file: '.nvmrc'
          cache: 'npm'
      - run: npm ci
      - run: npm run build --if-present
      # Replace with your deploy command/provider (e.g., Firebase Hosting, Vercel CLI, Cloud Run)
      - name: Deploy
        id: deploy
        run: |
          echo "url=https://preview.example.com" >> "$GITHUB_OUTPUT"
```

> \[!NOTE]
> Guard the **Preview** environment with reviewers in **Settings → Environments** if you need approvals before rollout.

---

## 🚢 Release Tagging & Deploy (on push to Release)

Create `.github/workflows/release.yml`:

```yaml
name: release
on:
  push:
    branches: [Release]
concurrency:
  group: release-${{ github.ref }}
  cancel-in-progress: true
jobs:
  tag-and-release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      id-token: write
    environment:
      name: Release
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: actions/setup-node@v4
        with:
          node-version-file: '.nvmrc'
          cache: 'npm'
      - run: npm ci
      - run: npm run build --if-present

      - name: Determine version from package.json
        id: version
        run: echo "value=v$(node -p \"require('./package.json').version\")" >> "$GITHUB_OUTPUT"

      - name: Create tag if missing
        run: |
          git fetch --tags
          if [ -z "$(git tag -l '${{ steps.version.outputs.value }}')" ]; then
            git tag '${{ steps.version.outputs.value }}'
            git push origin '${{ steps.version.outputs.value }}'
          fi

      - name: Create GitHub Release
        uses: actions/create-release@v1
        with:
          tag_name: ${{ steps.version.outputs.value }}
          release_name: ${{ steps.version.outputs.value }}
          body: "Automated release generated from Conventional Commits."
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      # Replace with your production deploy command/provider
      - name: Deploy to production
        run: |
          echo "Deploying to production..."
```

> \[!TIP]
> Use your Conventional Commits to generate changelogs automatically (e.g., via a release script or a dedicated action).

---

## 🧰 Quality Gates & Speed Tips

* **Keep CI deterministic**: `.nvmrc`, lockfiles, pinned tool versions.
* **Cache wisely**: `actions/setup-node` npm cache + avoid caching `node_modules`.
* **Fail fast**: run lint/tests before build if build is expensive.
* **Matrix** (optional): test multiple Node.js Long-Term Support (LTS) versions only if you support them.
* **Short logs**: redact secrets; avoid verbose debug unless failing.

---

## 🔐 Secrets, Permissions, & Approvals

* Store secrets in **GitHub Environments** (`Development`, `Preview`, `Release`).
* Use **OpenID Connect (OIDC)** (`id-token: write`) to issue cloud tokens without static keys when possible.
* Require **reviewers** for **Preview**/**Release** environments to gate deployments.
* Restrict who can trigger promotions (CODEOWNERS + environment rules).

---

## 🔄 Promotions & Rollbacks

* Promote by PR: **Development → Preview** then **Preview → Release**.
* Tag after promoting to `Release`.
* Roll back by **reverting the PR** or **redeploying the previous tag**; keep artifacts for a short retention window.

---

## 🧪 Health & Observability (recommended)

* Add smoke tests after deploy (ping health endpoint, basic navigation).
* Publish build provenance/attestations if your platform supports it.
* Emit release metadata (version, commit SHA) to logs/telemetry.

---

## ❓ Frequently Asked Questions (FAQ)

* **Why use `npm ci` instead of `npm install` in CI?**
  It installs exactly what the lockfile specifies, ensuring reproducibility.

* **How do I block deploys on failing checks?**
  Make the CI job a **required status check**, and set environment **required reviewers**.

* **Where should I put provider-specific steps?**
  In the `Deploy` steps of `preview-deploy.yml` and `release.yml`—those are intentionally pluggable.

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
