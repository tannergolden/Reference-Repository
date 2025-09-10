# 📘 Glossary
An A–Z glossary for this repository that standardizes the terms used in code, docs, issues, and workflows. Each entry provides a brief, plain-language definition and, where useful, examples or cross-references to related concepts. Acronyms and initialisms are spelled out at first mention and included as aliases (e.g., “Continuous Integration (CI)”). Use this glossary to keep commit messages, pull requests, and release notes consistent across the project.

---

## A

- **Accessibility (a11y)** — Inclusive design and testing practices that ensure software is usable by people with disabilities (e.g., screen readers, keyboard nav).
- **Action (GitHub Action)** — A reusable step packaged as code for GitHub Actions workflows.
- **Actions Cache** — Saved files between workflow runs to speed up installs/builds.
- **Actions Runner** — Host that executes workflow jobs (GitHub-hosted or self-hosted).
- **Advanced Security (GHAS)** — GitHub features like CodeQL, secret scanning, dependency review.
- **API Rate Limit** — Caps on API requests; inspect `X-RateLimit-*` headers to plan usage.
- **Architecture Decision Record (ADR)** — Short document capturing a significant engineering decision, its context, options, and consequences.
- **Artifact** — Build output produced by automation (e.g., compiled bundle, test report) that can be stored, downloaded, or promoted.
- **Approval Gate** — Required review step (e.g., environment protection rule) before deployment.

## B

- **Back-merge** — After a hotfix lands on `Release`, merge those changes back into `Development` (and `Preview` if diverged) to keep histories aligned.
- **Badge** — Status indicator (e.g., build passing) embedded in README.
- **Base Branch** — The branch a PR targets (e.g., `Development`).
- **Blame (git blame)** — Line-by-line commit attribution to find when/why code changed.
- **Breaking Change** — Backward-incompatible change; call out in Conventional Commits and release notes.
- **Branch Naming Convention** — Agreed pattern for branches (e.g., `feat/*`, `fix/*`).
- **Branch Protection** — GitHub rules that require Pull Requests (PRs), status checks, and approvals before merging into protected branches.
- **Build** — Step that compiles/transforms source code into deployable artifacts.
- **Bot Account** — Automation-scoped identity used for CI/CD actions.

## C

- **Cache Key** — String controlling Actions cache reuse/restore behavior.
- **Change Log (Changelog)** — Human-readable list of notable changes between versions; ideally generated from Conventional Commits.
- **Checks API** — API surfacing CI results (annotations, statuses) on commits/PRs.
- **Cherry-pick** — Apply a specific commit onto another branch.
- **Code Owner (CODEOWNERS)** — GitHub feature that routes review requests to specific owners based on file paths.
- **Code Quality Gate** — Required check (install → lint → test → build) that must pass before merging.
- **CodeQL** — Static analysis engine used for code scanning in GHAS.
- **Commit Message (Conventional Commits)** — Standard format: `type(scope): summary` used to automate semantic versioning and release notes.
- **Composite Action** — Action composed of multiple shell steps; defined in `action.yml`.
- **Container Registry (GHCR)** — GitHub Packages registry for OCI images.
- **Continuous Delivery (CD)** — Automated delivery of validated builds to environments (Preview/Release) with approvals where needed.
- **Continuous Integration (CI)** — Automated checks on every change: install, lint, test, build.
- **Content Security Policy (CSP)** — Browser security header that restricts resource loading to reduce Cross-Site Scripting (XSS).
- **Coverage** — Percentage of code exercised by tests (lines, branches, functions).
- **Cron Schedule** — `on.schedule` trigger for time-based workflow runs.

## D

- **DAST (Dynamic Application Security Testing)** — Black-box scanning of a running app for security issues.
- **Dashboard (Projects)** — GitHub Projects (v2) board for planning/triage using issues/PRs.
- **Dependency** — Third-party package the project relies on at runtime (`dependencies`) or for tooling (`devDependencies`).
- **Dependency Review** — GHAS diff view showing new/removed/updated dependencies and known risks.
- **Dependabot** — Bot that raises security/update PRs for dependencies.
- **Deployment** — Record of a release to an environment with status (in progress, success, failure).
- **Diff** — The set of changed lines/files between two commits or branches.
- **Discussions** — Forum-style Q&A/announcements tied to the repo/community.
- **Dry Run** — Execute validation steps without applying changes (e.g., `terraform plan`).
- **DORA Metrics** — Delivery KPIs: lead time, deployment frequency, MTTR, change failure rate.

## E

- **End-to-End (E2E) Test** — Validates user-critical flows in a real or headless browser against a deployed app.
- **Emulator** — Local fake of a remote service (e.g., Firebase Emulators for Firestore/Auth/Functions) used in Integration tests.
- **Encrypted Secret** — Encrypted value available to workflows (repo/org/environment scopes).
- **Environment** — Isolated runtime context with its own config and secrets: `Experimental`, `Development`, `Preview`, `Release`.
- **Environment Protection Rules** — Required reviewers, wait timers, or branch restrictions before deploying.
- **Environment Secret** — Secret scoped to a specific environment.
- **Enterprise (GitHub Enterprise)** — Organization/instance with enterprise features and policy controls.
- **Event (Workflow Event)** — Trigger that starts a workflow (e.g., `push`, `pull_request`, `workflow_dispatch`).
- **Experimental (branch)** — Safe place for spikes/prototypes with CI only (no deploy).

## F

- **Failing Test (Red)** — Test that currently fails; must be fixed before merge.
- **Fast-Forward (FF) Merge** — Move branch pointer when histories are linear (no merge commit).
- **Feature Branch** — Short-lived branch (`feature/*`) for one cohesive change; merged via PR into `Development`.
- **Feature Flag** — Runtime toggle that enables/disables behavior without redeploying.
- **Fixup Commit** — A commit created to amend a previous one; auto-squashed during interactive rebase.
- **Flaky Test** — Test with non-deterministic results; quarantine or stabilize.
- **Fork** — Personal/organization copy of a repo; PRs flow from fork to upstream.
- **Funding File (`FUNDING.yml`)** — Config enabling sponsor links in GitHub UI.

## G

- **GemCommit** — Visual Studio Code (VS Code) extension used to generate Conventional Commit messages with AI assistance.
- **Gist** — Lightweight snippet repository for sharing code/examples.
- **Git Flow** — Branching model with long-lived `develop`/`release`/`hotfix` branches.
- **Git Hooks** — Client-side scripts triggered by git actions (e.g., `pre-commit`).
- **GitHub Actions** — GitHub’s automation platform for CI/CD workflows defined in `.github/workflows/*.yml`.
- **Gitignore (`.gitignore`)** — Patterns of files/paths excluded from version control.
- **Gnu Privacy Guard (GPG)** — Tool for signing commits/tags to prove authorship and integrity.
- **GraphQL API** — GitHub’s primary API for flexible queries/mutations.

## H

- **HEAD** — Current commit/branch pointer in git.
- **Health Check** — Liveness/readiness endpoint used by monitors and rollouts.
- **Hotfix** — Minimal, targeted change that branches from `Release` to fix a production incident; must be back-merged afterward.
- **HSTS (HTTP Strict Transport Security)** — Security header that forces HTTPS for a domain.
- **Human-in-the-Loop** — Manual approval required in an automated pipeline.
- **Hygiene** — Practices that keep branches, issues, and dependencies tidy/up-to-date.
- **Webhook (HTTP Hook)** — Server callback when GitHub events fire.

## I

- **IAM (Identity and Access Management)** — Cloud permission model controlling which identities can access which resources.
- **IaC (Infrastructure as Code)** — Defining infrastructure (e.g., cloud resources) in versioned code.
- **ID Token (OIDC)** — Short-lived JWT issued to workflows for cloud auth without static keys.
- **Integration Test** — Verifies behavior across components/services.
- **Issue Forms / Templates** — Structured issue input via YAML forms.
- **Issue Reference** — A trailer like `Refs #123` or `Closes #123` in a commit or PR body linking to a tracker item.
- **Immutability** — Property that artifacts/tags (ideally) don’t change after release.

## J

- **Job (Actions Job)** — A set of steps executed on a runner; jobs can run in parallel or depend on others.
- **Job Matrix** — Strategy expanding a job across multiple environments (e.g., Node versions, OS).
- **JSON Schema** — Validation for issue forms, config files, or API payloads.
- **JWT (JSON Web Token)** — Token format used by OIDC for workflow authentication.
- **Jitter** — Randomized delay added to retries to avoid thundering herds.

## K

- **Keep a Changelog** — Community format/convention for CHANGELOG files.
- **Kebab-case** — Hyphen-separated naming style (e.g., `my-action`).
- **Key Management Service (KMS)** — Cloud service for encrypting secrets/artifacts.
- **Key Rotation** — Regular replacement of credentials/keys to reduce risk.

## L

- **Label** — Tag applied to issues/PRs to aid triage/automation.
- **Large File Storage (LFS)** — Git extension for storing large binaries externally instead of in the repo.
- **Least Privilege** — Grant only the minimal permissions (e.g., `GITHUB_TOKEN` with read-only).
- **License (`LICENSE`)** — Legal permissions/terms for using the code.
- **Lint / Linter** — Static analysis for style and correctness (e.g., ESLint, Prettier).
- **Lockfile** — Exact dependency graph snapshot (`package-lock.json`) used by `npm ci`.
- **Logs (Actions Logs)** — Console output and annotations from workflow runs.
- **Long-Term Support (LTS)** — A stable, supported release line of a platform (e.g., Node.js LTS).
- **Lead Time (DORA)** — Time from code commit to production.

## M

- **Maintainer** — Person/team with merge/release responsibility.
- **Manual Approval** — Required human step in deployments.
- **Markdown** — Lightweight markup used across README, issues, PRs.
- **Matrix Strategy** — (See J) Run a job across many environments.
- **Merge Queue** — Ordered, auto-rebasing queue that merges PRs when checks pass.
- **Merge Strategy (Squash Merge)** — GitHub option that combines all commits in a PR into a single commit on merge to keep history linear.
- **Mermaid** — Markdown diagram syntax used in docs for flowcharts and sequences.
- **Milestone** — Time-boxed grouping of issues/PRs toward a target.
- **MFA (Multi-Factor Authentication)** — Additional authentication factor beyond a password.
- **Monorepo** — Multiple projects/packages housed in a single repository.

## N

- **Namespace (Package Scope)** — Prefix like `@org` used for packages and container images.
- **Nightly Build** — Scheduled build published daily from `main`/`Development`.
- **No-Op** — Change that doesn’t alter behavior (e.g., docs-only).
- **Non-Fast-Forward (NFF)** — Push that rewrites history; prevented on protected branches.
- **Notifications** — GitHub inbox/email alerts for repo activity.
- **npm Token** — Secret used by CI to publish or install from private registries.

## O

- **OIDC (OpenID Connect)** — Standard that lets GitHub Actions exchange a short-lived ID token for cloud credentials without storing static keys.
- **Observability** — Telemetry (logs, metrics, traces) needed to understand system behavior in production.
- **Open Source Software (OSS)** — Code licensed for public use, contributions via forks/PRs.
- **OpenGraph Image** — Social preview image configured for the repo.
- **Organization** — Top-level container for users, teams, repos, and policies.
- **Output (Workflow Output)** — Values exported by steps/jobs for downstream use.
- **Owner** — Person/team responsible for a component (often expressed via CODEOWNERS).

## P

- **Packages (GitHub Packages)** — Artifact registries (npm, Maven, NuGet, GHCR).
- **Pages (GitHub Pages)** — Static site hosting from a branch/folder.
- **Pair Review** — Two reviewers collaborate to review/approve a PR.
- **Personally Identifiable Information (PII)** — Data that can identify a person; must not be logged or exposed.
- **Pipeline** — End-to-end CI/CD flow from commit to deploy.
- **Pre-commit** — Local checks that run before committing.
- **Pre-release** — Tagged but not “latest” release (e.g., `-rc`, `-beta`).
- **Preview (branch/environment)** — Staging/UAT environment where builds are deployed for validation before Release.
- **Project (Projects v2)** — Work planning and tracking boards.
- **Pull Request (PR)** — Proposed change from a topic branch reviewed and validated before merging.

## Q

- **QA (Quality Assurance)** — Activities verifying that a build meets requirements; in this template, smoke tests on Preview are required.
- **Quality Gate** — (See C) Required checks that gate merges.
- **Query (GraphQL)** — A selection of fields in GitHub’s GraphQL API.
- **Queue (Merge Queue)** — (See M) System that serializes PR merges safely.

## R

- **RBAC (Role-Based Access Control)** — Granting permissions to roles assigned to users or services.
- **README** — Primary documentation entry point for a repo.
- **Rebase** — Replay commits on top of a new base to produce linear history; use `--force-with-lease` to update remotes safely.
- **Release (branch/environment)** — Immutable production line; tags and production deploys originate here.
- **Release Candidate (RC)** — Pre-release tag (e.g., `v1.4.0-rc.1`) for final validation before the stable tag.
- **Release Notes** — Human-readable summary generated for a tag/release.
- **Repository (Repo)** — Version-controlled code + issues/PRs/settings.
- **Required Reviewers** — Minimum approvers enforced by branch rules.
- **Required Status Checks** — CI checks that must pass before merging.
- **Retry with Backoff** — Automated re-execution strategy for transient failures.
- **Runner (Actions Runner)** — Machine/VM/container executing jobs.

## S

- **SARIF** — Static analysis results format used by code scanning.
- **SAST (Static Application Security Testing)** — Code scanning to detect vulnerabilities in source.
- **SBOM (Software Bill of Materials)** — Inventory of packages and versions included in a build.
- **SCA (Software Composition Analysis)** — Tooling that detects vulnerable or outdated dependencies.
- **Schedule (cron)** — Time-based trigger for workflows.
- **Secret Scanning** — Automated detection of credentials accidentally committed to Git or printed to logs.
- **Self-hosted Runner** — Organization-managed runner used for specialized environments or private networking.
- **Semantic Versioning (SemVer)** — `MAJOR.MINOR.PATCH` versioning scheme; breaking/feature/fix map to major/minor/patch.
- **Signed Commits** — Commits/tags signed with GPG or SSH to verify authorship.
- **Single Sign-On (SSO)** — Centralized authentication across services.
- **Snapshot (Artifact)** — Time-specific build output used for debugging or promotion.
- **Staging** — Pre-production environment mirroring production.
- **Submodule** — Separate git repo embedded in a parent repo.

## T

- **Tag** — Named pointer to a specific commit (e.g., `v1.4.0`); used for releases and rollbacks.
- **Template Repository** — Repo marked as a template to seed new projects.
- **Test Pyramid** — Strategy that favors many fast Unit tests, some Integration tests, and few End-to-End (E2E) tests.
- **Timeouts** — Max run durations for jobs/steps to prevent runaway builds.
- **Token Permissions** — Least-privilege settings for `GITHUB_TOKEN` and PATs.
- **Trunk-Based Development (TBD)** — Short-lived branches merging rapidly to a single mainline.
- **Triage** — Prioritizing/labeling issues and PRs.
- **Type/Scope (Conventional Commits)** — `type` describes intent (`feat`, `fix`, …); optional `scope` names the module (e.g., `auth`).

## U

- **UAT (User Acceptance Testing)** — Validation by users/stakeholders (typically on Preview) before production release.
- **Unit Test** — Fast, isolated test of a single function/module.
- **Up-to-date Requirement** — Branch protection rule requiring PR branches to include the latest base before merging.
- **Upstream** — Canonical remote repo that a fork tracks.
- **Untracked Files** — Files not in version control; typically ignored or staged.

## V

- **Verification** — Signature verification for commits/tags/releases.
- **Version Pinning** — Fixing tool/runtime versions (via `.nvmrc`, lockfiles) for reproducible builds.
- **Versioning Policy** — Agreed rules for when/how versions bump and deprecations happen.
- **Vite** — Fast dev build tool often paired with Vitest.
- **Vitest** — Fast test runner commonly used for Unit/Integration tests in TypeScript/JavaScript projects.
- **Visibility** — Repo access level: `private`, `internal` (enterprise), or `public`.
- **Vulnerability Advisory** — Security advisory and patch workflow within GitHub.

## W

- **Wait Timer** — Deployment delay (cool-down) configured on environments.
- **Webhook** — HTTP callback fired on repo/org events (e.g., `push`, `release`).
- **Wiki** — Repo-attached documentation wiki.
- **Workflow (GitHub Actions)** — A YAML-defined automation process triggered by events (e.g., PR, push) to run CI/CD jobs.
- **Workflow Dispatch** — Manual trigger for a workflow with inputs.
- **Workspace (Actions)** — Directory on the runner where the repo is checked out.
- **Work-In-Progress (WIP)** — Incomplete change; open as a Draft PR for early feedback.
- **Workload Identity Federation** — Cloud auth model leveraging OIDC without long-lived keys.

## X

- **X.509 Certificate** — Standard certificate format for TLS/signing (used by registries, SSO, or mutual-TLS setups).
- **X-RateLimit Headers** — Response headers (`limit`, `remaining`, `reset`) indicating API rate-limit state.
- **XML (Extensible Markup Language)** — Common format for coverage reports (e.g., Cobertura) and some tooling.
- **XSS (Cross-Site Scripting)** — Injection of malicious scripts; mitigated via escaping, CSP, and safe templating.

## Y

- **YAGNI (You Aren’t Gonna Need It)** — Avoid speculative complexity; implement only what’s required.
- **YAML** — Human-readable data format used for Actions workflows and configs.
- **Yarn** — Package manager; maintain `yarn.lock` for reproducible installs.
- **YubiKey** — Hardware security key for MFA and commit/tag signing.

## Z

- **ZAP (OWASP Zed Attack Proxy)** — Popular DAST tool for automated security scanning.
- **Zero-Downtime Deploy** — Release pattern (blue/green, canary) that avoids user-visible downtime.
- **Zero Trust** — Security model assuming no implicit trust; enforce continuous verification and least privilege.
- **Zombie Branch** — Stale branch left unmaintained; prune routinely via branch rules/cleanup jobs.

---

## Long-lived Branches

| Branch | Purpose |
|---|---|
| `Release` | Production line; tags and production deploys originate here. |
| `Preview` | Staging/UAT deployments and End-to-End (E2E) smoke tests. |
| `Development` | Default integration line; all PRs target here. |
| `feature/*`, `bugfix/*`, `hotfix/*`, `experiment/*`, `integration/*` | Short-lived topic branches merged via PR. |

---

## Conventional Commits

### Subject
```
<type>(<scope>): <emoji> <subject>
```
- type ∈ feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert (lowercase).
- scope: short, lowercase, kebab-case (ui, api/users, auth); optional.
- emoji: exactly one, immediately after the colon.
- style: present tense by default (“Adds…”, “Fixes…”); no trailing period.
- length: aim ≤50 chars; hard cap ≤72.

### Emoji Map
feat ✨ | fix 🐛 | docs 📝 | style 🎨 | refactor ♻️ | perf ⚡️ | test 🧪 | build 🏗️ | ci 🤖 | chore 🧹 | revert ⏪

### Body
- Paragraph 1: one logical line, 2–4 short sentences (what/why/impact).
- Blank line, then: `The <thing> includes:`
- 3–6 bullets, sentence case, each ≤72 chars, end with a period.
- No emoji in body.

### Footers
- Only include issue refs if allowed: `Closes #123` or `Refs #123`.
- Breaking: `BREAKING CHANGE: <details>`.
- Co-authors: `Co-authored-by: Name <email>`.
- No emoji in footer.

### Infer Type & Scope
- add/new ⇒ feat | bug/regression ⇒ fix | docs-only ⇒ docs | rename/format ⇒ style
- restructure/no behavior change ⇒ refactor | speed/memory ⇒ perf | tests ⇒ test
- deps/build tooling/config ⇒ build | workflows ⇒ ci | chores/scripts ⇒ chore | undo ⇒ revert
- derive scope from path (ui, api, auth, db, build); omit if unclear.

### Example
```
feat(auth): ✨ Adds passwordless sign-in

This change introduces link-based authentication to simplify first-run setup and reduce friction for returning users. It improves conversion on sign-up and reduces support tickets for credential resets.

The change includes:
- New magic-link endpoint with signed tokens.
- Rate limits and replay protection for link consumption.
- UI flow for request, verification, and success states.
```

---

## Admonitions used in documents

- `[!NOTE]` — Additional context.  
- `[!IMPORTANT]` — Policy or rule to follow.  
- `[!TIP]` — Practical advice.  
- `[!CAUTION]` — Risky/footgun; double-check before proceeding.  
- `[!WARNING]` — Potentially breaking or security-critical.

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
