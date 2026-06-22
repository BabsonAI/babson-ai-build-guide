# Babson AI Build Guide — AI Patterns Review

A checklist for whether the **AI parts** of your project are built soundly. Separate from the [Production Compliance Check](./production-compliance-check.md), which covers hygiene (secrets, repo, data, toolkit). This one covers the engineering: did you build the RAG / agent / model calls in a way that holds up.

**To use it:** point your AI tool at this file and your project and ask *"Review my project against this. Mark PASS / FAIL / N/A per item and list what to fix."* Skip the sections that don't apply to what you built.

### Two kinds of items

- ✅ **You solve these.** Core to building the pattern correctly. Gaps here mean the thing doesn't really work.
- 👁️ **Awareness only.** Real production concerns — prompt injection, cost controls, rate limits, scaling. **You don't have to solve these.** The AI team handles them at hosting time. You just need to *know they exist* and note anything obvious so we're not surprised.

---

## A. Model calls (applies to everything)

✅ **Solve:**
- [ ] Model calls are wrapped so a failure doesn't crash the whole app — the API *will* error or time out sometimes.
- [ ] If the model is asked for structured output (JSON, etc.), the code handles the case where it comes back malformed instead of assuming it's always valid.
- [ ] The right-sized model is used for the job — not the most expensive model for a trivial task.
- [ ] Prompts live in a readable, editable place — not buried as giant inline strings scattered through the code.

👁️ **Awareness:**
- [ ] Aware that repeated/looping model calls cost money and hit rate limits — the AI team manages budgets and limits, but flag it if your design calls the model a lot.
- [ ] Aware that in production your app will be **logged and traced** (so the AI team can debug and monitor it). Don't log full prompts, model outputs, or anything containing real data — and since you're building on fake data, there shouldn't be anything sensitive to log anyway.

**Anti-patterns:**
- Calling the model and using `response` directly with no error handling.
- `json.loads(model_output)` with no guard for when it isn't JSON.
- Defaulting to the biggest model everywhere "to be safe."

---

## B. Prompting & context

✅ **Solve:**
- [ ] Instructions to the model are clear and specific about what to do and what *not* to do.
- [ ] The context given to the model is relevant and scoped — not "everything I have, just in case."
- [ ] Context stays within reasonable size; you're not dumping an entire database or document set into a single prompt.

👁️ **Awareness:**
- [ ] Aware that **prompt injection** is a thing — if untrusted text (user input, fetched web pages, documents) reaches the model, it can try to hijack instructions. You don't have to defend against it; just know it exists and flag where untrusted input enters. The AI team handles hardening.

**Anti-patterns:**
- Pasting an entire dataset into the prompt instead of retrieving or summarizing the relevant slice.
- One enormous prompt doing five unrelated jobs.

---

## C. RAG (chat-with-your-documents / retrieval)

Skip if your project doesn't retrieve from a document set.

✅ **Solve:**
- [ ] Documents are **chunked** sensibly (by section/paragraph), not dumped as one giant blob or split mid-sentence at arbitrary character counts.
- [ ] The system **retrieves only the relevant chunks** for a question and passes those to the model — it does not stuff all documents into every prompt.
- [ ] The model is instructed to answer **only from the retrieved context**, and to **abstain** — say it doesn't know — when the answer isn't there, rather than making something up. (Abstention is a feature, not a failure: a system that correctly says "that's not in the documents" is working as intended.)
- [ ] Answers can point back to **which source** they came from (even a filename or section), so answers are checkable.
- [ ] You've sanity-checked retrieval quality: for a few real questions, the right chunks actually come back. (Bad retrieval is the #1 reason RAG "doesn't work" — the model is only as good as what it's handed.)

👁️ **Awareness:**
- [ ] Aware that embedding/index choices, scaling the vector store, and re-indexing strategy are production concerns. Start with the simplest embedded option; the AI team handles scaling.

**Anti-patterns:**
- Putting every document into the prompt every time (no actual retrieval — this is not RAG, and it breaks the moment the docs grow).
- No abstention path, so the model confidently invents answers when the docs don't cover the question.
- Chunking by fixed character count with no regard for meaning, shredding sentences and tables.
- Never checking whether retrieval returns the right material.

---

## D. Agents & tool use

Skip if your project doesn't use tools or multi-step agent loops.

✅ **Solve:**
- [ ] Any agent loop has a **stopping condition / step cap** — it can't spin forever.
- [ ] Each tool **handles its own errors** and returns something sane when it fails, rather than crashing the loop.
- [ ] Tools that **change or delete things** require a confirmation step or are off by default — an agent shouldn't take destructive action on its own.
- [ ] The agent's available tools match the task — it's not handed broad, powerful capabilities it doesn't need.
- [ ] The agent can **abstain** — stop and say it can't complete a task — instead of flailing, looping, or fabricating a result when it's stuck or lacks a tool.
- [ ] **A human is in the loop where it matters.** For any consequential action — sending something on a person's behalf, changing a record, anything a user would want to review — the app proposes and a person approves, rather than acting silently. (See the README's fifth shaping question: *should this be automated at all?*)

👁️ **Awareness:**
- [ ] Aware that an agent that can act on real systems has a real blast radius. During build, keep tools pointed at fake data / sandboxes. Real-system access is an AI-team sign-off (see the Build Guide's data rule).

**Anti-patterns:**
- A `while` loop calling the model with no max steps.
- Giving an agent live, destructive tools (delete, send, pay) with no guardrail, on real data.
- One tool failing and taking the whole run down with it.

---

## E. Output handling & safety

✅ **Solve:**
- [ ] Model output is **never run directly** as code, SQL, or shell commands without a check — treat it as untrusted text.
- [ ] User input is **validated** before use (length, type, basic sanity).
- [ ] If the model can return something user-facing and wrong, there's at least a basic check or a graceful "couldn't do that" path.
- [ ] When a model call **fails or abstains**, the user sees a clear, friendly message — not a spinner that never ends, a blank screen, or a raw error/stack trace.

👁️ **Awareness:**
- [ ] Aware that content moderation, abuse handling, and PII scrubbing are production concerns the AI team layers in. Keep your build on fake data so there's no real PII in the first place.

**Anti-patterns:**
- `exec()` / raw SQL / shell from model output, unchecked.
- Trusting the model to always return something safe and well-formed.

---

## F. Evals — does it actually work, provably? (required)

This is not optional and not "I tried it once." If you can't show your project works on a fixed set of cases, you can't show it still works after you change something — and neither can we. Both pieces below are required for anything that gets hosted.

✅ **Solve:**
- [ ] **A test suite for core functionality.** Automated tests that check the non-AI parts behave: the API returns what it should, data loads, bad input is rejected, the happy path works end to end. Run with one command (`pytest`, `npm test`, or equivalent).
- [ ] **Golden prompt tests.** A fixed, version-controlled set of representative inputs paired with the expected or acceptable output, that you re-run to catch regressions. At minimum cover: a few normal cases, at least one **abstention case** (input where the right answer is "I can't help with that" / "that's not in the documents"), and at least one tricky/edge case. When you change a prompt, model, or retrieval setting, you re-run these and see what moved.
- [ ] The golden set lives in the repo (e.g. a `tests/` folder or an `evals/` file), so anyone — including the AI team — can run it and see the project pass.
- [ ] You've **actually run both** and they pass on the current version.

Golden prompt tests don't need exact string matching — for open-ended output, checking that the answer contains the right facts, or that it correctly abstained, is enough. The point is a repeatable yardstick, not perfection.

👁️ **Awareness:**
- [ ] Aware that richer evaluation (scoring quality at scale, automated grading, eval datasets that grow over time) is something the AI team can help layer in. Your job is to ship the starter golden set; it grows from there.

**Anti-patterns:**
- "I ran it once and it worked" as the only evidence.
- No abstention case in the eval set, so you never notice when the model starts confidently answering things it shouldn't.
- Changing the prompt or model and having no way to tell if you made it better or worse.
- Tests that don't actually run (broken, or were never executed).

---

## G. Graceful degradation (what happens when the AI fails)

The single biggest thing separating a demo from a product: a demo assumes the model works; a product plans for when it doesn't. The model **will** sometimes be slow, down, rate-limited, or just wrong. Decide what your app does in each case.

✅ **Solve:**
- [ ] When a model call **fails or times out**, the app recovers — a clear message, a retry, or a sensible fallback — rather than crashing or hanging forever.
- [ ] When the model returns something **wrong or malformed**, there's a check or a graceful "couldn't do that" path (overlaps with structured-output handling in Section A).
- [ ] When the model **abstains** (correctly says it can't answer), that's treated as a normal, handled outcome — not an error state.
- [ ] The user always sees *something* intelligible — never an endless spinner, blank screen, or raw stack trace.

**Anti-patterns:**
- The whole app dies if one model call errors.
- A spinner that spins forever when the API is slow or down.
- Treating "I don't know" as a crash instead of a valid answer.

---

## H. Cost & efficiency

Mostly awareness for now — the AI team manages real budgets and rate limits. You just need rough cost-sense.

✅ **Solve:**
- [ ] The right-sized model is used per task (don't reach for the biggest model for simple work — this overlaps with Section A and matters most for anything that runs often).
- [ ] Obviously repeated, identical model calls are avoided or cached where it's easy.

👁️ **Awareness:**
- [ ] You can roughly answer **"what does one use of this cost?"** — e.g. "one model call per question" vs. "fifty calls in a loop." You don't need exact numbers; you need to not be surprised.
- [ ] Aware that anything calling the model in a loop, on a schedule, or per-item over a large batch can get expensive fast — flag these for the AI team.

**Anti-patterns:**
- Calling the model once per row over a 10,000-row file with no batching or summarizing first.
- Re-asking the model the same thing repeatedly instead of reusing the answer.

---

## Reporting format

Ask the AI to return:

- **Verdict:** Pattern is sound / Sound after fixes / Needs rework
- **✅ Fixes you own:** failed solve-items, what's wrong, smallest fix for each.
- **👁️ Flag for AI team:** awareness items that apply to this project (e.g. "takes untrusted user input," "calls the model in a loop," "would eventually need a real vector store"). These aren't yours to fix — just surfaced so the AI team knows.

The point: the ✅ items are how you know your AI build is real. The 👁️ items are how the AI team knows what to watch for when they take it to production. You don't have to solve the hard production problems — you just have to build the core pattern well and tell us where the sharp edges are.
