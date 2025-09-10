# ⌨️ Git Commands
A concise, copy-paste guide for everyday workflows in this repo. Assumes the default branch is **`Development`**, with promotions to **`Preview`** and **`Release`** via Pull Request (PR). Expand acronyms on first use: Pull Request (PR), Continuous Integration (CI), Continuous Delivery (CD).

> [!IMPORTANT]
> Never push directly to protected branches (`Development`, `Preview`, `Release`). Open a Pull Request (PR), wait for Continuous Integration (CI) to pass, then **squash merge**.

---

## 🧭 Setup & Orientation

```bash
# one-time setup
git config --global init.defaultBranch Development
git config --global pull.ff only          # only fast-forward pulls
git config --global fetch.prune true      # auto prune deleted branches
git config --global rerere.enabled true   # reuse recorded conflict resolutions

# clone & enter
git clone <repo-url>
cd <repo>

# sync everything
git fetch --all --prune
git switch Development
git pull --ff-only
```

---

## 🔄 Daily Flow (small, reviewable changes)

```bash
# create a topic branch
git switch Development && git pull --ff-only
git switch -c feature/<topic>             # or bugfix/<ticket>, experiment/<idea>

# do work, then stage selectively
git add -p                                # interactive stage

# commit (use GemCommit in Visual Studio Code (VS Code) or commit manually)
git commit                                # Conventional Commits format

# keep current with base
git fetch origin
git rebase origin/Development
git push --force-with-lease -u origin HEAD

# open a Pull Request (PR) to Development in GitHub
```

---

## 🧩 Conventional Commits

```bash
# <type>(<scope>): <emoji> <subject <= 72 chars>
# types: feat, fix, docs, style, refactor, perf, test, chore, build, ci, revert
# scopes: auth, api, db, ui, build, etc.
# emoji map: feat ✨ | fix 🐛 | docs 📝 | style 🎨 | refactor ♻️ | perf ⚡️ | test 🧪 | chore 🧹 | build 🏗️ | ci 🤖 | revert ⏪
```

Example:

```bash
git commit -m "feat(auth): ✨ add passwordless sign-in"
```

---

## 🌿 Branching & Promotions

```bash
# promote Development -> Preview via PR (preferred)
# (From GitHub UI: open PR with base=Preview, compare=Development)

# promote Preview -> Release via PR (after QA and smoke tests)
# (From GitHub UI: open PR with base=Release, compare=Preview)
```

**Back-merge after hotfix**

```bash
# after merging a hotfix to Release, back-merge to Development
git switch Development
git fetch origin
git merge --no-ff origin/Release
git push
```

---

## 🚑 Hotfix Protocol (production incident)

```bash
# branch from Release
git fetch origin
git switch -c hotfix/<ticket> origin/Release

# minimal change + tests
git add -A
git commit -m "fix(api): 🩹 handle null header edge case"

# push & open PR to Release
git push -u origin HEAD

# after merge: tag patch and back-merge Release -> Development (and Preview if needed)
```

---

## 🔧 Rebase, Squash & Repair

```bash
# rebase topic branch on latest Development
git fetch origin
git rebase origin/Development

# squash last N commits into one (interactive)
git rebase -i HEAD~N

# fixup the previous commit with staged changes
git commit --fixup=<SHA>
git rebase -i --autosquash origin/Development

# resolve conflicts during rebase
git status
# edit files, then:
git add <resolved-files>
git rebase --continue
```

---

## ⏪ Safe Undo & Cleanups

```bash
# unstage changes (keep working tree)
git reset HEAD .

# drop local edits (careful)
git checkout -- <path>

# undo the last commit but keep changes staged
git reset --soft HEAD~1

# revert a bad merge/commit (creates a new inverse commit)
git revert <SHA>

# delete a merged branch locally and remote
git branch -d feature/<topic>
git push origin --delete feature/<topic>
```

---

## 🔍 Inspect & Compare

```bash
git status -sb                       # short status
git log --oneline --decorate --graph --all
git show <SHA>
git diff                             # working tree
git diff --staged                    # staged vs HEAD
git diff origin/Development...HEAD   # what's in your branch vs base
```

---

## 🏷️ Tags & Releases

```bash
# tag current commit (use Semantic Versioning (SemVer))
git tag v1.4.0
git push origin v1.4.0

# list tags and show details
git tag --list
git show v1.4.0
```

> \[!NOTE]
> Tags should be created by automation on the `Release` branch. Use manual tags only if your team’s process allows it.

---

## 🗃️ Stash & Patch

```bash
git stash push -m "wip: local experiment"
git stash list
git stash show -p stash@{0}
git stash apply stash@{0}            # keep entry
git stash drop stash@{0}             # remove entry

# create and apply a patch between two commits
git diff <base>..<head> > /tmp/patch.diff
git apply /tmp/patch.diff
```

---

## ⚙️ Helpful Aliases (optional)

```bash
git config --global alias.co "checkout"
git config --global alias.sw "switch"
git config --global alias.br "branch"
git config --global alias.st "status -sb"
git config --global alias.lg "log --oneline --decorate --graph --all"
git config --global alias.unstage "reset HEAD --"
git config --global alias.wip "commit -m 'chore: wip'"
```

---

## 🧪 CI Parity (match CI locally)

```bash
nvm use                   # align Node.js version
npm ci                    # exact lockfile install
npm run lint
npm test -- --ci
npm run build
```

---

## 🧯 Troubleshooting Snippets

```bash
# "out of date with base" on PR
git fetch origin
git rebase origin/Development
git push --force-with-lease

# restore a deleted local branch (if reflog has it)
git reflog
git switch -c feature/<topic> <found-SHA>

# recover a commit you lost the ref to
git fsck --lost-found
```

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
