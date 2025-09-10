# 🛡️ Branch Protection

Guard the integrity of long-lived branches and make safe shipping the default. All work flows by Pull Request (PR) through **Development → Preview → Release**; no direct pushes to protected branches.

> [!IMPORTANT]
> **Default branch = `Development`.** Require PRs, passing checks (install → lint → test → build), and approvals on `Development`, `Preview`, and `Release`. Hotfixes to `Release` are minimal, reviewed, and **back-merged**.

---

## 🎯 Goals
- Prevent accidental pushes/force-pushes to release lines.
- Ensure every change is reviewed, tested, and auditable.
- Keep histories clean (squash merges) and rollbacks predictable (tags).

---

## 🧱 What to Protect (and how)

| Branch (or pattern) | Purpose | PR reviews | Required checks | Other rules |
|---|---|---|---|---|
| `Development` | Daily integration | ≥1 (CODEOWNERS if applicable) | `ci / build-test` (install/lint/test/build) | Up-to-date with base; resolve conversations |
| `Preview` | Staging/UAT | ≥1 (owners for risky changes) | Same as `Development` | Up-to-date; environment approvals (Preview) |
| `Release` | Production line | ≥1 + release manager | Same as `Development` | Up-to-date; environment approvals (Release); signed commits (optional) |
| `hotfix/*` | Emergency fixes | ≥1 + release manager | Same as `Release` | Back-merge after tag |
| `experiment/*` (optional) | Spikes | 0–1 (team choice) | `ci / build-test` | No deploy; CI only |

> [!NOTE]
> “Required checks” are the **job names** reported by your workflows. Adjust the names to match your repo (e.g., `ci / build-test`, `Commitlint`, `Typecheck`).

---

## ✅ GitHub Settings Checklist (per protected branch)

- Require a **pull request** before merging.  
- **Required status checks**: add your CI jobs; **Require branches up to date**.  
- **Require review approvals** (≥1) and **Dismiss stale approvals**.  
- **Require review from CODEOWNERS** (where defined).  
- **Require conversation resolution**.  
- **Restrict who can push** (optional): set team(s) for `Release`/`hotfix/*`.  
- **Include administrators** (enforce on admins).  
- Disable **force pushes** and **branch deletions**.  
- (Optional) **Require signed commits** on `Release`.

---

## 🔧 Scripted Setup (GitHub CLI `gh api`)

> Replace `$OWNER`, `$REPO`, and branch names as needed. Contexts must match your workflow check names (e.g., `ci / build-test`).

```bash
OWNER=<your-org> REPO=<your-repo>
protect () {
  local BR=$1
  gh api -X PUT \
    -H "Accept: application/vnd.github+json" \
    "/repos/$OWNER/$REPO/branches/$BR/protection" \
    -f required_status_checks.strict=true \
    -f enforce_admins=true \
    -f required_linear_history=true \
    -f allow_force_pushes=false \
    -f allow_deletions=false \
    -f required_conversation_resolution=true \
    -F required_status_checks.contexts[]="ci / build-test" \
    -F required_pull_request_reviews.dismiss_stale_reviews=true \
    -F required_pull_request_reviews.require_code_owner_reviews=true \
    -F required_pull_request_reviews.required_approving_review_count=1
}

protect Development
protect Preview
protect Release

# (Optional) restrict pushes on Release to a team
TEAM_SLUG=release-managers
gh api -X PUT \
  -H "Accept: application/vnd.github+json" \
  "/repos/$OWNER/$REPO/branches/Release/protection/restrictions/teams" \
  -f teams[]="$TEAM_SLUG"

# (Optional) require signed commits on Release
gh api -X POST \
  -H "Accept: application/vnd.github+json" \
  "/repos/$OWNER/$REPO/branches/Release/protection/required_signatures"
````

> \[!TIP]
> Prefer **squash merge only** in repository settings to keep history linear. (Settings → General → “Merge button”.)

---

## 🧭 Promotion Guardrails

* **Development → Preview**: PR with all checks green; Preview environment may require **approvals**.
* **Preview → Release**: PR with approvals; **tag and release notes** generated from Conventional Commits.
* **Hotfix to Release**: minimal scope; after merge/tag, **back-merge** to `Development` (and `Preview` if diverged).

---

## 🔐 Advanced (Rulesets – optional)

If your plan includes **Rulesets**, mirror the above as a repository ruleset to:

* Enforce org-wide policies (e.g., commit signing, secret scan required).
* Apply rules by **branch pattern** (`Release`, `Preview`, `Development`, `hotfix/*`).
* Block merges when **code scanning** or **secret scanning** fails.

---

## 🧯 Common Pitfalls (and fixes)

| Symptom                              | Likely cause       | Fix                                                   |
| ------------------------------------ | ------------------ | ----------------------------------------------------- |
| “Required status check missing”      | Job name changed   | Update branch protection contexts to the new job name |
| Can’t merge: “out of date with base” | Base moved         | Click **Update branch** or rebase locally             |
| Force-push still allowed             | Admins exempt      | Enable **Include administrators**                     |
| Hotfix not visible in Development    | Missed back-merge  | PR `Release → Development` immediately after hotfix   |
| Stale approvals                      | New commits pushed | Enable **Dismiss stale approvals**                    |

---

## ✅ Definition of Done (Branch Protection)

* `Development`, `Preview`, `Release` protected with PRs, checks, and approvals.
* Required checks listed and stable; job names match CI.
* Admins included; force-push & deletions disabled.
* (Optional) `Release` push restricted to a team; signed commits required.
* Back-merge policy documented and followed after every hotfix.

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
