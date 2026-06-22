# Data Guidance

Two questions decide how your project handles data: **what data you build on** (fake or real) and **how you store it** (persistence). This guide covers both, plus how to make synthetic data that's good enough to build on.

The one hard rule lives elsewhere: real or live data is gated, and that gate is enforced in the [Production Compliance Check](../checks/production-compliance-check.md). This guide is the *why* and the *how*; that check is the *line*.

---

## What data you build on

**Default: synthetic (fake) data.** It's the faster path, not just the safer one — no privacy strings, no access requests, no review delays. You can build and prove the whole idea without waiting on anyone.

Synthetic data is enough more often than people expect. If your logic depends on data of the right *shape* rather than *specific real records*, fake data proves it completely.

**When you genuinely need real data.** Some projects can't be proven on fake data — the value *is* the real records (answering questions over an actual document set, analyzing real historical data). That's legitimate; real data isn't forbidden, it's a deliberate, gated choice. To use it in a hosted project you bring a campus use case, a durable owner, and AI-team sign-off — see [Production Compliance Check §3](../checks/production-compliance-check.md).

**The trap to avoid:** reaching for real data just to make a demo feel more impressive. If you don't *need* the real records, don't take on the risk. And data isn't "fake" if it's a real export with names swapped — real patterns and identifiers survive that.

---

## Making good synthetic data

Fake data should look and behave like the real thing without being it:

- **Match the shape, not the contents.** Same columns, types, ranges, and relationships as the real data — generated, not copied.
- **Use a generator.** Python's [Faker](https://faker.readthedocs.io/) produces realistic names, emails, dates, and addresses; pair it with `pandas` for tabular data. For domain-specific data, write a small script that encodes the real rules (valid grade ranges, plausible enrollment patterns).
- **Make it reproducible.** Seed the generator and commit the script (or the generated file) so anyone — including the AI team — can regenerate the same dataset. The repo should never depend on data only you have.
- **Cover the edge cases.** Include empty, malformed, and out-of-range rows so you build and test against them from the start.

---

## How you store it: persistence and tradeoffs

Start with the least storage that works and move up only when you can say why. Most projects need far less than they reach for.

| Option | Use it when | Tradeoff |
|--------|-------------|----------|
| **No persistence** (in-memory) | Nothing needs to be remembered between runs or requests | Simplest, nothing to manage — data's gone when the process stops |
| **Files** (CSV / JSON via `pandas`) | Small, mostly-read data; config; generated datasets | Transparent and easy; poor for concurrent writes or relational data |
| **SQLite** (the default) | You need real queries, relationships, or to persist state | One file, nothing to run, goes far; one writer at a time, not for high concurrency |
| **A bigger database** (Postgres, etc.) | Many concurrent writers, large scale, or a real SQLite limit hit | More power, more to run — only when you can name what SQLite couldn't do |

**The default is SQLite.** One file, no server to manage, and it handles more than most campus projects will ever throw at it. The honest test for moving up: *can you say, specifically, why SQLite stopped working?* "It might not scale" isn't a reason yet — "twenty people writing at once" is.

**Keep storage swappable.** Don't scatter raw SQL or file paths through your code — put data access in one place. When the AI team hosts it, the storage layer may move (e.g. to a managed database); a clean seam makes that a small change, not a rewrite.

**Never persist real data into the repo.** Data generated from synthetic sources (including SQLite files built from it) is fine to commit or regenerate. Real data — and anything derived from it — stays out of the repo, fixtures, and logs (see the [Production Compliance Check](../checks/production-compliance-check.md)).

---

[← Back to the Build Guide](../README.md)
