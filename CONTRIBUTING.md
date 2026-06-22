# Contributing

This repo is maintained by the **BabsonAI team**. It collects guidance and a path-to-production framework for AI projects that Babson hosts.

## Who can contribute

Anyone may open an issue or a pull request, but only BabsonAI team members with write access can merge. `main` is protected — changes land through a reviewed pull request, not direct pushes.

## What good contributions look like

Keep contributions **build-first, hostable, and useful** — the same bar the guide sets for projects:

- **Add an example** to [`/examples`](./examples) — a real build, in its own folder, with a short README on what it is and why it's shaped that way.
- **Add a prompt scaffold** to [`/prompt-templates`](./prompt-templates) for a shape that keeps recurring.
- **Add a diagram** of a common architecture to [`/diagrams`](./diagrams).
- **Improve a guide or check** if something tripped you up or went stale.

## Ground rules

- No secrets, keys, or real personal data in any contribution — including examples and test fixtures. Build on synthetic data (see the [Production Compliance Check](./checks/production-compliance-check.md)).
- Keep the toolkit lean; if you introduce a tool outside the approved set, say why.
- Write so the next person can follow it without asking you.

## How to propose a change

1. Fork the repo (or branch, if you have write access).
2. Make your change on a branch.
3. Open a pull request describing what changed and why.
4. A BabsonAI team member reviews and merges.
