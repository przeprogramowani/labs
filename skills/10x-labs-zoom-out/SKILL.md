---
name: 10x-labs-zoom-out
description: >
  Turn a small, one-off task into the bigger outcome behind it, then list
  what the agent needs (data, access, environments) to own that outcome
  without a human in the loop. Use at the START of a session, before work
  begins, when the request is a single instance of a recurring problem
  ("fix this crash", "write the Q3 report", "answer this ticket"), when the
  task would normally need meetings, or when the user asks "what should we
  really be doing here". Trigger phrases: "zoom out", "bigger goal", "goal behind the
  goal", "think bigger", "be more ambitious", "keep it fixed", "co dalej",
  "większy cel", "myśl szerzej", "bądź ambitniejszy", "żeby to się nie
  powtarzało". Not for creative/taste work or purely mechanical edits.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Write
  - AskUserQuestion
---

# Zoom out: aim at the outcome, not the task

People hand agents the task in front of them: fix this crash, write this report. The agent does it well, and next week the same kind of task comes back. The real value sits one or two levels higher — the outcome the task serves, and the system that would keep that outcome true without anyone asking.

This skill runs at the start of a session. It does not do the work. It produces a short brief that says which level the session should work at, and what is missing for the agent to work there on its own.

Source idea: Felix Rieseberg moved from "fix this crash" to *"keep our apps crash-free"* — but that only worked after he gave the agent crash reports, a map from app version to git commit, and Windows/macOS/Linux machines to reproduce and verify fixes. The goal and the access go together; a big goal without access is just a wish.

## Step 1 — Climb three levels

Write the request at three levels. Ask "what do you want to happen next?" and "what is the goal behind the goal?" to move up.

| Level | Question | Example (crash) | Example (quarterly review) |
|---|---|---|---|
| 1. Task | What was asked? | Fix crash #4512 | Make the Q3 review deck |
| 2. Outcome | Why does the task exist? What changes when it is done? | Users stop hitting crashes in this area | Leadership catches problems early and moves money/people to the right places |
| 3. Standing system | What would keep the outcome true without anyone asking again? | Agent watches crash reports, reproduces, fixes, verifies, opens PRs — "keep the app crash-free" | A better early-warning process, tested against past quarters, run next to the current review for one quarter |

Make level 2 and 3 concrete for THIS request. Generic phrases ("improve quality", "add monitoring") are a sign you have not understood the domain yet — read the code, data, or docs available before writing them.

## Step 2 — List what the agent needs to work at level 3

A standing goal only works when the agent can see the problem and check its own fixes. For each item, say whether it exists today, where, and what it would take to add:

- **Signals** — where does the agent learn that something is wrong? (logs, crash reports, tickets, metrics, raw data exports)
- **Mapping** — can it connect a signal to the code or data that caused it? (version → commit, metric → query, ticket → module)
- **Reproduction** — where can it try things safely? (test env, OS machines, sandbox DB, fixtures)
- **Verification** — what, other than the agent's own opinion, shows the fix worked? (test, benchmark, re-run of the failing case, held-out data)
- **Permission** — what may it do alone, and what needs a human? (open PR vs merge, draft vs send)

Check the repo/workspace before claiming something is missing. Mark each item `have` / `partial` / `missing`.

## Step 3 — Pick the level for this session

Recommend one level, with a one-line reason. Usually:

- All level-3 needs are `have` → work at level 3 now.
- Some are `missing` but cheap → this session does level 1 AND adds the cheapest missing piece, so the next session can go higher.
- Most are `missing` or need other people → work at level 2, and list the setup steps as follow-ups for the user.

Do not silently expand scope. If the recommended level is bigger than what the user asked, say so and ask before starting (one short question is enough).

## Output

Write `zoom-out.md` (or print it if there is no workspace) with exactly these sections:

```markdown
# Zoom out — <short task name>

## Three levels
1. Task: ...
2. Outcome: ...
3. Standing system: ...

## What the agent needs for level 3
| Need | Status | Where / how to add |
|---|---|---|

## Recommended level for this session
<level + reason + what gets done this session>

## Standing mandate (copy-paste prompt)
<one paragraph the user can give an agent to run level 3 once the needs are in place>

## Setup steps for the user
- ...
```

Then continue with the work at the chosen level, unless the user asked only for the brief.

## When to skip

- Creative or taste work (writing voice, visual design) — a bigger goal there is usually a different project.
- Purely mechanical changes (rename, bump a version).
- The user already stated the outcome and the standing system themselves.
