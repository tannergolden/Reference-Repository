# 🧩 Repository Hygiene

Keep the repository tidy, predictable, and easy to onboard: clear layout, consistent naming, zero secrets, and automation that enforces the rules across **Experimental → Development → Preview → Release**.

> [!CAUTION]
> Do **not** commit generated artifacts, local environment files, or secrets. Prefer build outputs from CI (Continuous Integration), `.env.example` placeholders, and environment-scoped secrets.

---

## 🎯 Goals
- **Predictable structure** that every contributor recognizes.
- **Consistent conventions** for files, branches, and commits.
- **No noise**: exclude build outputs, large binaries, and transient files.
- **Automated enforcement** via linters, hooks, and branch protection.

---

## 🗂️ Standard Layout (baseline)

Pick one documentation folder and stick to it: `docs/` **or** `documents/`. If you use plain-text (`*.txt`) docs, keep the same names and relative links.

```

/ (repo root)
├─ README.md                       # Overview + quick links
├─ LICENSE                         # License text (any SPDX)
├─ SECURITY.md                     # Security policy & contacts
├─ CONTRIBUTING.md                 # How to contribute
├─ CODEOWNERS                      # Review ownership
├─ docs/ or documents/             # Project docs (md)
├─ src/ | app/ | packages/         # Source code
├─ test/ | tests/                  # Unit/integration tests
├─ .github/
│  ├─ workflows/                   # CI/CD pipelines
│  ├─ ISSUE\_TEMPLATE/              # Issue templates
│  └─ pull\_request\_template.md     # PR template
├─ .editorconfig                   # Editor defaults
├─ .gitattributes                  # EOL/merge/linguist
├─ .gitignore                      # Exclusions
├─ .nvmrc                          # Node.js Long-Term Support (LTS) version
└─ commitlint.config.cjs           # Conventional Commits policy

````

---

## 🧱 Required Root Files

| File | Purpose | Notes |
|---|---|---|
| `README.md` | Orientation and links | Keep short; link into docs/documents |
| `LICENSE` | Legal terms | Use an SPDX-compatible text |
| `SECURITY.md` | Report/vuln policy | Contacts, timelines, scope |
| `CONTRIBUTING.md` | How to contribute | Branching, tests, review rules |
| `CODEOWNERS` | Review routing | Small, explicit ownership sets |
| `.editorconfig` | Formatting defaults | Tabs/spaces, charset, line endings |
| `.gitattributes` | Normalization rules | `* text=auto eol=lf` + merge hints |
| `.gitignore` | Exclude noise | Build, cache, local env files |
| `.nvmrc` | Node.js version pin | Aligns local dev with CI |
| `commitlint.config.cjs` | Message rules | Enforce Conventional Commits |

> [!TIP]
> Add an **Architecture Decision Record (ADR)** index under `docs/` or `documents/` to record non-obvious choices.

---

## 🔤 Naming Conventions

| Thing | Rule | Examples |
|---|---|---|
| Files & dirs | Kebab-case for web projects; PascalCase for components | `user-profile.ts`, `AppHeader.tsx` |
| Tests | Mirror source path; suffix `.test` or `.spec` | `user-profile.test.ts` |
| Env files | Never commit real values; provide `.env.example` | `API_BASE_URL=…` |
| Branches | `feature/*`, `bugfix/*`, `hotfix/*`, `experiment/*`, `integration/*` | `feature/auth-magic-links` |
| Commits | Conventional Commits | `feat(auth): add magic links` |

---

## 🚫 Ignore the Right Things

**`/.gitignore` essentials (JavaScript/Web):**
```gitignore
# Node & tooling
node_modules/
npm-debug.log*
yarn*.log
.pnpm-store/
.vscode/
.idea/

# Build & caches
dist/
build/
.cache/
coverage/
*.tsbuildinfo

# Env & secrets (placeholders only in .env.example)
.env
.env.*.local
firebase-debug.log

# OS cruft
.DS_Store
Thumbs.db
````

---

## ↔️ Line Endings, Merge Hints & Linguist

Use `.gitattributes` to normalize line endings (LF) and help Git with merges:

```gitattributes
* text=auto eol=lf

# Treat lockfiles and snapshots as text but avoid noisy diffs
package-lock.json -diff
*.snap -diff

# Mark generated assets
*.min.js linguist-generated
*.map linguist-generated
```

---

## ✏️ Editor Defaults

`.editorconfig` keeps contributors aligned regardless of IDE:

```editorconfig
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
indent_style = space
indent_size = 2

[*.md]
trim_trailing_whitespace = false
```

---

## 🧹 Generated & Large Files

* **Do not commit** compiled bundles, minified assets, or vendor artifacts. Build in CI.
* For large binaries (datasets, media): prefer external storage or **Git Large File Storage (LFS)** **only if necessary** and scoped. Document why and how.

| Asset               | Recommended home                                     |
| ------------------- | ---------------------------------------------------- |
| Build output        | CI artifacts, not Git                                |
| Test fixtures (big) | Compressed under `test/fixtures/` or external bucket |
| Design assets       | Design tool source/links, not repo binaries          |

---

## 🤖 Automation & Hooks

* **Pre-commit**: lint staged files; fix formatting (Prettier/ESLint).
* **Commit message**: commitlint enforces Conventional Commits (use **GemCommit** in Visual Studio Code (VS Code) to draft).
* **CI required checks**: install → lint → test → build must be green before merge.
* **Auto-delete head branches** after merge (repo setting).

`package.json` (excerpt)

```jsonc
{
  "scripts": {
    "prepare": "husky",
    "lint": "eslint .",
    "format": "prettier -w .",
    "test": "vitest run"
  },
  "lint-staged": {
    "*.{js,jsx,ts,tsx,css,md}": ["prettier -w", "eslint --fix"]
  }
}
```

---

## 🏷️ Labels, Templates & Shields

* Standardize **labels**: `type:feature`, `type:bug`, `priority:high`, `area:<module>`.
* Provide **issue/PR templates** under `.github/` to standardize context.
* Use a few **README shields** (build, coverage, license) to signal status—avoid clutter.

---

## 🧹 Stale Branch & Issue Policy

* **Auto-delete** merged branches (repo setting).
* Close branches older than **90 days** with no activity (except long-lived).
* Triage issues weekly; label, assign, or close with rationale.

---

## 📜 Licensing & Notices

* Ensure `LICENSE` exists and matches your intent (e.g., MIT, Apache-2.0).
* If distributing binaries, maintain `THIRD_PARTY_NOTICES` or a Software Bill of Materials (SBOM).

---

## 🔐 Secrets & Sensitive Data (cross-check)

* Real secrets belong in **GitHub Environments** or a cloud secret manager.
* Include only placeholders in `.env.example`.
* Add secret scanning in CI and treat confirmed findings as blockers.

---

## 🧾 ADRs (Architecture Decision Records)

* Store under `docs/adrs/` or `documents/adrs/`.
* Use a numbered format (`0001-title.md`) with context, decision, consequences.
* Link ADRs from relevant PRs and the README.

---

## ✅ New Repo Hygiene Checklist

* [ ] Root files present: README, LICENSE, SECURITY, CONTRIBUTING, CODEOWNERS.
* [ ] `.editorconfig`, `.gitattributes`, `.gitignore`, `.nvmrc` committed.
* [ ] `docs/` or `documents/` chosen and consistent; links verified.
* [ ] CI pipeline enforces install → lint → test → build.
* [ ] Commitlint + Husky set up; GemCommit documented in README.
* [ ] Branch protection configured (`Development`, `Preview`, `Release`).
* [ ] Issue/PR templates added; labels created.
* [ ] Secret scanning, code scanning, and SCA enabled.

---

## 🔄 Ongoing Hygiene (weekly)

* [ ] Merge or close stale PRs; delete merged branches.
* [ ] Triage open issues; ensure owners and labels.
* [ ] Run dependency updates (Dependabot/Renovate) in **small batches**.
* [ ] Prune unused files and outdated docs; update ADR index.
* [ ] Verify CI times; keep logs concise and secrets redacted.

---

## 🧯 Common Pitfalls (and fixes)

| Symptom                        | Likely cause                   | Fix                                                     |
| ------------------------------ | ------------------------------ | ------------------------------------------------------- |
| “Works on my machine” failures | Tool/version drift             | `.nvmrc` + `npm ci` + EditorConfig enforced             |
| Noisy diffs on lockfiles       | Platform EOL drift             | `.gitattributes` with `* text=auto eol=lf`              |
| Binaries in Git                | Missing ignores                | Update `.gitignore`; purge via filter-repo if necessary |
| Confusing docs folder          | Mixed `docs/` and `documents/` | Choose one; add redirect/readme explaining structure    |
| Long PRs hard to review        | Mixed concerns                 | Split into feature/refactor/docs/test PRs               |
| Secrets leaked in PR           | Local `.env` committed         | Rotate/revoke + add ignores + enable secret scanning    |

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
