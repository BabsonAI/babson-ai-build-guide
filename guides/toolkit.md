# The Toolkit

A deliberately short, approved stack. These tools cover most projects and host cleanly, so a build that sticks to them is one the AI team can pick up without surprises. Pick the language that fits you — Python or JS/TS, both supported.

**Languages** — Python (data, backends, agents, scripting) or JavaScript/TypeScript (web-facing, pairs with Vercel).

**Building it** — FastAPI (Python) for a backend/API; Next.js + Vercel AI SDK (JS/TS) for web apps with AI built in.

**Models** — Claude (Anthropic SDK) or OpenAI SDK. Use your own keys while you build; when we host it, it runs on Babson-provisioned keys — a small swap at hosting time, not a rebuild. See [Getting Model Access](./getting-model-access.md).

**Data & HTTP** — pandas (Python) for tables; `requests` / `fetch` for calling other services. More on storage and persistence in [Data Guidance](./data-guidance.md).

**Storage** — SQLite by default: one file, nothing to run, goes further than expected. Move to a bigger database when you can say why SQLite stopped working.

**Version control** — Git + GitHub, from the start.

**Want something not listed?** Check with the AI team before going deep. If it's hostable, we'll usually say yes — the point isn't to restrict you, it's to keep builds on a stack we can actually run.

> Just need to move fast and browse tools? The hackathon quick-start — [github.com/BabsonAI/hack_quickstart](https://github.com/BabsonAI/hack_quickstart) — is built for shipping a demo in an afternoon. This guide is the next step: the subset that becomes a hosted product.

The [Production Compliance Check](../checks/production-compliance-check.md) verifies a project against this toolkit.

---

[← Back to the Build Guide](../README.md)
