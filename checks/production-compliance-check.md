# Babson AI Build Guide — Production Compliance Check

A checklist for auditing a project against the Babson AI Build Guide before submitting it for hosting. Covers the hygiene that makes a project hostable: toolkit, storage, data, secrets, version control, hand-off, and shape. For whether the *AI parts* are built soundly, use the [AI Patterns Review](./ai-patterns-review.md). When you think you're ready to submit, run the [Submission Readiness Check](./submission-readiness-check.md).

**To use it:** point your AI tool at this file and your project and ask *"Check my project against this. For each item, mark PASS / FAIL / N/A and list the gaps."* Or run through it yourself.

This checks whether a project is in shape for **Babson hosting**. It does not judge whether the idea is good — only whether we can host it.

---

## 1. Toolkit

- [ ] Built in **Python** or **JavaScript/TypeScript** (not a mix without reason).
- [ ] Web/backend uses **FastAPI** (Python) or **Next.js + Vercel AI SDK** (JS/TS).
- [ ] Models accessed via the **Claude (Anthropic) SDK** or **OpenAI SDK**.
- [ ] Data handled with **pandas** (Python) or standard equivalents; HTTP via `requests`/`fetch`.
- [ ] No tool, framework, or platform outside the approved toolkit — or, if there is one, it's noted with a reason and flagged for AI-team review.

## 2. Storage

- [ ] Uses **SQLite** (or no persistence at all) unless a bigger database is justified.
- [ ] If a heavier database is used, there's a stated reason SQLite was insufficient.

## 3. Data

Synthetic data is the default and needs no sign-off — it's the fast path. Real or live data is a legitimate choice when the project genuinely needs it, but it's gated, and **this is where the gate is enforced.**

**Default path — synthetic:**
- [ ] Runs entirely on **synthetic / fake data**.
- [ ] No connection to real accounts, live systems, or real personal data.
- [ ] Anything labeled "fake" is genuinely synthetic — not a real export with names swapped (real patterns and identifiers survive that).

**Real-data path — only if the project needs it, and then *all* of these:**
- [ ] A written **campus use case** for why synthetic data won't do.
- [ ] A named **durable owner** (staff/faculty sponsor) accountable for the data long-term — not a graduating student.
- [ ] **AI-team sign-off documented** before the connection is built or hosted.
- [ ] **Data classification noted** — which sensitive categories it touches (student/FERPA, employee/HR, health, financial) so handling rules apply.
- [ ] **Least access:** only the data the project needs; real data is never copied into the repo, fixtures, example inputs, or logs.

If real data is involved and any box above is unchecked, the project is **not hostable** — a hard blocker, same tier as secrets in code.

## 4. Secrets

- [ ] **No API keys, tokens, or passwords** hardcoded anywhere in the code.
- [ ] **No secrets committed** to git history (check, don't assume).
- [ ] Secrets come from environment variables / a gitignored `.env`.
- [ ] Model access is configured so it can swap to **Babson-provisioned keys** at hosting time without a rewrite.

## 5. Version control

- [ ] Project lives in a **GitHub repo**.
- [ ] `.gitignore` excludes secrets, virtual environments, and local data files.

## 6. Hand-off readiness

- [ ] Has a **`README.md`** stating: what it does, how to run it, and what data is mocked.
- [ ] Someone other than the author could run it from the README alone.
- [ ] Dependencies are declared (`requirements.txt`, `package.json`, or equivalent).

## 7. Shape (architecture)

- [ ] Its trigger type is clear: **interactive / scheduled / event-triggered / batch**.
- [ ] It does the simplest thing that works — no unnecessary services, frameworks, or moving parts.
- [ ] If it became real, there's an answer to **who would own it**.

---

## Reporting format

When an AI checks a project against this, ask it to return:

- **Verdict:** Hostable as-is / Hostable after fixes / Not yet
- **Gaps:** each failed item, what's wrong, and the smallest change to fix it
- **Blockers vs. nits:** which gaps actually prevent hosting (secrets in code, real data, not in a repo) vs. which are minor (missing README detail)

The hard blockers are: **secrets in the code, real/live data without sign-off, or not being in a repo.** Everything else is usually a quick fix.
