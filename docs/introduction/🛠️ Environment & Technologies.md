# 🛠️ Environment & Technologies

Establish a **consistent, reproducible** developer setup so local work ≈ **Continuous Integration (CI)** and promotions to **Preview → Release** are boring and safe.

> [!IMPORTANT]
> Use the exact tool versions pinned in this repository to avoid “works on my machine” failures. Prefer version managers (e.g., **Node Version Manager (nvm)** or **asdf**) over system installs.

---

## Core Technologies

- **Backend & Artificial Intelligence (AI) App Builders: Firebase Studio & Lovable** ⚡🤖💙  
  - **Firebase Studio** — Cloud Firestore (**Database (DB)**), Firebase Authentication (**Auth**) for users, Cloud Functions (serverless), **and an Artificial Intelligence (AI) app-builder** capability to generate/scaffold full-stack features and services.
    https://firebase.studio/  
  - **Lovable** — An Artificial Intelligence (**AI**) app builder that can generate full-stack code and **provide/host backend functionality**. Treat Lovable output like any other code: submit as a **Pull Request (PR)**, pass the same linters/tests, and review security before merge.
    https://lovable.dev/
  - **Bubble** — A no-code, visual web-app builder for full-stack apps: built-in **database (DB)** and **privacy rules**, **user auth**, **backend workflows** (serverless), **API Connector** for REST/GraphQL, and managed hosting/CDN. Treat Bubble changes with the same rigor: use dev/live environments, document schema changes, version API contracts in the repo, and review security before publishing.  
    https://bubble.io/

- **Front-End (Web): Hosting + DNS (backend-agnostic)** 🌐  
  Build with a modern **JavaScript (JS)** framework (React / Vue / Svelte). Deploy to your choice of host/CDN—e.g., **Vercel**, **Netlify**, **Cloudflare Pages**, **Firebase Hosting**, **GitHub Pages**, **Amazon S3 + CloudFront**, or self-hosted **Nginx/Apache**. Connect to any backend (Firebase / Lovable / Bubble, Node/Express, Python/Django/FastAPI, Rails, Go, etc.) via HTTPS APIs. Manage custom domains with your **DNS** provider, enable TLS, set redirects, and cache static assets. Use CI/CD with preview deploys and store **environment variables/secrets** in the hosting platform.

- **Artificial Intelligence (AI) in the workflow** 🤖  
  Beyond the builders above, an **Artificial Intelligence (AI)** agent assists with code generation, drafting **Pull Request (PR)** descriptions, and **Conventional Commits** on the correct branch. Humans remain architects, reviewers, testers, and final approvers.

- **Mobile: Flutter or Capacitor** 📱  
  Cross-platform frameworks for high-performance **Apple iOS / Google Android** from a single codebase.  
  https://flutter.dev/ · https://capacitorjs.com/

> [!CAUTION]
> Regardless of tool, **never** share production secrets in prompts or browser consoles. Review generated code for insecure patterns and dependency risk before merging.

---

## 📋 Support Matrix (Overview)

| Layer | Default Choice | Purpose |
|---|---|---|
| Operating System (**OS**) | macOS / Linux / Windows (via Windows Subsystem for Linux (**WSL**)) | Developer workstation |
| Version Control System (**VCS**) | Git ≥ 2.40 | Source control |
| Runtime | Node.js **Long-Term Support (LTS)** | Tooling + web build |
| Package Manager | **Node Package Manager (npm)** with lockfile committed | Reproducible installs |
| Hosting / Backend | **Firebase Studio &/or Lovable** (Firestore, Auth, Functions, generated services) | Data + serverless + generated backend |
| Web Hosting | Firebase Hosting | Static assets + edge config |
| Mobile | Flutter / Capacitor | Cross-platform apps |
| Container Engine | Docker / Podman (optional) | Environment parity |
| Editor / Integrated Development Environment (**IDE**) | Visual Studio Code (**VS Code**) | Coding + extensions |

> [!TIP]
> If your team standardizes on another stack (e.g., **Python 3.11**, **Java Development Kit (JDK) 21**), keep the same principles: **pinned versions**, **lockfiles**, and **one-command flows**.

---

## 🧩 Recommended Versions & Pinning

| Tool | Recommended | How We Pin / Check |
|---|---|---|
| Node.js | LTS (e.g., 20.x) | `.nvmrc` / `.tool-versions` |
| **Node Package Manager (npm)** | Use lockfile | `npm ci` (never `npm install` in Continuous Integration (CI)) |
| Firebase Command Line Interface (**CLI**) | Latest | `firebase --version` |
| Google Cloud Software Development Kit (**SDK**) (**gcloud**) | Latest | `gcloud version` |
| Git | ≥ 2.40 | `git --version` |

---

## 🧪 Local Development Environment

### Install Node.js (LTS) with a Version Manager
```bash
# macOS/Linux — Node Version Manager (nvm)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
nvm install --lts
nvm use --lts
node -v && npm -v
```

### Firebase Command Line Interface (CLI)
```bash
npm install -g firebase-tools
firebase login
firebase --version
# (optional) set default project
firebase use --add   # choose YOUR_PROJECT_ID
```

### Google Cloud Software Development Kit (SDK) (gcloud)
```bash
# macOS (Homebrew)
brew install --cask google-cloud-sdk
gcloud auth login
gcloud config list
```

### Windows via Windows Subsystem for Linux (WSL)
1. Enable **Windows Subsystem for Linux (WSL)** and install Ubuntu from Microsoft Store.  
2. Run the same **Node Version Manager (nvm)** / **Firebase Command Line Interface (CLI)** / **Google Cloud Software Development Kit (SDK)** steps inside Ubuntu.  
3. Use **Visual Studio Code (VS Code) + Remote - WSL** for an **Integrated Development Environment (IDE)** experience.

---

## 🧪 Emulators & Local Parity

Run Firebase emulators locally to keep local ≈ **Continuous Integration (CI)** and reduce cloud cost:

```bash
firebase emulators:start
```

| Emulator | Default Port | Notes |
|---|---:|---|
| Firestore | 8080 | Data rules and queries |
| Authentication | 9099 | Sign-in flows |
| Functions | 5001 | Local callable Hypertext Transfer Protocol (HTTP) |
| Hosting | 5000 | Static site preview |
| User Interface (**UI**) | 4000 | Emulator dashboard |

Point your app at these during development to test end-to-end locally.

---

## 🔐 Environment Variables & Secrets

- Copy **`.env.example` → `.env.local`** and fill local-only values.  
- **Never commit** real secrets; keep `.env.local` in `.gitignore`.  
- In **CI/CD** (Continuous Integration / Continuous Delivery (CD)), store secrets in **GitHub Actions Environments** with **Role-Based Access Control (RBAC)**, **Single Sign-On (SSO)**, and **Multi-Factor Authentication (MFA)**.  
- Prefer a cloud secret manager when applicable; **redact** secrets from logs and **rotate** immediately if exposed.

**`.env.example`**
```ini
# Firebase
FIREBASE_PROJECT_ID=your-project-id
FIREBASE_WEB_API_KEY=replace-in-gh-secrets

# App
APP_ENV=local
```

---

## 🏗️ Project Scripts

Keep the same commands locally and in **Continuous Integration (CI)**.

| Script | Purpose |
|---|---|
| `npm run setup` | Install dependencies (`npm ci`), prep hooks |
| `npm run dev` | Start local dev server (e.g., Vite / Next / Nuxt / SvelteKit) |
| `npm run lint` | Run linters/format checks |
| `npm test` | Run unit tests (watch locally, `--ci` in Continuous Integration (CI)) |
| `npm run build` | Build production bundle |
| `npm run serve` | Start Firebase emulators |

`package.json` (excerpt)
```jsonc
{
  "scripts": {
    "setup": "npm ci",
    "dev": "vite",                  // or next dev / nuxt dev / svelte-kit dev
    "lint": "eslint .",
    "test": "vitest run",           // or jest --ci
    "build": "vite build",          // or next build / nuxt build / svelte-kit build
    "serve": "firebase emulators:start"
  }
}
```

---

## 🧰 Quality Gates & Hooks

| Check | Tooling | Why |
|---|---|---|
| Formatting | Prettier | Consistent diffs |
| Linting | ESLint (stack-appropriate) | Find bugs early |
| Unit tests | Vitest / Jest | Fast feedback |
| Secret scanning | GitHub Advanced Security / `gitleaks` | Prevent leaks |
| Software Composition Analysis (**SCA**) | Dependabot / Renovate / `npm audit` | Vulnerability management |
| Commit style | commitlint + **Conventional Commits** | Clean history & changelogs |
| Pre-push hooks | Husky + lint-staged | Catch issues before Continuous Integration (CI) |

---

## 🐳 Containers & Dev Containers (optional)

**Dev Containers** (Visual Studio Code (VS Code)) and **GitHub Codespaces** give hermetic, reproducible environments.

`.devcontainer/devcontainer.json` (example)
```jsonc
{
  "name": "repo-dev",
  "image": "mcr.microsoft.com/devcontainers/javascript-node:20",
  "postCreateCommand": "npm ci",
  "customizations": {
    "vscode": {
      "extensions": [
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "github.vscode-github-actions",
        "ms-vscode.vscode-typescript-next",
        "gemcommit" // Conventional Commits assistant (set your team's Marketplace ID if different)
      ]
    }
  },
  "features": {
    "ghcr.io/devcontainers/features/git:1": {}
  }
}
```

Docker (local run)
```bash
docker build -t repo-app:dev .
docker run --rm -it -p 3000:3000 --env-file .env.local repo-app:dev
```

> [!NOTE]
> Keep the container Node.js **Long-Term Support (LTS)** aligned with local and **Continuous Integration (CI)**.

---

## 🧩 Recommended Visual Studio Code (VS Code) Extensions

| Extension | ID (example) | Why |
|---|---|---|
| ESLint | dbaeumer.vscode-eslint | Lint feedback in editor |
| Prettier | esbenp.prettier-vscode | Auto-format |
| EditorConfig | EditorConfig.EditorConfig | Consistent editor settings |
| Firebase | firebase.firebase-vscode | Rules validation, Firestore tooling |
| GitLens | eamodio.gitlens | Git insight |
| YAML | redhat.vscode-yaml | Workflow/config editing |
| GemCommit | gemcommit | Assist with **Conventional Commits** and commit messages |

---

## 🆘 Troubleshooting (Most common → least)

| Symptom | Likely Cause | Fix |
|---|---|---|
| Build fails on **Continuous Integration (CI)** but not locally | Node.js version mismatch / missing lockfile usage | Use `nvm use` (reads `.nvmrc`), run `npm ci`; pin the same Node.js in CI (e.g., `actions/setup-node` with `.nvmrc`). |
| `Cannot find module ...` at runtime | Dependencies not installed / mixed package managers | Remove `node_modules` and lockfile cache; run `npm ci`; avoid mixing **Node Package Manager (npm)** / pnpm / yarn. |
| Emulator connection errors | Emulators not running / wrong ports / firewall | `firebase emulators:start`; verify ports table; check `firebase.json`; allow local firewall; use `--only` to isolate services. |
| “Project not found” / wrong project selected | Wrong Firebase project alias or config | `firebase projects:list`; `firebase use --add`; confirm `.firebaserc` and active project. |
| Firestore `PERMISSION_DENIED` | Security rules deny access / wrong authentication context | Use emulator auth or signed-in user; fix `firestore.rules`; confirm you’re hitting **emulator vs production** as intended. |
| Authentication fails locally | Using production endpoints instead of emulator | Set `FIREBASE_AUTH_EMULATOR_HOST=localhost:9099`; call `useEmulator(...)` in code; clear session and retry. |
| `command not found: firebase` | Firebase **Command Line Interface (CLI)** not installed / `PATH` issue | `npm i -g firebase-tools`; re-open shell; or run `npx firebase ...`. |
| Cross-Origin Resource Sharing (CORS) errors calling Functions | Missing CORS headers on HTTP Functions | Add CORS middleware / `onRequest` with allowed origins; test with `curl`; avoid `*` in production. |
| Port already in use (`EADDRINUSE`) | Another process bound to the port | `lsof -i :<port>` (or `netstat` on Windows) and kill; or change ports in `firebase.json`. |
| Git push rejected to protected branches | Branch protection rules block direct pushes | Open a **Pull Request (PR)**; obtain required reviews; keep checks green; avoid direct pushes. |
| Tests pass locally but fail on **Continuous Integration (CI)** | Environment differences (timezones, network, snapshots) | Mock network/time; set required environment variables; run headless; regenerate snapshots deliberately. |
| Husky Git hooks not running | Hooks not installed / permissions not executable | `npm run setup`; `chmod +x .husky/*`; `git config core.hooksPath .husky`. |

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
