# Babson AI Build Guide — AI Patterns Review

The verification checklist for whether the **AI parts** of your project are built soundly. This is the terse version — for the *why and how* behind each item, see [AI Patterns](../guides/ai-patterns.md). For hygiene (secrets, repo, data, toolkit), use the [Production Compliance Check](./production-compliance-check.md).

**To use it:** point your AI tool at this file and your project and ask *"Review my project against this. Mark PASS / FAIL / N/A per item and list what to fix."* Skip sections that don't apply.

- ✅ **You solve these.** Gaps mean it doesn't really work.
- 👁️ **Awareness only** — the AI team handles these at hosting; just know they exist and flag where they apply.

---

## A. Model calls (applies to everything)
- [ ] ✅ Model calls are wrapped — a failure doesn't crash the app.
- [ ] ✅ Malformed structured output (e.g. non-JSON) is handled, not assumed valid.
- [ ] ✅ Right-sized model for the job, not the biggest by default.
- [ ] ✅ Prompts live somewhere readable and editable, not buried inline.
- [ ] 👁️ Flagged if the design calls the model a lot (budgets / rate limits).
- [ ] 👁️ No real data in logs or traces (you're on fake data, so nothing sensitive).

## B. Prompting & context
- [ ] ✅ Instructions are clear about what to do and what *not* to do.
- [ ] ✅ Context is relevant, scoped, and within reasonable size — not the whole dataset.
- [ ] 👁️ Flagged where untrusted input reaches the model (prompt injection).

## C. RAG — skip if no document retrieval
- [ ] ✅ Documents chunked sensibly (by section/paragraph), not one blob or mid-sentence.
- [ ] ✅ Retrieves only the relevant chunks — not all documents into every prompt.
- [ ] ✅ Answers only from retrieved context, and **abstains** when the answer isn't there.
- [ ] ✅ Answers cite their source (filename / section).
- [ ] ✅ Retrieval quality sanity-checked on a few real questions.
- [ ] 👁️ Embedding / index / scaling left to the AI team (simplest embedded option for now).

## D. Agents & tool use — skip if no tools/loops

*Plain code or a framework like LangGraph / LangChain — the items below apply either way.*

- [ ] ✅ Agent loops have a stopping condition / step cap.
- [ ] ✅ Each tool handles its own errors without crashing the loop.
- [ ] ✅ Destructive tools require confirmation or are off by default.
- [ ] ✅ Tools match the task — no unneeded broad capabilities.
- [ ] ✅ The agent can abstain instead of flailing or fabricating.
- [ ] ✅ A human approves consequential actions (the app proposes; a person confirms).
- [ ] 👁️ Tools kept on fake data / sandboxes during build; real-system access is a sign-off.

## E. Output handling & safety
- [ ] ✅ Model output is never run (code / SQL / shell) without a check.
- [ ] ✅ User input is validated (length, type, basic sanity).
- [ ] ✅ Wrong or user-facing output has a check or graceful fallback.
- [ ] 👁️ Moderation / abuse / PII scrubbing left to the AI team (fake data = no real PII).

## F. Evals — required
- [ ] ✅ A **functional test suite**, runnable with one command (`pytest`, `npm test`, …).
- [ ] ✅ **Golden prompt tests** in the repo: normal cases + at least one **abstention case** + an edge case.
- [ ] ✅ Both have actually been run and pass on the current version.
- [ ] 👁️ Richer / automated evaluation left to the AI team; ship the starter set.

## G. Graceful degradation
- [ ] ✅ Model failure / timeout → clear message, retry, or fallback (no crash or hang).
- [ ] ✅ Wrong or malformed output → a check or graceful path.
- [ ] ✅ Abstention treated as a normal outcome, not an error.
- [ ] ✅ User always sees something intelligible — never an endless spinner or raw stack trace.

## H. Cost & efficiency
- [ ] ✅ Right-sized model per task; obvious repeated identical calls avoided or cached.
- [ ] 👁️ Can roughly answer "what does one use cost?"; loop / schedule / batch calls flagged.

---

## Reporting format

Ask the AI to return:

- **Verdict:** Pattern is sound / Sound after fixes / Needs rework
- **✅ Fixes you own:** failed solve-items, what's wrong, smallest fix for each.
- **👁️ Flag for AI team:** awareness items that apply (untrusted input, model-in-a-loop, would-need-a-real-vector-store, …) — surfaced, not solved.

The ✅ items are how you know your AI build is real; the 👁️ items are how the AI team knows what to watch for. Build the core pattern well and tell us where the sharp edges are. The reasoning behind every item is in [AI Patterns](../guides/ai-patterns.md).
