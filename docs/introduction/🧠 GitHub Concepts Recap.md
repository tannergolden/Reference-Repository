# 🧠 GitHub Concepts Recap

A quick, practical refresher on the Git/GitHub model we use: small branches, Pull Requests (PRs), Continuous Integration (CI), and a clean promotion path (**Development → Preview → Release**).

> [!IMPORTANT]
> Expand acronyms on first use (e.g., Pull Request (PR), Continuous Integration (CI)). Keep branches short-lived, use squash merges, and follow Conventional Commits.

---

## 🔌 Mental Model (how it all fits)
- **Git** is your local Version Control System (VCS); **GitHub** hosts shared remotes, reviews, and automation.
- You do work on **topic branches** (e.g., `feature/<topic>`), propose changes via a **Pull Request (PR)**, and CI (Continuous Integration) proves they’re safe.
- We integrate to **Development**, then **promote** to **Preview**, then **Release**.

---

## 🧱 Core Objects & What They Mean

| Concept | What it is | Typical commands |
|---|---|---|
| **Commit** | A snapshot with metadata (author, message, parent) | `git commit -m "feat: ..."` |
| **Branch** | A movable pointer to a commit; a line of work | `git switch -c feature/x` · `git branch -vv` |
| **Remote** | A named server (usually `origin`) | `git remote -v` · `git push origin HEAD` |
| **HEAD** | Your current checkout (branch/commit) | `git status` |
| **Index (Staging)** | What will be in the next commit | `git add .` · `git restore --staged <file>` |
| **Merge** | Combine histories, creating a merge commit | `git merge <branch>` |
| **Rebase** | Replay commits on a new base | `git rebase origin/Development` |
| **Tag** | A named pointer (often a release) | `git tag v1.2.3` · `git push --tags` |

---

## 🌿 Our Branch Model (at a glance)

```mermaid
flowchart LR
  Local[Local work];
  Feature[feature/*];
  Bugfix[bugfix/*];
  Hotfix[hotfix/*];
  Dev[Development];
  Prev[Preview];
  Rel[Release];

  Local --> Feature;
  Feature --> Dev;
  Bugfix --> Dev;
  Dev --> Prev;
  Prev --> Rel;
  Bugfix --> Prev;
  Hotfix --> Rel;
````

> \[!NOTE]
> Day-to-day integration lands in **Development**. Promotion path is **Development → Preview → Release**. Hotfixes can go directly to **Release** when needed (with approvals).

---

## 🚦 Everyday Workflows

### 1) Start new work (feature)

```bash
git switch Development
git pull --ff-only origin Development
git switch -c feature/<topic>
# ...edit...
git add -A
git commit -m "feat(<scope>): brief change description"
git push -u origin HEAD
```

### 2) Keep your branch up to date (rebase on Development)

```bash
git fetch origin
git rebase origin/Development   # or: git merge origin/Development
# resolve conflicts if any, then:
git push --force-with-lease
```

### 3) Open a Pull Request (PR)

* Target **Development**.
* Keep the PR small and single-purpose.
* Link tickets/docs; add testing notes and risk/rollback.
* Prefer **squash merge** to keep history tidy.

### 4) Update after review

```bash
# make edits...
git add -A
git commit -m "fix: address review feedback"
git push
```

---

## 🔀 Merge vs Rebase vs Squash

| Action     | What it does                                        | When to use                                                     |
| ---------- | --------------------------------------------------- | --------------------------------------------------------------- |
| **Merge**  | Creates a merge commit combining histories          | Team branches or when preserving the exact history is important |
| **Rebase** | Replays your commits on a new base (linear history) | Updating a topic branch before merging                          |
| **Squash** | Collapses PR commits into one on merge              | Default for PRs: clean, readable history                        |

> \[!TIP]
> Use `--force-with-lease` (not `--force`) after a rebase to protect teammates’ work.

---

## 🧰 Conventional Commits (for readable history & automated notes)

**Format**

```
<type>(<scope>): <summary>

[optional body]

[optional footer(s)]
```

**Common types**: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `build`, `ci`.

**Example**: `feat(auth): add passwordless sign-in`

---

## 🧨 Conflicts—how to resolve safely

1. **Fetch latest**: `git fetch origin`
2. **Rebase or merge** onto the target base (usually `origin/Development`).
3. **Edit conflict markers** (`<<<<<<<`, `=======`, `>>>>>>>`) and decide the correct result.
4. **Mark resolved**: `git add <files>`
5. **Continue**: `git rebase --continue` (or complete the merge).
6. **Run tests locally**, then `git push --force-with-lease` (if you rebased).

> \[!CAUTION]
> Never commit conflict markers. Always run tests before pushing.

---

## 🧯 Undo—use the right tool

| Goal                                   | Safe command                  | Notes                                                       |
| -------------------------------------- | ----------------------------- | ----------------------------------------------------------- |
| Undo local edits before staging        | `git restore <file>`          | Working tree only                                           |
| Unstage but keep edits                 | `git restore --staged <file>` | Moves back to working tree                                  |
| Revert a public commit                 | `git revert <sha>`            | Creates a new commit that undoes changes                    |
| RePoint branch to older commit (local) | `git reset --hard <sha>`      | **Destructive**; use with care; don’t do on shared branches |

---

## 🛰️ Sync a fork (if you use forks)

```bash
git remote add upstream https://github.com/<org>/<repo>.git
git fetch upstream
git switch Development
git rebase upstream/Development
git push --force-with-lease origin Development
```

---

## 📦 Tags & Releases

* Tag after promoting to **Release**: `git tag vX.Y.Z && git push --tags`
* Draft release notes from Conventional Commits; attach artifacts built by CI.

---

## 🧪 Quick Command Cheat Sheet

```bash
# create & switch
git switch -c feature/<topic>

# stage & commit
git add -A
git commit -m "feat(scope): message"

# sync with Development
git fetch origin
git rebase origin/Development
git push --force-with-lease

# open PR on GitHub after first push, then later:
git pull --rebase   # keep your branch fresh
```

---

## ❓ Frequently Asked Questions (FAQ)

* **Why squash merge PRs?**
  To keep the main history readable; individual WIP commits remain visible in the PR.

* **When should I rebase?**
  Before merging a topic branch to **Development**, so reviewers see a clean diff.

* **What if CI fails?**
  Fix locally (`lint`, `test`, `build`), push updates; CI should mirror local commands.

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
