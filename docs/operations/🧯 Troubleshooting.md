# 🧯 Troubleshooting

Fast, practical fixes for common issues across **Experimental → Development → Preview → Release**. Start with the quick checks, then jump to the section that matches your symptom.

> [!CAUTION]
> **Never** commit secrets. If a secret leaks, revoke/rotate immediately and follow the incident steps in **🔐 Security & Secrets**.

---

## 🚦 Quick Triage (90-second checklist)

1. **Sync tool versions**  
   ```bash
   nvm use            # reads .nvmrc
   node -v && npm -v
   ```

2. **Clean, exact install**

   ```bash
   rm -rf node_modules package-lock.json && npm ci
   ```
3. **Baseline commands (same as CI)**

   ```bash
   npm run lint
   npm test -- --ci
   npm run build
   ```
4. **Git sanity**

   ```bash
   git fetch --all --prune
   git status -sb
   ```
5. **Environment**

   * Using **.env.example** as a guide? (no real secrets in Git)
   * Emulators running if needed? `firebase emulators:start`

---

## 🔟 Top Issues (most common → least)

| Symptom                                      | Likely cause                          | Fix                                                                                     |
| -------------------------------------------- | ------------------------------------- | --------------------------------------------------------------------------------------- |
| CI green locally, red in CI                  | Node.js mismatch / non-exact install  | `nvm use` then `npm ci`; pin Node in CI via `.nvmrc`                                    |
| Husky hooks not running (commitlint ignored) | Husky not installed or not executable | `npm run prepare`; `chmod +x .husky/*`; ensure `.git/hooks` not blocked                 |
| “Required status check is missing” on PR     | Job name changed / missing            | Update branch protection to actual job name (e.g., `ci / build-test`)                   |
| “Out of date with base” → merge blocked      | Base branch advanced                  | Rebase: `git fetch && git rebase origin/Development` then `git push --force-with-lease` |
| Emulator errors / port in use                | Emulators not started or wrong ports  | `firebase emulators:start`; free ports (see **Emulators** section)                      |
| Playwright E2E fails in CI                   | Browser deps missing / wrong URL      | `npx playwright install --with-deps`; set `PREVIEW_URL`; upload traces                  |
| Commitlint fails                             | Message not Conventional              | Use **GemCommit**; format `type(scope): summary`; keep subject ≤72 chars                |
| `ELIFECYCLE` / build script crash            | Missing env / script error            | Check script names, env vars; run `npm run build` locally with logs                     |
| `ERESOLVE` dependency conflicts              | Incompatible ranges                   | Pin direct dep, use overrides; re-lock: `rm package-lock.json && npm ci`                |
| Secret scanner flags a leak                  | Real key in repo/logs                 | Revoke/rotate; remove from history only if required; add patterns to scanner            |

---

## 🧰 Diagnostics You Can Paste

```bash
# Quick env snapshot
echo "Node: $(node -v)"; echo "NPM: $(npm -v)"
git rev-parse --abbrev-ref HEAD
git rev-parse --short HEAD
git remote -v

# Lockfile drift check
grep -E '"name"|"version"' package.json | head -n 5
wc -l package-lock.json

# CI parity run
npm ci && npm run lint && npm test -- --ci && npm run build
```

---

## 🌿 Git & PR Problems

### Rejected push / non-fast-forward

```bash
git fetch origin
git rebase origin/Development   # or origin/<target>
git push --force-with-lease
```

### “Required status check is missing”

* Open **Settings → Branches → Branch protection** and make sure the **exact** job names from your workflows are listed (e.g., `ci / build-test`, `Commitlint`, `Typecheck`).

### PR too large / noisy

* Split into **feature/refactor/test/docs** PRs. Keep ≤10 files and \~300 changed lines per PR when possible.

---

## ⚙️ CI/CD Failures

### Fails in CI but not locally

* Ensure parity: `nvm use` and **always** `npm ci` (never `npm install`) in CI.
* Verify workflow uses:

  ```yaml
  - uses: actions/setup-node@v4
    with:
      node-version-file: '.nvmrc'
      cache: 'npm'
  - run: npm ci
  ```

### Running out of memory building

```yaml
- name: Increase Node memory
  run: export NODE_OPTIONS="--max_old_space_size=4096"
```

### Stuck “Queued” / permissions

* Check GitHub **Environments** required reviewers.
* Check concurrency group collisions (`concurrency: group: ci-${{ github.ref }}`).

---

## 🧪 Tests & E2E

### Playwright cannot launch browser (Linux CI)

```bash
npx playwright install --with-deps
```

Set Preview URL:

```bash
export PREVIEW_URL="https://preview.example.com"
```

Capture artifacts in `playwright.config.ts`:

```ts
use: { trace: 'retain-on-failure', screenshot: 'only-on-failure', video: 'retain-on-failure' }
```

### Flaky E2E

* Set `retries: 1`, add explicit waits for **network idle** or **visible**.
* Keep **smoke** suite tiny; move breadth to **regression** gated on promotion.

---

## 🔌 Firebase Emulators & Local Env

### Ports in use / emulator won’t start

**Common ports**

| Service   | Port |
| --------- | ---: |
| Firestore | 8080 |
| Auth      | 9099 |
| Functions | 5001 |

**Free a port (macOS/Linux)**

```bash
lsof -i :8080
kill -9 <PID>
```

**Windows**

```powershell
netstat -ano | findstr :8080
taskkill /PID <PID> /F
```

### Rules / auth tests failing

* Use `@firebase/rules-unit-testing` to set **authed/unauthenticated** contexts.
* Ensure emulators env vars are set (in test env):

```ini
FIREBASE_AUTH_EMULATOR_HOST=localhost:9099
FIRESTORE_EMULATOR_HOST=localhost:8080
FUNCTIONS_EMULATOR_HOST=localhost:5001
```

---

## 🧱 Dependencies & Build

### `ERESOLVE` or transitive conflicts

```bash
rm -rf node_modules package-lock.json
npm ci
# If still failing, pin the direct dependency or use overrides
```

### Native module errors (node-gyp)

* macOS: install **Xcode Command Line Tools**
  `xcode-select --install`
* Linux: `sudo apt-get install -y build-essential python3 make g++`

### Missing type declarations

```bash
npm i -D @types/<pkg>
```

---

## 🧲 Husky, Commitlint, GemCommit

### Hooks not running

```bash
npm run prepare
chmod +x .husky/*
```

Ensure `.husky/commit-msg` calls commitlint:

```bash
npx --no install commitlint --edit "$1"
```

### Commit message rejected

* Use **GemCommit** in Visual Studio Code (VS Code) with the repo prompt.
* Format: `type(scope): summary` (≤72 chars); body for **why/impact**.

---

## 🔐 Secrets & Scanners

### Secret scanner fails the PR

1. **Revoke/rotate** the secret at the provider.
2. Remove from code, configs, and logs.
3. Add/adjust scanner patterns if needed (never whitelist real keys).
4. Document in the incident ticket; follow **hotfix** path if production is affected.

---

## 🧯 Rollback & Hotfix (quick path)

* Create `hotfix/<ticket>` **from `Release`**, minimal change + tests.
* Merge PR → tag patch `vX.Y.Z+1`.
* **Back-merge** `Release → Development` (and `Preview` if diverged).

---

## ✅ When to Ask for Help (include this info)

* Commit SHA, branch, and failing job link.
* Output of: `node -v`, `npm -v`, `npm ci && npm test -- --ci`.
* Whether emulators or Preview were used (include URL).
* Any secrets possibly printed to logs (confirm redaction).

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
