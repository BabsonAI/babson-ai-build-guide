# Babson AI Build Guide

How to build an AI project that Babson will host for you.

Build whatever you want, however you want. This guide only covers one thing: getting a project onto Babson's infrastructure so other people can use it. You build it; we host, secure, and run it. The requirements below exist so we can actually do that.

---

## Contents

1. [Who this is for](#1-who-this-is-for)
2. [Scope](#2-scope)
3. [Demo vs. product](#3-demo-vs-product)
4. [Before you build](#4-before-you-build)
5. [Five questions to answer first](#5-five-questions)
6. [The toolkit](#6-the-toolkit)
7. [What makes a project hostable](#7-hostable)
8. [Data](#8-data)
9. [Responsible AI](#9-responsible-ai)
10. [Repo contents](#10-repo-contents)
11. [Path to production](#11-path-to-production)
12. [For AI Fellows](#12-for-fellows)

---

<a name="1-who-this-is-for"></a>
## 1. Who this is for

Grant recipients, AI Fellows, and students who've outgrown no-code tools and want to build something custom that Babson will host. You don't need to be an expert — you need an idea and the willingness to build it so it can leave your laptop.

If you're already a confident builder: skim sections 3–5, then work from 6–9. The scaffolds in [`/prompt-templates`](./prompt-templates) are the fastest start.

---

<a name="2-scope"></a>
## 2. Scope

This guide is the path to *Babson-hosted* production. It is not a rulebook for what you're allowed to build. On your own machine, with your own tools and keys, build anything. The requirements here only apply when you want *us* to host it.

---

<a name="3-demo-vs-product"></a>
## 3. Demo vs. product

A demo works once, for you, while you're watching. A product works every time, for everyone, unattended — without breaking, leaking data, or running up a surprise bill.

The demo is the easy part. Reliability, security, hosting, and cost are the rest of the work, and it's where most projects stall. We handle that part for you, as long as the project is built in a shape we can pick up. That's what the rest of this guide defines.

---

<a name="4-before-you-build"></a>
## 4. Before you build

Check whether you need to build at all. Two no-code tools cover a lot:

- **Microsoft Copilot Studio** — drag-and-drop agents, Babson login, no code.
- **BeaverChat** — chat with models, build lightweight assistants.

If one of these does the job, use it and skip the rest. This guide is for when you've outgrown them.

---

<a name="5-five-questions"></a>
## 5. Five questions to answer first

Answer these in a sentence each and you understand your project's shape. They're also what we'll ask when you submit it.

1. **How does it get used?** Interactive (someone clicks/chats), scheduled, event-triggered, or batch?
2. **What data does it touch?** Use fake data while you build — see [section 8](#8-data).
3. **What does it need to remember?** Nothing, a little, or a lot? Start at the low end.
4. **Should it be automated at all — and where does a human stay in the loop?** Some decisions an AI can make on its own; consequential ones (sending something for a person, changing a record, anything someone would want to review) should be proposed by the app and approved by a person. Decide this before you build.
5. **Who would own it in production?** Not you — a **durable owner**: a staff or faculty sponsor who stays accountable for it after you graduate or move on. For a throwaway experiment, nobody. For anything the campus would rely on, there has to be a named sponsor. See [`/guides/lifecycle-and-ownership.md`](./guides/lifecycle-and-ownership.md).

Deeper version: [`/guides/solution-architecture.md`](./guides/solution-architecture.md). Diagrams: [`/diagrams`](./diagrams).

---

<a name="6-the-toolkit"></a>
## 6. The toolkit

Short on purpose. These cover most projects and host cleanly. Pick the language that fits you — Python or JS/TS, both supported.

**Languages** — Python (data, backends, agents, scripting) or JavaScript/TypeScript (web-facing, pairs with Vercel).

**Building it** — FastAPI (Python) for a backend/API; Next.js + Vercel AI SDK (JS/TS) for web apps with AI built in.

**Models** — Claude (Anthropic SDK) or OpenAI SDK. Use your own keys while you build. When we host it, it runs on Babson-provisioned keys — a small swap at hosting time, not a rebuild. See [`/guides/getting-model-access.md`](./guides/getting-model-access.md).

**Data & HTTP** — pandas (Python) for tables; `requests`/`fetch` for calling other services.

**Storage** — SQLite by default: one file, nothing to run, goes further than expected. Move to a bigger database when you can say why SQLite stopped working.

**Version control** — Git + GitHub, from the start.

Want something not listed? Check with us before going deep. If it's hostable, we'll usually say yes.

> Just need to move fast and browse tools? The hackathon quick-start — [github.com/BabsonAI/hack_quickstart](https://github.com/BabsonAI/hack_quickstart) — is built for shipping a demo in an afternoon. This guide is the next step: the subset that becomes a hosted product.

---

<a name="7-hostable"></a>
## 7. What makes a project hostable

Hit these and we can run it:

- **It's in a GitHub repo.** That's how it reaches us.
- **No secrets in the code.** Keep keys, tokens, and passwords out of your files and commits — we wire in credentials at hosting time.
- **It runs on fake data.** So we can run and review it without exposing anything real.
- **Someone can run it without you.** A short `README.md` — what it does, how to run it, what's mocked — is enough.

Each item is something that would otherwise block hosting. None of it is busywork.

---

<a name="8-data"></a>
## 8. Data

Build on synthetic (fake) data. It's the faster path, not just the safer one — no privacy strings, no access requests, no review delays. You can build and prove the whole idea without waiting on anyone.

Connecting to real or live systems is possible but deliberate, because live data carries real risk. To use it in a hosted project, bring a clear campus use case, a **durable owner** (a staff or faculty sponsor who'll stand behind it long-term — not a student who'll graduate), and a sign-off from the AI team. The bar is about who owns the responsibility, not how good the idea is. Plenty of ideas clear it.

On your own machine, connect whatever you want — this only applies to what we host.

Generating fake data: [`/guides/using-fake-data.md`](./guides/using-fake-data.md).

---

<a name="9-responsible-ai"></a>
## 9. Responsible AI

A few things matter more because this is a university and you may be building for other students, faculty, or staff. You don't have to become an expert — but you do have to know where the lines are and flag anything near them.

- **Student data is regulated (FERPA).** Real student records — grades, enrollment, advising notes, anything tied to an identifiable student — are protected. "I'll just use real data to make the demo realistic" is exactly the trap. Build on fake data; a real-data connection is a sponsor-and-sign-off decision, not yours to make alone.
- **Tell people they're using AI.** A campus-facing tool should be clear that it's AI — not quietly pose as a human or an official source.
- **Academic integrity.** If your tool could be used to complete coursework, think about where the line is between helping someone learn and doing their work for them, and flag it.
- **Bias and fairness (awareness).** Models can produce skewed or unfair output, especially anything that sorts, scores, or evaluates people. If your project does that, flag it — the AI team and the institution weigh in before it goes live.
- **Accessibility (awareness).** If it's campus-facing, it should be usable by people with disabilities — readable, navigable, not dependent on a single sense. Build sensibly and flag it to confirm before broad rollout.

Most of this is awareness-tier: you acknowledge and flag, the AI team and Babson own enforcement. Full detail: [`/guides/responsible-ai.md`](./guides/responsible-ai.md).

---

<a name="10-repo-contents"></a>
## 10. Repo contents

```
babson-ai-build-guide/
├── README.md            this file
├── checks/              the three self-checks you run before submitting
├── prompt-templates/    scaffolds to start a build fast and in-shape
├── examples/            real products & agents built with this framework
├── diagrams/            architecture & flow diagrams (mermaid + excalidraw)
└── guides/              deeper-dive docs linked from here
```

**[`/checks`](./checks)** — Three checklists you (and your AI tool) run before submitting: the **Production Compliance Check** (is it hostable), the **AI Patterns Review** (is the AI built soundly, including required evals), and the **Submission Readiness Check** (are you actually ready to hand it over). Run all three before you submit.

**[`/prompt-templates`](./prompt-templates)** — Drop-in prompts for Claude or your AI coding tool that bootstrap a project already in the right shape: right toolkit, fake data, clean structure, no hardcoded keys. Includes a general new-project starter plus common shapes (chatbot, RAG agent, data agent). *Coming soon.*

**[`/examples`](./examples)** — Real builds, each in its own folder with a short README on what it is and why it's built that way. *Coming soon.*

**[`/diagrams`](./diagrams)** — Architecture and flow diagrams in mermaid (text-based) and excalidraw (exported images). *Coming soon.*

**[`/guides`](./guides)** — Solution architecture, using fake data, getting model access, responsible AI, lifecycle & ownership, and submitting for production.

---

<a name="11-path-to-production"></a>
## 11. Path to production

1. **Build** — your toolkit, your keys, fake data, in a GitHub repo.
2. **Prove** it works — including the required evals (functional tests + golden prompt tests).
3. **Self-check** — run the three checks in [`/checks`](./checks).
4. **Submit** to the AI team ([`/guides/submitting-for-production.md`](./guides/submitting-for-production.md)).
5. **Review** — built in a hostable shape, with a durable owner behind it? It's a go.
6. **Ship** — we package it for the cloud, give it a real address, wire in production keys, secure it, and keep it online.

The infrastructure is on us. You bring a working idea worth shipping. What happens *after* it ships — maintenance, updates, who owns it when you graduate — is covered in [`/guides/lifecycle-and-ownership.md`](./guides/lifecycle-and-ownership.md).

---

<a name="12-for-fellows"></a>
## 12. For AI Fellows

The repo improves the more it's used:

- **Add an example** to [`/examples`](./examples) with a short README.
- **Add a prompt scaffold** to [`/prompt-templates`](./prompt-templates) for a shape that keeps recurring.
- **Add a diagram** of a common architecture.
- **Fix a guide** in [`/guides`](./guides) if something tripped you up.

Open a PR. Keep contributions build-first, hostable, and useful.
