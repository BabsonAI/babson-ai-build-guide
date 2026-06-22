# AI Patterns — building the AI parts soundly

The difference between an AI demo and an AI product is in the parts you don't see in a happy-path click-through: what happens when the model errors, returns garbage, or is asked something it can't answer. This guide covers how to build the common AI patterns — model calls, prompting, RAG, agents, output handling, evals, and graceful degradation — so they hold up.

It's the *why and how*. When you want to verify a build against it, the [AI Patterns Review](../checks/ai-patterns-review.md) is the checklist version.

Two kinds of concerns run through all of this:

- **What you solve** — building the pattern correctly. Get these wrong and the thing doesn't really work.
- **What the AI team handles** — production concerns like prompt injection, cost controls, rate limits, and scaling. You don't have to solve these; you just need to know they exist and flag where they apply, so nobody's surprised at hosting time.

---

## Model calls

Every AI feature rests on calls to a model, and the model is the least reliable part of your stack — it *will* sometimes error, time out, or return something malformed. Build for that from the start:

- **Wrap every call** so a failure doesn't crash the app. (Anti-pattern: using `response` directly with no error handling.)
- **Guard structured output.** If you ask for JSON, handle the case where it comes back as not-JSON instead of assuming it's valid. (Anti-pattern: `json.loads(model_output)` with no fallback.)
- **Right-size the model.** Use the model that fits the job, not the biggest one for a trivial task. (Anti-pattern: defaulting to the largest model everywhere "to be safe.")
- **Keep prompts editable.** Put them somewhere readable, not buried as giant inline strings scattered through the code.

*The AI team handles:* budgets and rate limits (flag it if your design calls the model a lot); logging and tracing in production (don't log full prompts, outputs, or anything with real data — and since you build on fake data, there's nothing sensitive to log anyway).

---

## Prompting & context

- **Be specific.** Tell the model what to do *and* what not to do.
- **Scope the context.** Give it what's relevant, not "everything I have, just in case." Don't dump an entire database or document set into one prompt — retrieve or summarize the relevant slice. (Anti-pattern: one enormous prompt doing five unrelated jobs.)

*The AI team handles:* prompt-injection hardening. Just know it exists — untrusted text (user input, fetched pages, documents) can try to hijack the model's instructions — and flag where untrusted input enters.

---

## RAG (chat-with-your-documents / retrieval)

If your project answers questions over a document set, retrieval quality is everything — bad retrieval is the #1 reason RAG "doesn't work," because the model is only as good as what it's handed.

- **Chunk sensibly** — by section or paragraph, not one giant blob and not sliced mid-sentence at arbitrary character counts.
- **Retrieve only the relevant chunks** for a question and pass those — don't stuff all documents into every prompt. (If you put every document into every prompt, that's not RAG, and it breaks the moment the docs grow.)
- **Answer only from the retrieved context, and abstain when the answer isn't there** rather than inventing one. Abstention is a feature, not a failure: a system that correctly says "that's not in the documents" is working as intended.
- **Cite the source** — even a filename or section — so answers are checkable.
- **Sanity-check retrieval:** for a few real questions, do the right chunks actually come back?

*The AI team handles:* embedding/index choice, scaling the vector store, re-indexing. Start with the simplest embedded option.

---

## Agents & tool use

If your project uses tools or multi-step loops:

- **Cap the loop.** Every agent loop needs a stopping condition or step cap — it can't spin forever. (Anti-pattern: a `while` loop calling the model with no max steps.)
- **Each tool handles its own errors** and returns something sane on failure, rather than crashing the whole run.
- **Guard destructive actions.** Tools that change or delete things need a confirmation step or are off by default. (Anti-pattern: giving an agent live delete/send/pay tools on real data with no guardrail.)
- **Least capability.** The agent's tools match the task — don't hand it broad, powerful capabilities it doesn't need.
- **Let it abstain** — stop and say it can't, instead of flailing or fabricating when it's stuck or lacks a tool.
- **Keep a human in the loop where it matters.** For any consequential action — sending something for a person, changing a record, anything someone would want to review — the app proposes and a person approves. (This is the README's fifth shaping question: *should this be automated at all?*)

*The AI team handles:* the blast radius of an agent acting on real systems. During build, keep tools pointed at fake data or sandboxes; real-system access is an AI-team sign-off (see [Data Guidance](./data-guidance.md)).

---

## Output handling & safety

- **Never run model output directly** as code, SQL, or shell without a check — treat it as untrusted text. (Anti-pattern: `exec()` / raw SQL / shell from model output, unchecked.)
- **Validate user input** before use (length, type, basic sanity).
- **Check user-facing output.** If the model can return something wrong, have at least a basic check or a graceful "couldn't do that" path.

*The AI team handles:* content moderation, abuse handling, PII scrubbing. Building on fake data means there's no real PII in the first place.

---

## Evals — does it actually work, provably?

This is the one that's **required, not optional**, for anything hosted — and it's not "I tried it once and it worked." If you can't show your project works on a fixed set of cases, you can't show it still works after you change something, and neither can we.

Two pieces:

1. **A test suite for core functionality.** Automated tests that the non-AI parts behave — the API returns what it should, data loads, bad input is rejected, the happy path works end to end. Runnable with one command (`pytest`, `npm test`, or equivalent).
2. **Golden prompt tests.** A fixed, version-controlled set of representative inputs paired with their expected or acceptable outputs, that you re-run to catch regressions. Cover a few normal cases, **at least one abstention case** (where the right answer is "I can't help with that"), and at least one tricky/edge case. Change a prompt, model, or retrieval setting → re-run → see what moved.

They don't need exact string matching — for open-ended output, checking the answer contains the right facts (or correctly abstained) is enough. The point is a repeatable yardstick, not perfection. Keep the golden set in the repo (a `tests/` or `evals/` folder) so anyone, including the AI team, can run it.

*The AI team handles:* richer evaluation — scoring quality at scale, automated grading, eval sets that grow over time. Ship the starter golden set; it grows from there.

---

## Graceful degradation (what happens when the AI fails)

The single biggest thing separating a demo from a product: a demo assumes the model works; a product plans for when it doesn't. The model **will** sometimes be slow, down, rate-limited, or wrong. Decide what your app does in each case:

- **On failure or timeout** — recover with a clear message, a retry, or a sensible fallback, rather than crashing or hanging.
- **On wrong or malformed output** — a check or a graceful "couldn't do that" path.
- **On abstention** — treat "I can't answer that" as a normal, handled outcome, not an error.
- **Always show something intelligible** — never an endless spinner, blank screen, or raw stack trace.

---

## Cost & efficiency

Mostly the AI team's concern at hosting time, but build with rough cost-sense:

- **Right-size the model per task** (overlaps with model calls; matters most for anything that runs often).
- **Avoid obviously repeated identical calls** — cache where it's easy.
- **Know what one use costs** — roughly "one call per question" vs. "fifty calls in a loop." Anything that calls the model in a loop, on a schedule, or per-item over a big batch can get expensive fast — flag those. (Anti-pattern: one model call per row over a 10,000-row file with no batching.)

---

[← Back to the Build Guide](../README.md) · [Verify against the AI Patterns Review →](../checks/ai-patterns-review.md)
