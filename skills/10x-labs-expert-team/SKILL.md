---
name: 10x-labs-expert-team
description: >
  Build a small team of experts from different fields — including critics
  who try to break the result — and have each one do the real work of their
  job on the task, not act out a persona. First rewrites the task as verbs
  (what must actually happen) instead of nouns (what document to produce).
  Use at the START or planning stage of a session for any task with an
  audience, a decision, or a high cost of being wrong: reviews, reports,
  launch plans, architecture choices, pitches, docs, migrations. Works best
  with subagents. Trigger phrases: "expert team", "panel of experts",
  "different perspectives", "red team", "who should look at this",
  "zespół ekspertów", "różne perspektywy", "spójrz z innych stron",
  "znajdź dziury". Not for small single-file code changes.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Write
  - Agent
  - Task
  - AskUserQuestion
---

# Expert team: many fields, real work, built-in critics

A single agent tends to approach a task from one default angle — usually a generalist writing a document. Better results come from looking at the work through several fields at once: their knowledge, tools and techniques. And from people whose job is to find what is wrong.

Two rules make this work:

1. **Verbs, not nouns.** "A quarterly review" is a noun — it pulls the agent towards making a deck. The verb version is what has to actually happen: *"get busy people to believe a story about cause and effect, and to decide under uncertainty, in 30 minutes."* The verb version tells you which experts you need. (Idea from *The Art of Innovation*.)
2. **Do the job, not the voice.** Don't ask the model to *write like* a short seller. Ask it to *do what a short seller does* to research a position: find the weakest numbers, check them against the source, build the case against. A persona changes the tone; a job changes the work.

Source: Felix Rieseberg — for a business review he would bring an analyst, a journalist and an editor; a motion and audio designer if it is presented; and a red team of a short seller and a forensic accountant.

## Step 1 — Rewrite the task as verbs

Write one or two sentences: what must happen in the world for this task to succeed? Who must understand, believe, decide or be able to do what, and under what limits (time, attention, risk)?

## Step 2 — Pick the team (3–6 roles)

Choose roles from the verbs, not from habit. Aim for:

- **1–2 core roles** who produce the main work (e.g. analyst, engineer).
- **1–2 roles from a different field** that brings tools the core roles lack (e.g. journalist for story structure, SRE for failure modes, teacher for explaining to beginners, statistician for uncertainty).
- **At least 1 critic (red team)** whose job is to break the result (e.g. short seller, forensic accountant, security attacker, hostile reviewer, the user who will misuse the feature).

For each role write:

| Role | Why this task needs them (link to a verb) | What they actually DO (methods, tools, checks) | What they deliver |
|---|---|---|---|

The "what they do" column must name concrete actions — "re-derives every number in the deck from the raw export and flags any that do not match" — not adjectives like "critical" or "creative".

## Step 3 — Run the team

- **With subagents available:** start one subagent per role in parallel. Give each the task, the verbs, their exact job and deliverable, and the input materials. Critics run after the core work exists, on the actual draft.
- **Without subagents:** do each role's work yourself in turn, as separate passes with separate notes. Don't merge the passes until each is done.

## Step 4 — Combine and resolve

Merge the outputs into the final result. For each critic finding, record: fixed / accepted risk (why) / rejected (why). Unresolved critic findings go to the user, not into silence.

## Output

```markdown
# Expert team — <short task name>

## Task as verbs
...

## Team
| Role | Why needed | What they do | Deliverable |
|---|---|---|---|

## Run plan
<parallel/sequential, what each gets as input, when critics run>

## Findings and resolution (after the run)
| Critic finding | Decision | Reason |
|---|---|---|
```

Save as `expert-team.md` when there is a workspace. If the user asked only for the plan, stop after "Run plan". Otherwise run it.

## When to skip

- Small, low-risk code changes — the overhead is not worth it.
- Tasks where the user already named the reviewers and their jobs.
