# Babson AI Build Guide — Submission Readiness Check

The final gate before you hand a project to the Babson AI team for hosting. It's a meta-check: it confirms the other two checks passed, the documentation is real, and the project is genuinely ready to leave your hands.

Run this when you think you're done. If everything here passes, submit. If not, you've got your punch list.

**To use it:** point your AI tool at this file and your project and ask *"Am I ready to submit? Mark each item PASS / FAIL and list what's left."*

The three hard gates are: **the two prior checks passed**, **the README is good**, and **a demo runs on fake data**. Miss any of those and it's not ready, regardless of everything else.

---

## 1. Prior checks passed

- [ ] **[Production Compliance Check](./production-compliance-check.md)** passed — no hard blockers (no secrets in code, no real data without sign-off, in a repo).
- [ ] **[AI Patterns Review](./ai-patterns-review.md)** passed — the AI pattern (RAG / agent / model calls) is built soundly, with abstention and error handling in place.
- [ ] **Evals exist and pass.** A functional test suite *and* a golden prompt test set are in the repo, both run with one command, and both pass on the current version. The golden set includes at least one abstention case. (This is a hard gate — see the AI Patterns Review, Section F.)
- [ ] Any 👁️ awareness items from the patterns review are **written down** for the AI team (untrusted input, loop/cost flags, would-need-a-real-vector-store, etc.) — not solved, just surfaced.

## 2. README & documentation — PASS / FAIL

A project without a usable README is **not ready**, full stop. This is a hard gate. The README must let someone who isn't you understand and run the project.

A **PASS** README has all of:

- [ ] **What it is** — one or two sentences on what the project does and who it's for.
- [ ] **How to run it** — exact setup steps: install, environment variables needed (named, not their values), and the command to start it. Someone following these from a clean machine should get it running.
- [ ] **What data is mocked** — clear statement that it runs on fake/synthetic data, and where that data comes from or how to regenerate it.
- [ ] **Dependencies declared** — `requirements.txt`, `package.json`, or equivalent is present and current.
- [ ] **Shape stated** — how it's triggered (interactive / scheduled / event / batch) and, if it became real, who would own it.
- [ ] **Known limits** — a short, honest note on what it doesn't do or where it's rough.

It's a **FAIL** if any of these are true:

- The README is missing, empty, or still the default template text.
- Setup steps are absent, vague, or you'd have to already know the project to follow them.
- It assumes knowledge only the author has ("just run it the usual way").
- Secrets, keys, or real data values appear anywhere in it.

> Quick test: hand the repo to someone who's never seen it. Can they get it running from the README alone, without asking you a question? If yes, PASS. If they have to ask you anything, FAIL — fix that gap and retest.

## 3. It actually runs

- [ ] A **demo runs end-to-end on fake data** — clone, follow the README, it works.
- [ ] It handles the obvious bad inputs (empty, unexpected, out-of-scope) without crashing.
- [ ] There are a few **example inputs/outputs** someone can try — in the README or a short examples file — so a reviewer can see it work without guessing.

## 4. Ownership & rationale

- [ ] **Why this should be hosted** is stated in a sentence or two — the campus value, not just "it's cool."
- [ ] There is a **durable owner**: a staff or faculty sponsor who stays accountable for the project after you graduate or move on — not just you. (For a throwaway experiment that doesn't need hosting, skip this. For anything the campus will use, it's required. See [`/guides/lifecycle-and-ownership.md`](../guides/lifecycle-and-ownership.md).)
- [ ] If it needs anything beyond fake data or the standard toolkit, the **owner and the ask** are written down for the AI team to weigh.
- [ ] **Responsible-AI flags raised.** If the project touches student data, makes decisions about people, could affect academic integrity, or is campus-facing, those are noted for review (see [`/guides/responsible-ai.md`](../guides/responsible-ai.md)).
- [ ] You know who the **point of contact** is for questions after handoff.

---

## Reporting format

Ask the AI to return:

- **Ready to submit?** Yes / Not yet
- **Hard gate status:** prior checks passed (Y/N), README PASS/FAIL, demo runs (Y/N) — all three must be yes.
- **Punch list:** everything left, smallest fix first.
- **For the AI team:** the awareness flags and any real-data/extra-tool asks, collected in one place so they travel with the submission.

If the three hard gates pass, you're ready. Submit per [`/guides/submitting-for-production.md`](../guides/submitting-for-production.md).
