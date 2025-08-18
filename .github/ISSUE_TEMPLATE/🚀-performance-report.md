---
name: "\U0001F680 Performance Report"
about: "⚡️ Report slow pages, high latency, memory spikes, or inefficiencies so we
  can measure, reproduce, and optimize performance."
title: "\U0001F3CE️ Performance: <Component/Flow> — <Symptom/Metric> (Platform: <Desktop/Mobile>,
  Application Version: <x.y.z>)"
labels: 'priority: To be determined (TBD), status: Needs triage, type: Performance'
assignees: tannergolden

---

<!--
Thank you for helping us improve performance!

Please provide measurable signals and clear steps. Avoid sharing secrets or personally identifiable information (PII).
-->

## 📌 What this template is for (and not for)

**Use this template for:**  
- Slow load times or app startup delays  
- High memory or CPU usage  
- Animation jank, frame drops, or lag  

**Don’t use this template for:**  
- Product bugs or broken features → use **🐛 Bug Report**  
- Usability or design improvements → use **📝 Feedback Report**  
- Documentation gaps or errors → use **📖 Documentation Report**  
- Accessibility-specific issues → use **♿ Accessibility Report**  
- New functionality requests → use **✨ Feature Request**  
- Security vulnerabilities → follow **SECURITY.md** (responsible disclosure) 

---

## ✍️ Summary
<!-- One or two sentences describing the performance concern. -->
<!-- Example: "Dashboard takes ~6–8s to become interactive on first load over 4G." -->

## 👥 Who Is Affected
<!-- Audience/persona and scope (All / Many / Some / Few). -->
<!-- Example: "New users on mobile over cellular networks." -->

## 🔎 Scenario / Current Experience
<!-- Describe the user journey or workload where the issue appears. Include data size, filters, or account state if relevant. -->

## 🔁 Steps to Reproduce
<!-- Precise, numbered steps to reproduce the performance issue consistently. Include dataset size, concurrency, or iteration counts if relevant. -->
1.
2.
3.
4.

## 📈 Measurements & Evidence
<!-- Provide concrete metrics, timestamps, and artifacts. Attach screenshots/recordings and link traces if available. -->
- Page load metrics: Time To First Byte (TTFB): `__ ms` · Time To Interactive (TTI): `__ ms` · Largest Contentful Paint (LCP): `__ ms`
- Runtime metrics: Central Processing Unit (CPU) usage: `__%` · Memory peak: `__ MB`
- Network metrics: 95th percentile (p95) latency: `__ ms` · Payload size: `__ KB/MB` · Requests per action: `__`
- Logs/traces: link to profiling session / flamegraph / trace ID(s)
- Before vs. after (if you tested a tweak): `baseline → candidate` numbers

## ❗ Actual Result
<!-- What happens now (measured)? -->

## ✅ Expected Result
<!-- Target goals or budgets (for example, p95 < 300 ms, LCP < 2.5 s, memory stable within ±10%). -->

## 🧰 Environment
- Platform: Web / Mobile / Desktop
- Device (for mobile/low-power): e.g., iPhone 15 / Pixel 8 / low-end Android
- Operating System (OS) & version: e.g., macOS 14.5, Windows 11, iOS 17.5, Android 14
- Web Browser & version: e.g., Chrome 128, Safari 17.5, Firefox 128, Edge 128
- Network conditions: Wi-Fi (speed/latency) / Cellular (3G/4G/5G) / throttled profile
- Application version (Semantic Versioning (SemVer)): `x.y.z`
- Branch / Commit SHA (Secure Hash Algorithm): `Development|Preview|Release` / `abcdef1`
- Backend/region (if applicable): e.g., United States-East, Europe-West
- Firebase context (if applicable): Project ID `your-project-id` · Using Firebase Emulator Suite: Yes / No

## 🧪 Diagnostics Performed (Optional)
<!-- Describe any profiling you ran (for example, Performance panel, Lighthouse, React Profiler, flamegraphs, query plans). Attach results if possible. -->

## 🎯 Impact / Severity
- Severity: Critical / High / Medium / Low
- Users affected (estimate): All / Many / Some / Few
- Business impact (optional): conversion/time-to-task/support volume implications

## 🧭 Proposed Direction (Optional)
<!-- Quick ideas to explore (for example, cache layer, pagination, index, code-splitting, image optimization). -->

## 🔗 Additional Context / Links
<!-- Related issues, Pull Requests (PRs), dashboards, runbooks, or documents. -->

---

### ✅ Reporter Checklist
- [ ] I provided measurable metrics (for example, 95th percentile (p95), Time To Interactive (TTI), Largest Contentful Paint (LCP)).
- [ ] I included exact reproduction steps and environment details.
- [ ] I attached evidence (screenshots/recordings/profiles) where possible.
- [ ] I removed or obfuscated secrets and personally identifiable information (PII).
