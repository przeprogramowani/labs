---
name: 10x-labs-expert-prompt
description: >
  Rewrite a task prompt the way a top expert in the field would approach
  it: deeper, not bigger. Finds what the expert would do that was not
  asked, the raw source material they would start from, the exact methods
  and tools they would use, and what outside check would prove the result.
  Use at the START of a session, before running a non-trivial analysis,
  research, debugging, performance, data or planning task — especially when
  the prompt asks for a summary, a markdown table, or an opinion where an
  expert would build a model, a query or a measurement. Trigger phrases:
  "expert prompt", "go deeper", "improve my prompt", "how would an expert
  do this", "rewrite this prompt", "pogłęb", "jak zrobiłby to ekspert",
  "popraw mój prompt", "lepszy prompt". Not for one-line mechanical edits.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Write
  - AskUserQuestion
---

# Expert prompt: deeper, not bigger

Most prompts ask for what the user already knows how to picture: a summary, a table, a list of ideas. A top professional would start somewhere else — from raw data instead of a digest, with a real method instead of an opinion, and with a check that does not depend on their own judgment. Models can do that work, but only when the prompt asks for it.

This skill rewrites the user's prompt before work starts. It makes the task deeper (better methods, better sources, real verification), not bigger (more sections, more scope).

Source: Felix Rieseberg's template — *"I want a deeper version of this task, not a bigger one."*

## Step 1 — Understand the real context

Before answering the questions below, look at what is actually available: files in the repo, data exports, logs, schemas, existing scripts. An expert rewrite that names a data source that does not exist is worse than the original prompt. If something important is unknown, note it as an assumption.

## Step 2 — Answer the four expert questions

Answer each one specifically for this task and this domain. Name things: the table, the log, the tool, the statistical method, the profiler.

1. **What would a top professional do here that the user is not asking for?** Think of the person who is best in the world at this exact job. What is their first move? What would they refuse to skip?
2. **What is the rawest material they would start from?** Not the dashboard, the summary, or someone's slide — the event log, the raw export, the trace, the source code, the original documents.
3. **Which specific methods and tools would they use?** Real instruments of the field: a SQL query over raw events, a cohort analysis, a regression, a flame graph, `git bisect`, a load test, a simulation. Prefer things that produce numbers or reproducible artifacts over prose and markdown tables.
4. **What, other than the model or the user, would show the result is right?** An outside judge: a test that passes, a benchmark number, a prediction checked on held-out data, a reproduction of a known past event. If the agent can run this check itself, even better — say how.

Also list **assumptions to test**: beliefs hidden in the original prompt ("churn is an onboarding problem", "the slowness is the database") that the work should check against raw data instead of accepting.

## Step 3 — Rewrite the prompt

Write a new prompt that asks for exactly what the expert would do. It should:

- keep the user's actual goal and constraints (time, scope, audience),
- name the raw sources, methods and tools from Step 2,
- state the outside check and require it before calling the work done,
- turn hidden assumptions into explicit checks,
- stay roughly the same scope — deeper, not longer. If the rewrite doubles the work, cut the least valuable part and say what you cut.

## Output

```markdown
# Expert prompt — <short task name>

## Original prompt
> ...

## Four expert questions
1. Expert move not asked for: ...
2. Rawest material: ...
3. Methods and tools: ...
4. Outside check: ...

## Assumptions to test
- ...

## Rewritten prompt
<the full new prompt, ready to paste or to execute>

## What changed and why (3 bullets max)
```

Save it as `expert-prompt.md` when there is a workspace. Then ask the user (one short question) whether to run the rewritten prompt, unless they already said to go ahead — in that case, start working from the rewritten prompt.

## Example (shortened)

Original: *"Summarize why our customers churned last quarter."*

- Expert move: separate voluntary vs involuntary churn, segment by cohort and plan before looking for causes.
- Rawest material: subscription event table and product usage events, not the churn survey summary.
- Methods: cohort retention curves, comparison of usage in the 30 days before cancel vs retained users, simple predictive model.
- Outside check: the model built on Q1–Q2 must predict Q3 churners better than a naive baseline.
- Assumption to test: "churn is an onboarding problem" — check whether churners differ in first-week activity at all.
