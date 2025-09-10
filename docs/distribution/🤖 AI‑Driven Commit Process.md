# 🤖 AI-Driven Commit Process

Use Artificial Intelligence (AI) to draft **Conventional Commits** that are correct, consistent, and informative—then **you** review and ship. This workflow centers on the **GemCommit** Visual Studio Code (VS Code) extension and is enforced by commitlint in Continuous Integration (CI).

> [!IMPORTANT]
> AI assists, **humans approve**. Every commit is reviewed by you, validated locally, and gated by CI (commitlint + tests). Expand acronyms on first use (e.g., Pull Request (PR), Continuous Integration (CI)).

---

## 🎯 Goals
- Produce **Conventional Commits** automatically while maintaining accuracy and intent.
- Keep commits **small and single-purpose**, with clear `type(scope): summary`.
- Encode **what/why/testing/impact** so release notes and audits are effortless.

---

## 🔧 Prerequisites
- **GemCommit** VS Code extension installed (`gemcommit`).
- **commitlint** with `@commitlint/config-conventional` configured.
- **Husky** (or equivalent) Git hooks enabled to run commitlint locally.
- CI pipeline checks commit messages and runs tests.

> [!TIP]
> If you need to add tools: `npm i -D @commitlint/cli @commitlint/config-conventional husky lint-staged`.

---

## 🧱 Commit Anatomy (Conventional Commits)

```

<type>(<scope>): <summary in imperative or present tense>

\[optional body explaining the why / context]

\[optional footer(s): BREAKING CHANGE:, Co-authored-by:, Closes #123, Refs #456]

````

**Common `type` values**: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `build`, `ci`, `perf`, `revert`.  
**Scope**: short area like `auth`, `api`, `db`, `ui`, `build` (omit if unclear).

---

## 🧭 When to Commit (and how big)
- **One intent per commit.** Split feature work from refactors or formatting.
- **Green locally first** (`npm run lint && npm test`) so CI won’t fail on style/tests.
- Prefer several **small commits** over a single “kitchen-sink” commit.

---

## 💙 GemCommit: How to Use (Step-by-Step)

1. **Stage changes** you want in the commit: `git add -A` (or stage selectively in VS Code).
2. Open VS Code **Command Palette** → **GemCommit: Generate Conventional Commit**.
3. **Provide context** (or let it infer from diffs):
   - What changed and why (ticket/reason)
   - Tests/validation performed
   - Any breaking changes or migrations
4. GemCommit proposes a **Conventional Commit** (type/scope/summary + body).
5. **Review carefully**, edit for accuracy, and apply.
6. Commit and **push**: `git push -u origin HEAD`.
7. Open a **Pull Request (PR)** into `Development`.

> [!CAUTION]
> Do **not** include secrets, tokens, or proprietary data in prompts. Treat AI prompts like public text.

---

## 📋 GemCommit Prompt (copy/paste)

> Use this prompt verbatim inside GemCommit when you want strict, standards-aligned Conventional Commits with emoji and well-structured bodies.

```txt
TITLE
Conventional Commit Generator — Emoji + Recency + Clean (Standards-Aligned, 2–4 Sentence One-Line Body)

ROLE
Generate one Conventional Commits 1.0.0–compliant commit message with exactly one emoji in the subject. Use present-tense descriptive style in the subject (e.g., “Adds…”). Output must follow Git best practices for length and formatting.

GUARANTEES (standards)
- Conventional Commits: `<type>(<scope>): <subject>` with lowercase `type`, optional `(scope)`, and colon+space separator.
- Subject length: aim ≤50 chars; hard cap ≤72 chars; no trailing period.
- Body lines wrapped at ~72 chars only where allowed; the first body paragraph is forced to a single logical line (see BODY section).
- Use Git/GitHub trailers: `BREAKING CHANGE:`, `Co-authored-by:`, `Closes #`, `Refs #` (only when allowed).
- Exactly one emoji in the subject; none in body/footer.

INPUTS
- CHANGE_SUMMARY: one sentence describing what changed and why.
- DIFF: optional unified diff (or concise list of key edits).
- TYPE: optional; one of feat, fix, docs, style, refactor, perf, test, build, ci, chore, revert. Infer if missing.
- SCOPE: optional short scope token (ui, api, db, auth, build, infra, etc.). Infer if missing. Lowercase.
- ALLOW_ISSUE_REFS: optional true|false (default false).
- ISSUE_REFS: optional comma IDs (123,456). Ignored unless ALLOW_ISSUE_REFS=true.
- AUTO_CLOSE: optional true|false (default false). Used only if ALLOW_ISSUE_REFS=true and ISSUE_REFS provided.
- BREAKING: optional details if breaking.
- COAUTHORS: optional lines of "Name <email>".
- SUBJECT_STYLE: optional `present` (default) or `imperative`.

EMOJI MAP (choose first that fits)
- feat: ✨ / 🆕 / 🚀
- fix: 🐛 / 🩹 / 🔧
- docs: 📝 / 📚 / 📖
- style: 🎨 / 💄 / 🧼
- refactor: ♻️ / 🧩 / 🪚
- perf: ⚡️ / 🏎️ / 📈
- test: 🧪 / 🔬 / 🧫
- build: 🏗️ / 🧱 / 📦
- ci: 🤖 / ⚙️ / 🕒
- chore: 🧹 / 🗂️ / 🪛
- revert: ⏪ / 🔙 / ↩️
- security: 🔒 / 🛡️ / 🚨

SANITIZE (inputs)
- Summarize; never copy raw DIFF lines verbatim.
- You may inspect `+`/`-` lines to infer TYPE/SCOPE.
- Ignore diff metadata: `diff --git`, `index`, `---`, `+++`, `@@`, and the exact marker `\ No newline at end of file`.

RECENCY (strict)
1) Treat the bottom-most DIFF hunk as most recent; scan bottom → top.
2) Prefer CODE over DOCS when both exist.
   - DOCS paths: `README*`, `CHANGELOG*`, `CONTRIBUTING*`, `LICENSE*`, `docs/**`, `*.md`, `*.mdx`, `*.rst`, `*.adoc`, `*.txt`.
   - CODE dirs: `src/**`, `app/**`, `lib/**`, `server/**`, `client/**`, `packages/**`, `components/**`.
3) Skip prose-only overview hunks unless all changes are docs.
4) Choose the first eligible hunk; infer TYPE/SCOPE from it.
5) Mention up to 4 secondary details as bullets.

TYPE & SCOPE INFERENCE
- Honor provided TYPE; else infer: add/new ⇒ feat | bug/regression ⇒ fix | docs-only ⇒ docs | rename/format/whitespace ⇒ style | restructure w/o behavior change ⇒ refactor | speed/memory ⇒ perf | tests/spec/snapshots ⇒ test | deps/build tooling/config ⇒ build | workflows/CI ⇒ ci | chores/infra scripts ⇒ chore | undo ⇒ revert
- Derive SCOPE from chosen hunk path (ui, api, auth, db, build). Omit if unclear. Lowercase, short.

FORMAT (exact shape)
Subject (line 1)
- `<type>(<scope>): <emoji> <subject>`
- SUBJECT_STYLE:
  - `present`: start with “Adds…/Fixes…/Refactors…”.
  - `imperative`: start with “add/fix/refactor”.
- Aim ≤50 chars; hard cap ≤72 chars; no trailing period.

Body (paragraph + details)
- Paragraph 1 (one line, 2–4 sentences): produce two to four short sentences that together state what changed, why it was done, and the user/technical impact. Do not insert manual line breaks; keep it on a single logical line. If it becomes long, shorten sentences rather than wrapping.
- Blank line.
- Heading line: `The <thing> includes:` where `<thing>` is derived from the subject.
- Bullets (hyphen+space): 3–6 items, sentence case, end each with a period. Keep each bullet ≤72 chars.

Footer (locked down)
- By default, no issue lines.
- Only include issue lines if ALLOW_ISSUE_REFS=true and ISSUE_REFS is non-empty:
  - If AUTO_CLOSE=true: one per id as `Closes #<id>`.
  - If AUTO_CLOSE=false: one per id as `Refs #<id>`.
- Never invent IDs.
- If BREAKING provided: `BREAKING CHANGE: <details>` on its own line.
- If COAUTHORS provided: one `Co-authored-by: Name <email>` per line.

WHITESPACE (strict)
- No line starts with a space or tab.
- Exactly one blank line between: subject → paragraph and paragraph → heading; one blank line before footer (only if footer exists).
- No extra blank lines elsewhere; blank lines must be truly empty.
- Trim trailing spaces on every line.
- End output with exactly one final newline.

POST-GEN CLEANUP (must run before emitting)
1) De-wrap paragraph 1: replace any internal newlines with single spaces.
2) Sentence count enforcement: ensure paragraph 1 contains 2–4 sentences (split by `.`, `!`, or `?`). If fewer than 2, add a concise impact or rationale sentence. If more than 4, compress to the most informative 4.
3) Conform subject style: if SUBJECT_STYLE=imperative, convert leading verb (“Adds”→“add”, “Fixes”→“fix”, etc.).
4) De-duplicate: remove any line that repeats paragraph 1 (including parenthesized echoes).
5) Strip forbidden refs if ALLOW_ISSUE_REFS=false or ISSUE_REFS empty:
   - Delete lines matching `^(Closes|Fixes|Resolves|Refs|Relates-to)\s+#\d+\b.*` (case-insensitive).
6) Re-apply WHITESPACE rules. Ensure subject ≤72 chars and exactly one emoji.

OUTPUT (emit only this — no commentary/fences)

<subject line>
<blank line>
<one-line paragraph with 2–4 sentences>
<blank line>
The <thing> includes:
- <bullet 1>.
- <bullet 2>.
- <bullet 3>.
- <optional more bullets, up to 6>
```

---

## 🧩 Smart Defaults (mapping branch → type)

| Branch prefix   | Likely `type`              | Example summary                                   |
| --------------- | -------------------------- | ------------------------------------------------- |
| `feature/*`     | `feat`                     | `feat(auth): ✨ add passwordless sign-in`          |
| `bugfix/*`      | `fix`                      | `fix(payments): 🐛 handle webhook retries`        |
| `hotfix/*`      | `fix!` (breaking) or `fix` | `fix!(api): 🐛 sanitize headers to prevent crash` |
| `docs/*`        | `docs`                     | `docs(readme): 📝 add setup instructions`         |
| `experiment/*`  | `feat` or `refactor`       | `refactor(search): ♻️ spike on vector index`      |
| `integration/*` | `chore` or `refactor`      | `chore(repo): 🔧 align tsconfig across packages`  |

> \[!TIP]
> GemCommit can infer type/scope from diffs and branch names—**you** make the final call.

---

## ✅ Good vs. Bad Examples

**Good**

```
feat(auth): ✨ add passwordless sign-in

Motivation: reduce login friction and support email magic links. The change introduces token-based flows with strict expiry and audit logging. Users can request links and complete sign-in without passwords. The architecture remains compatible with multi-factor authentication and rate limiting.
 
The sign-in feature includes:
- New token issuance and verification endpoints.
- Expiry enforcement with replay protection.
- Unit tests for success and failure paths.
```

**Bad**

```
update stuff
```

**Fix with breaking change**

```
fix!(billing): 🐛 normalize currency handling to ISO 4217

This change removes inconsistent currency parsing that caused rounding errors. It standardizes minor units and simplifies formatter logic. Users see consistent totals across checkout and invoices. Integrations will need to align input values.

The currency normalization includes:
- Amount storage in minor units (cents).
- Unified rounding strategy across services.
- Input validation for currency codes.

BREAKING CHANGE: amounts are now stored in minor units (cents); update consumers.
```

---

## 🔗 Commitlint & CI Integration

* **Local**: Husky `commit-msg` runs commitlint—rejects invalid messages.
* **CI**: A job checks commit messages on the PR; merges are blocked if they fail.
* **Changelogs**: Release jobs parse commits to build notes automatically.

**Minimal config (example):**

```jsonc
// commitlint.config.cjs
module.exports = { extends: ["@commitlint/config-conventional"] };
```

**GitHub Actions snippet (checks on PRs):**

```yaml
- name: Commitlint
  run: npx commitlint --from "${{ github.event.pull_request.base.sha }}" --to "${{ github.sha }}"
```

---

## 🧪 Testing Hooks (preflight)

Add to `package.json` (example):

```jsonc
{
  "scripts": {
    "precommit": "lint-staged",
    "test:ci": "vitest run",
    "lint": "eslint ."
  },
  "lint-staged": {
    "*.{ts,tsx,js,jsx}": ["eslint --fix", "vitest related --run"]
  }
}
```

> \[!NOTE]
> Keep local commands and CI identical: `npm ci` → `npm run lint` → `npm test -- --ci` → `npm run build`.

---

## 🧰 DOs & DON’Ts

**DO**

* Keep the **summary** imperative/present: “add”, “fix”, “remove” / “Adds…”.
* Write the **why** in the body; link issues/PRs when allowed.
* Mark **BREAKING CHANGE** explicitly in footers.

**DON’T**

* Don’t paste raw diff lines or secrets into prompts or bodies.
* Don’t combine unrelated changes into one commit.
* Don’t rely on AI output without reading it.

---

## 🆘 Troubleshooting

| Symptom                        | Likely Cause              | Fix                                                                                |
| ------------------------------ | ------------------------- | ---------------------------------------------------------------------------------- |
| Commitlint fails on PR         | Wrong `type`/format       | Adjust to `type(scope): summary`; see config.                                      |
| “Message too long” or noisy    | AI added excess detail    | Keep subject ≤72 chars; move detail into body; enforce 2–4 sentences in paragraph. |
| Wrong `type` suggested         | Branch/diff misleading    | Override manually; `feat` vs `fix` is your call.                                   |
| Missing breaking change notice | Prompt lacked impact info | Add `BREAKING CHANGE:` footer with details.                                        |
| Hook didn’t run                | Husky not set up          | Ensure `.husky/commit-msg` calls commitlint; run `npm run prepare` if needed.      |

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
