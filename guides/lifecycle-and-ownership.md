# Lifecycle & Ownership

What happens to a project *after* it's hosted — who keeps it alive, who's accountable, what happens when the builder graduates, and how unused projects get retired. This is the part most build-it/host-it programs skip, and skipping it is how you end up with a pile of running apps nobody owns and nobody can safely turn off.

> **Status:** Parts of this guide describe roles and processes that are proposed and still being formalized with Babson leadership. It's written so builders, sponsors, and the AI team have a shared model to work from. Where it differs from established Babson policy, the policy governs.

---

## The core problem: students graduate; products don't

A student builds something genuinely useful. The campus starts relying on it. Then the student graduates in May and is gone. The model API changes, a dependency breaks, someone reports a bug — and there's no one to call. The app keeps running, slowly rotting, until it fails at the worst possible time or quietly does something wrong for months.

The fix is simple to state and easy to skip: **anything hosted for real has a durable owner who isn't the student.**

---

## Roles

**Builder** — the person (often a student, fellow, or grantee) who creates the project. Owns it through build and submission. *Not* the long-term owner of a hosted product.

**Durable owner / sponsor** — a **staff or faculty member** who agrees to be accountable for the project as long as it's hosted. They don't have to maintain the code themselves, but they own the answers to: *Is this still needed? Is someone keeping it current? Should it be retired?* When the builder leaves, the sponsor is who remains. For anything the campus depends on, a hosted project without a sponsor shouldn't exist.

**AI team** — owns the infrastructure: hosting, security, keys, monitoring, the platform itself. Handles breakages that are platform-level, and flags app-level issues to the sponsor. Not the default owner of every app's *purpose* — that's the sponsor's job.

The split in one line: **the AI team keeps it running; the sponsor decides whether it should keep running.**

---

## The lifecycle

**1. Build & submit** — builder builds on fake data, passes the checks, submits. (Covered by the rest of the guide.)

**2. Sponsorship** — before anything campus-facing is hosted, a durable owner signs on. For a small throwaway or a personal experiment, this can be skipped — but then it isn't hosted as a campus product. The bigger the footprint, the more this matters.

**3. Hosting** — the AI team takes it to production. At this point ownership is recorded: who built it, who sponsors it, who to contact.

**4. Maintenance** — things break: models change, dependencies age, bugs surface. Platform-level issues are the AI team's; "is this still doing the right thing / is it still wanted" is the sponsor's. If active code maintenance is needed and the builder is gone, the sponsor either finds a new builder (a new fellow, a class project, the AI team) or moves toward retiring it.

**5. Review** — hosted projects get a light periodic check-in (e.g. once a term or once a year): still used? still working? still owned? still on fake or approved data? Anything that fails review goes to step 6.

**6. Retirement (sunsetting)** — projects that are unused, unowned, broken beyond worthwhile repair, or superseded get retired on purpose: taken offline, data handled appropriately, users told if needed. Retiring something cleanly is a normal, healthy outcome — not a failure. The graveyard of zombie apps is what you get when nobody's allowed to turn anything off.

---

## When the builder graduates (offboarding)

This is the case the whole guide exists for. Before a builder leaves:

- **Hand off knowledge.** The README should already let someone else run and understand the project (that's a submission gate). A short note on known issues and "things only I know" helps.
- **Confirm the sponsor.** Make sure the durable owner is still on board and knows the builder is leaving.
- **Decide the path:** keep it (sponsor + a plan for maintenance), freeze it (leave running as-is, accept it won't get updates, plan to revisit), or retire it (turn it off cleanly).
- **Don't just disappear.** An app that loses its only knowledgeable person with no handoff is a candidate for retirement, not indefinite drift.

A useful default: if no one is willing to sponsor and maintain a project once the builder leaves, that's a strong signal it should be retired rather than left running unowned.

---

## What this means for you as a builder

- If you want your project to **outlive your time at Babson**, line up a staff or faculty sponsor early — ideally before you submit. Building something great that has to be switched off because no one can own it is a waste of your work.
- Write the README and notes so the next person can pick it up. "Build to hand off" isn't just for the AI team's review — it's for whoever inherits this after you.
- Be honest about what your project is. A weekend experiment doesn't need a sponsor or a lifecycle — it needs to not be hosted as if it were a campus product. A real tool the campus will lean on needs a durable owner from day one.

---

## What this means for sponsors

Sponsoring isn't a rubber stamp. You're agreeing to be the person who, for as long as this is hosted, can answer "is this still needed and still working?" and to participate in the periodic review. You don't have to write code. You do have to be reachable and willing to make the keep/freeze/retire call when the time comes. If you're not willing to be that person, the honest move is to not sponsor — which usually means the project stays a prototype or gets retired, and that's fine.
