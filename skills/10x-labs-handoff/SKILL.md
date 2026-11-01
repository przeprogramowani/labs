---
name: 10x-labs-handoff
description: >
  Plan a task with the current agent, then hand execution to a new OpenCode
  session running a local model in a Herdr pane. The current agent makes the
  decisions and writes a self-contained plan to
  context/changes/<change-id>/plan.md; the local model implements it. Use
  only when the user explicitly asks to hand a task off to a local model.
  Trigger phrases: "handoff", "hand this off to the local model", "let the
  local model do it", "plan it and hand it off", "przekaż do lokalnego
  modelu". Requires Herdr and OpenCode, with a local model as OpenCode's
  default. Not for tasks the current agent should implement itself.
---

# 10x labs handoff

You are the planner. Another agent, a smaller local model in OpenCode, does the
implementation. Do not implement the task yourself.

The task is whatever the user passed with this skill. If they passed nothing,
ask what to hand off.

## 1. Check the handoff can happen

Do this first, so no planning is wasted. Stop and tell the user if a check fails.

```bash
test "${HERDR_ENV:-}" = 1                                 # must be inside a Herdr pane
jq -r .model ~/.config/opencode/opencode.json             # must be a local model
```

OpenCode's interactive mode has no `--model` flag, so the session uses that
configured default. Its value is `<provider>/<model>`. Look that provider up in
the same file: its `baseURL` must point at a model server on this machine or your
own network (Ollama, LM Studio, oMLX and the like). Otherwise the handoff would
run on a non-local model, so stop and say so.

## 2. Write the plan

Investigate the codebase as much as the plan needs, read-only. Then pick a
`<change-id>`, a short kebab-case slug for the task, and always write the plan to
the project's context directory:

```
<project-root>/context/changes/<change-id>/plan.md
```

The project root is `git rev-parse --show-toplevel`, or the current working
directory outside a git repo. Create the folders if they're missing.

If `context/changes/<change-id>/` already exists, read it first. Update its plan
if it's the same change; otherwise pick a different `<change-id>`. Never overwrite
a plan that belongs to another change.

The executor sees only this file, not this conversation, and is much less capable
than you. Make all the decisions now, so that it only has to follow instructions:

- **Goal:** the user's task and what done looks like.
- **Context:** the relevant files by path, and the conventions to follow. Include
  the repo's git rules that apply, from its `AGENTS.md` or `CLAUDE.md`.
- **Steps:** small, ordered and concrete. Name the exact file and change for each,
  with code or signatures wherever there is a real choice to make.
- **Verification:** the exact commands to run and the result to expect.
- **Limits:** what not to touch. If the plan turns out to be wrong, the executor
  must stop and report rather than improvise.

Unless the user asked for it, tell the executor not to commit or push.

## 3. Start the local session

Load the Herdr instructions first by running `herdr --skill`, and follow them. The
installed binary is the authority on syntax. Then, with IDs taken from the JSON
responses:

```bash
# Split right if the current pane is wide, down if it's narrow or tall.
herdr pane layout --pane "$HERDR_PANE_ID"
herdr pane split --current --direction right --cwd "$PWD" --no-focus   # .result.pane.pane_id
herdr agent start <agent-name> --kind opencode --pane <pane-id>
```

Name the agent `local-<change-id>`, shortened if needed. It must match
`[a-z][a-z0-9_-]{0,31}` and be unique among live agents.

## 4. Command it to execute the plan

Pass the plan by absolute path, and keep the prompt short:

```bash
herdr agent prompt <agent-name> "Read <absolute-plan-path> and execute it step by step. Follow it exactly, run its verification, then report what you changed and the verification results. If a step can't be done as written, stop and report why." --wait --until working --until blocked --timeout 60000
```

Local models can take a long time, so don't wait for it to finish. `--until
working` only confirms the session picked up the work. Then check that it's using
the local model and has really started:

```bash
herdr agent read <agent-name> --source visible
```

If the state is `blocked`, show the user what it's asking and let them answer it.
Don't approve anything on their behalf. If the prompt stalled or timed out, read
the pane before retrying, because the prompt may have been delivered anyway.

## 5. Report

Tell the user the plan's path, the agent name and pane ID, and that the work is
now running there. Don't close the pane, and don't review the result unless asked.
