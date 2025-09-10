<!--
Thanks for your contribution! Please fill out the sections below so we can review quickly and safely.
Keep changes small and focused; link to tickets/docs; include tests and evidence.
-->

> [!IMPORTANT]
> **Target branch = `Development`.** Open a PR from a short-lived topic branch (`feature/*`, `bugfix/*`, `hotfix/*`). Let CI run (install → lint → test → build), obtain review, then **Squash merge**. Never push directly to protected branches.

> [!CAUTION]
> **No secrets** in code, screenshots, or logs. Resolve any security, secret-scanning, or license alerts **before** requesting review.

# 📝 Summary
<!-- One or two sentences: what changed and why. -->
- Purpose:
- Context / motivation (link ticket/ADR):

## 🔗 Related Issues
<!-- Use GitHub keywords to auto-close when merged. -->
- Closes #___
- Refs #___

---

## 🎯 Type of change
<!-- Select all that apply. -->
- [ ] 🐞 Bug fix (non-breaking)
- [ ] ✨ New feature (non-breaking)
- [ ] 💥 Breaking change
- [ ] ♻️ Refactor (no functional change)
- [ ] 🧾 Documentation
- [ ] 🧪 Tests only
- [ ] ⚙️ Build/CI
- [ ] 🔒 Security
- [ ] ⚡️ Performance
- [ ] 🧹 Task / Maintenance

> _Maintainers:_ apply labels (e.g., `type: Feature`, `status: Ready for review`, `priority: …`).

---

## 🧾 What changed
<!-- Bullet list of key edits; call out notable files, flags, migrations. -->
- …
- …
- …

## ✅ Validation / Test Plan
<!-- How did you verify this change? Include steps for reviewers to reproduce. Adapt commands for your stack. -->
- Local green: `npm ci` → `npm run lint` → `npm test -- --ci` → `npm run build`
- Manual QA steps:
  1.
  2.
  3.
- Automated tests:
  - [ ] Unit
  - [ ] Integration
  - [ ] E2E
- **Platform priority:** Mobile ➜ Desktop

| Platform   | Versions / Environments                | Result |
|------------|----------------------------------------|--------|
| **Desktop**| macOS / Windows / Linux app (version)  | ✅ / ⚠️ |
| **Mobile** | iOS / Android (device & OS version)    | ✅ / ⚠️ |

---

## 💥 Breaking changes (if any)
<!-- Describe API/UX changes and migration steps. -->
- Impact:
- Migration steps:
- Rollback plan:

## 🔒 Security
<!-- Auth/permission changes, sensitive paths, data handling, SCA/secret-scan results. -->
- Changes:
- Risks & mitigations:

## 📈 Performance (if applicable)
<!-- Provide measurable results if perf-related. -->
- Baseline → New:
- Metrics: TTI __ms · LCP __ms · p95 latency __ms · Memory __MB
- Evidence: links to profiles/traces

## ♿ Accessibility (if UI)
<!-- Keyboard nav, focus, ARIA, contrast, SR output. -->
- Considerations & checks:

---

## 👀 Screenshots / Recordings (UI changes)
**Before:**

**After:**

---

## 🧭 Risk & Rollback
- Risk level: Low / Medium / High
- Rollback: revert PR / disable flag
- Feature flag (if used): name + default

## 📚 Documents & Release Notes
- Docs updated: [ ] (link)
- Changelog entry: [ ] (one-liner below)
- **Release note (one sentence):** …

---

## ✅ Author checklist (pre-review)
- [ ] Scope is single-intent; ≲ ~300 lines & ≤10 files (guideline) or explained.
- [ ] Local green (install → lint → test → build).
- [ ] No secrets; scanners addressed; least-privilege configs.
- [ ] Tests added/updated; QA notes included.
- [ ] README/usage/docs updated if behavior changed.
- [ ] Target branch is **Development**; CI checks are green.
- [ ] **Validated Mobile first, Desktop second** per platform priority.
- [ ] Ready to **Squash merge**; propose Conventional Commit via **GemCommit**.

<!-- Optional: propose the squash commit message -->
<details>
<summary>🧾 Proposed squash commit message (optional)</summary>

`<type>(<scope>): <emoji> <summary>`

Body: 2–4 concise sentences on what/why/impact.

</details>

<!-- Co-author credit (optional). Keep lines exactly formatted for GitHub to recognize. -->
<!--
Co-authored-by: Name <email@example.com>
-->
