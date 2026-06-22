# Responsible AI for Babson Builders

A short guide to the things that matter more because this is a university and you may be building for real students, faculty, and staff. You don't need to become an ethicist or a lawyer. You need to know where the lines are, stay on the safe side of them while you build, and **flag** anything close to a line so the AI team and the institution can weigh in.

Most of this is **awareness-tier**: you acknowledge it and surface it; Babson and the AI team own enforcement and the hard calls. A few items are hard lines you don't cross on your own.

---

## 1. Student data and FERPA (the one with teeth)

**FERPA** is the federal law protecting student education records — grades, enrollment, schedules, advising and disciplinary notes, financial-aid info, anything tied to an identifiable student. It is not yours to connect to a project on your own judgment.

Synthetic-first is the rule, and the trap is realism: "my demo would be so much better with real data" is the exact thought that gets people in trouble. Build on fake data that *looks* real (see [using-fake-data.md](./using-fake-data.md)). Real student data isn't forbidden — but it's a deliberate, sponsored decision (a durable owner plus AI-team sign-off), never a shortcut you take on your own to make a demo pop.

Hard lines:

- Don't pull real student records into a project to "make it realistic."
- Don't treat data as "fake" if it's actually a real export with names changed — real patterns and identifiable details can survive that.
- If you're unsure whether something counts as protected student data, assume it does and ask.

This applies equally to other sensitive categories — employee/HR data, health information, financial data. Same rule: fake it, or get it sponsored and signed off.

---

## 2. Tell people they're using AI

A campus-facing tool should be honest that it's AI. Don't let it imply it's a human, or pose as an official Babson source of truth when it's a student project.

- Say it's an AI assistant where users will see it.
- If it can be wrong (it can), make that visible rather than presenting output as authoritative fact.
- Don't impersonate a real person, office, or official channel.

---

## 3. Academic integrity

If your tool touches anything coursework-adjacent, think about the line between **helping someone learn** and **doing their work for them**. A study aid that explains a concept is great; a tool that writes the essay or solves the problem set and hands it back may put its own users at risk of a violation.

You don't have to police this perfectly. You do have to:

- Notice when your tool sits near that line.
- Flag it on submission so the AI team and faculty can look at it before it's hosted for others.

---

## 4. Bias and fairness (awareness)

Models can produce skewed, stereotyped, or unfair output. This matters most when a project **sorts, scores, ranks, screens, or evaluates people** — applicants, students, candidates, anyone.

If your project does anything like that:

- Flag it. Don't ship it to campus on your own.
- Expect the AI team and the institution to want a closer look before it goes live.

If your project doesn't make judgments about people, this is mostly a non-issue — note it and move on.

---

## 5. Accessibility (awareness)

If your project is campus-facing, it should be usable by people with disabilities — readable, navigable, not dependent on a single sense. You don't have to deliver a full accessibility audit; build sensibly and flag it as something to confirm before broad rollout.

---

## What this means in practice

For most projects, "responsible AI" comes down to three habits:

1. **Build on fake data.** It sidesteps most of the risk above before it starts.
2. **Be honest that it's AI.** One line of disclosure where users see it.
3. **Flag the sharp edges.** If your project touches student data, makes calls about people, sits near academic integrity, or is broadly campus-facing — write that down when you submit. Surfacing it is your job; resolving it is the AI team's and the institution's.

None of this is here to slow you down. It's here so that when your project reaches real people, it doesn't create a problem that lands on you, a sponsor, or the program.

---

> **Note:** This guide is a builder's orientation, not Babson's official AI or data-governance policy. Where this and any official Babson policy differ, the official policy governs. When in doubt, ask the AI team.
