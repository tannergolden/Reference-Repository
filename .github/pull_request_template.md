<!--
PR Template — Fill out EVERY section as fully as possible.
Do not delete sections. If something is not applicable, write N/A.
Keep PRs small; link ticket/ADR; include tests and evidence.
Title: use Conventional Commit. No secrets in code/logs/screenshots.
-->

<!-- **Target:** `Development` · Short-lived branch (`feature/*`, `bugfix/*`, `hotfix/*`) · Let CI run (install → lint → test → build) · **Squash merge** only -->

# 📝 Summary
- What changed (2-4 sentences):
- Why (2 sentences, link ticket/ADR):

---

## 🎯 Type
- [ ] 🧹 Maintenance
- [ ] 💥 Breaking change
- [ ] 🔒 Security
- [ ] ⚡️ Performance
- [ ] ✨ Feature
- [ ] 🐞 Bug fix
- [ ] ♻️ Refactor
- [ ] 🧪 Tests only
- [ ] ⚙️ Build/CI
- [ ] 📚 Docs

<!-- Optional: delete if none -->
## 🔗 Linked issues
- Closes #___
- Refs #___

---

## 🧾 What changed (3–6 bullets)
- …
- …
- …
- …
- …
- …

---

## ✅ Validation / Testing
- Local: `npm ci && npm run lint && npm test -- --ci && npm run build`
- Manual Quality Assurance (QA) steps:

1.

2.

3.

- Automated: [ ] Unit · [ ] Integration · [ ] E2E
- Platform priority: **Desktop ➜ Mobile**
| Platform   | Env/Version                         | Result |
|------------|-------------------------------------|--------|
| Desktop    | macOS / Windows / Linux (version)   | ✅ / ⚠️ |
| Mobile     | iOS / Android (device & OS version) | ✅ / ⚠️ |

---

## 💥 Breaking / Risk
- Breaking impact:
- Migration steps:
- Risk level: Low / Medium / High
- Rollback plan: Revert PR / disable flag

---

## 🔒 Security / 📈 Performance / ♿ Accessibility (if applicable)
- Security: [ ] None · [ ] Changes, risks & mitigations:
- Performance: [ ] None · [ ] Baseline → New; metrics/evidence:
- Accessibility: [ ] None · [ ] Checks/notes (keyboard, focus, ARIA, contrast, SR):

---

## 👀 Evidence (UI or behavior)
**Before:**  
**After:**  
_Include screenshots, recordings, traces, or logs (redact secrets)._

---

## 🧭 Rollout
- Feature flag: name + default
- Dependencies: linked PRs/migrations/config toggles
- Owner/area: team or codeowner

---

## 📚 Documents & Release Notes
- Docs updated: [ ] link
- Release note (one sentence): …

---

## ✅ Author checklist (pre-review)
- [ ] Single-intent PR; ideally ≤1,000 lines & ≤20 files (explain if larger)
- [ ] Local green (install → lint → test → build)
- [ ] No secrets; scanners clean; license alerts resolved
- [ ] Tests added/updated; QA notes included
- [ ] README/usage/docs updated if behavior changed
- [ ] Target branch is **Development**; CI green
- [ ] Mobile validated first, then desktop
- [ ] Ready to **Squash merge**; propose Conventional Commit via **GemCommit**

---

## 🔍 Reviewer guidance
When reviewing this PR, confirm that:
- The scope is clear and the PR size is appropriate.
- Tests cover the change paths and CI is passing.
- Security, performance, and accessibility impacts are reviewed if relevant.
- Documentation and release notes are accurate and sufficient.
- The title follows Conventional Commit standards, ensuring a clean squash history.

<details>
<summary>🧾 Proposed squash commit (optional)</summary>

`<type>(<scope>): <emoji> <summary>`

Body: 2–4 short sentences on what/why/impact.

</details>

<!-- Keep lines exactly as-is for GitHub to credit co-authors. -->
<!--
Co-authored-by: Name <email@example.com>
-->
