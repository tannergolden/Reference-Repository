# 📦 Dependency Management

Reliable, auditable, and repeatable dependency practices for fast builds and safe releases across **Experimental → Development → Preview → Release**.

> [!IMPORTANT]
> Use a **lockfile** (`package-lock.json`) and **Node.js Long-Term Support (LTS)** pinned via `.nvmrc`. In **Continuous Integration (CI)** use `npm ci` (not `npm install`) for reproducible installs.

> [!CAUTION]
> Treat supply chain risk as product risk. Review new packages, watch **Software Composition Analysis (SCA)** alerts, and never commit or echo secrets.

---

## 🎯 Objectives
- **Reproducible**: identical installs locally and in CI/CD (Continuous Delivery).
- **Secure**: monitored with SCA, least privilege tokens, and vetted sources.
- **Up-to-date**: small, regular upgrades instead of surprise mega-bumps.
- **Compliant**: license policy enforced; third-party attributions tracked.

---

## 🧱 Package Types & When to Use

| Field | Use for | Ships to production? | Examples |
|---|---|:--:|---|
| `dependencies` | Runtime code needed by the app | ✅ | HTTP client, UI lib |
| `devDependencies` | Tooling for build/test/lint | ❌ | TypeScript, ESLint, Vitest |
| `peerDependencies` | Libraries expecting the host to provide a version | ⚠️ | React plugins |
| `optionalDependencies` | Best-effort installs, non-critical | ⚠️ | Native extras |

> [!NOTE]
> Applications rarely need `peerDependencies`; libraries do. Prefer `devDependencies` for build/test tools.

---

## 🔐 Supply Chain Guardrails

| Control | Practice |
|---|---|
| **Lockfile discipline** | Commit `package-lock.json`. Never hand-edit. |
| **Node.js pinning** | `.nvmrc` (and `"engines"` in `package.json`) to keep versions aligned. |
| **Registry source** | Default to the public registry; audit any private mirrors/proxies. |
| **Postinstall scripts** | Avoid packages with risky scripts; review before adding. |
| **SCA** | Enable Dependabot alerts or Renovate plus CI scans. |
| **Provenance** | Prefer signed commits/tags; record build SHA in app. |

---

## 🗓️ Update Strategy (Cadence & Scope)

| Cadence | What to update | Scope | Who |
|---|---|---|---|
| Weekly | Patch updates | Auto-PRs in small batches | Bot + code owner |
| Bi-weekly | Minor updates | Grouped by area (e.g., testing) | Owners |
| As needed | Major updates | One PR each; migration notes | Owners + reviewers |
| Immediately | Critical security patches | Single-issue PR | Owners + release manager |

> [!TIP]
> Keep updates **small and frequent**. Run through **Experimental** first if risky.

---

## 🧪 CI Install & Caching (baseline)

**CI installation (deterministic):**
```yaml
- uses: actions/setup-node@v4
  with:
    node-version-file: '.nvmrc'
    cache: 'npm'
- name: Install (lockfile)
  run: npm ci
````

**Local developer parity:**

```bash
nvm use        # reads .nvmrc
npm ci         # exact lockfile install
npm run build  # same steps as CI
```

---

## ⚖️ SemVer (Semantic Versioning) & Ranges

* Prefer **pinned by lockfile**; ranges in `package.json` are fine but rely on the lockfile for exact versions.
* Avoid broad ranges like `"*"` or `">=1.0.0"`. Prefer `^` for libraries you trust, or exact versions for high-risk deps.
* For breaking changes, follow release notes and include migration steps in the PR.

---

## 🤖 Automation (Dependabot / Renovate)

### Option A — Dependabot (GitHub-native)

`.github/dependabot.yml`

```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule: { interval: "weekly", day: "monday", time: "07:00" }
    open-pull-requests-limit: 5
    versioning-strategy: increase
    groups:
      tooling:
        patterns: ["eslint*", "typescript", "vitest*", "@types/*"]
      runtime:
        patterns: ["react*", "firebase*", "axios*", "zod*"]
```

### Option B — Renovate (more control)

`renovate.json`

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["config:recommended"],
  "rangeStrategy": "bump",
  "labels": ["deps"],
  "packageRules": [
    { "groupName": "tooling", "matchPackagePatterns": ["^eslint", "typescript", "vitest", "^@types/"] },
    { "groupName": "runtime", "matchPackagePatterns": ["^react", "^firebase", "^axios", "^zod"] }
  ],
  "prHourlyLimit": 2,
  "prConcurrentLimit": 5,
  "stabilityDays": 2
}
```

> \[!TIP]
> Cap concurrent PRs to keep noise low. Group non-breaking updates by area.

---

## 🧰 Security & Audits

* **Software Composition Analysis (SCA):**

  * Enable GitHub Dependabot alerts (repository → *Security* → *Dependabot alerts*).
  * Optionally run `npm audit` or a third-party SCA in CI (non-blocking informational job).
* **Policy:**

  * Block merges for **critical** vulnerabilities affecting runtime.
  * Document accepted risks with a ticket and time-boxed follow-up.
* **Tokens & secrets:**

  * Never place registry tokens or service keys in `package.json` scripts.
  * Use **GitHub Environments** for secrets with reviewers/approvals.

CI snippet (informational scan):

```yaml
- name: Audit (informational)
  run: npm audit --audit-level=high || true
```

---

## 📜 Licenses & Attribution

| Control   | Practice                                                                                  |
| --------- | ----------------------------------------------------------------------------------------- |
| Allowlist | Permissive (MIT, BSD, Apache-2.0) preferred for runtime.                                  |
| Review    | Strong copyleft (GPL-3.0, AGPL-3.0) requires approval; avoid for client bundles.          |
| Tracking  | Generate **Software Bill of Materials (SBOM)** and keep `THIRD_PARTY_NOTICES` up to date. |

Generate SBOM (example using CycloneDX CLI):

```bash
npx @cyclonedx/cyclonedx-npm --output-format json --output-file sbom.json
```

---

## ➕ Adding / Upgrading a Dependency (PR checklist)

1. **Justify need** (feature, performance, security) and alternatives considered.
2. **Review risk**: maintainer activity, weekly downloads, open issues, license.
3. Add with `npm i <pkg>` (or `-D` for dev tools); commit updated lockfile.
4. Run locally: `npm ci && npm run lint && npm test && npm run build`.
5. Add or update tests/docs.
6. Open PR → **Development** (or **Experimental** first if risky); request review from code owners.
7. If runtime impact: smoke test on **Preview** before promote.

---

## 🧯 Troubleshooting (deps)

| Symptom                              | Likely cause                          | Fix                                                                 |
| ------------------------------------ | ------------------------------------- | ------------------------------------------------------------------- |
| Build passes locally but fails in CI | Node.js mismatch / transitive changes | `nvm use` + `npm ci`; pin Node.js in CI                             |
| `ERESOLVE` conflicts                 | Incompatible ranges                   | Pin direct dep; consider overrides/resolutions                      |
| Native module compile errors         | Missing build tools                   | macOS: Xcode Command Line Tools; Linux: `build-essential`           |
| Bot PR flood                         | Too many parallel updates             | Lower PR limits; group packages; weekly cadence                     |
| Unexpected behavior after minor bump | Hidden breaking change                | Revert to previous lockfile; open upstream issue; pin exact version |

---

## 📏 Example `package.json` (relevant fields)

```jsonc
{
  "name": "my-app",
  "version": "1.4.0",
  "packageManager": "npm@10.8.2",
  "engines": { "node": ">=18 <21" },
  "scripts": {
    "lint": "eslint .",
    "test": "vitest run",
    "build": "tsc -p tsconfig.build.json && vite build"
  }
}
```

---

## ✅ Definition of Done (Dependencies)

* Lockfile committed; CI uses `npm ci`.
* SCA alerts triaged; no critical runtime vulns outstanding.
* Weekly/bi-weekly updates merged in small batches.
* Licenses compliant; SBOM generated for releases (recommended).
* Rollback path: prior lockfile/tag can be redeployed quickly.

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
