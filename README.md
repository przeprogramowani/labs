# labs

Experimental agent skills from [Przeprogramowani](https://przeprogramowani.pl).

## Skills

| Skill | Purpose |
|---|---|
| [`10x-labs-zoom-out`](skills/10x-labs-zoom-out/SKILL.md) | Turns a one-off task ("fix this crash") into the outcome behind it and the standing system that keeps it true, then lists the signals, access and checks the agent needs to own that outcome. |
| [`10x-labs-expert-prompt`](skills/10x-labs-expert-prompt/SKILL.md) | Rewrites a task prompt the way a top expert would approach it: deeper, not bigger. Names the raw data, real methods and an outside check that proves the result. |
| [`10x-labs-expert-team`](skills/10x-labs-expert-team/SKILL.md) | Assembles a small cross-field team, including red-team critics, and has each role do the real work of its job on the task instead of acting out a persona. |
| [`10x-labs-push-further`](skills/10x-labs-push-further/SKILL.md) | Sets a hard, measurable target (often 10× past baseline) with guardrails and a complexity budget, and keeps iterating when progress stalls instead of stopping at "good enough". |
| [`10x-labs-memory-atlas`](skills/10x-labs-memory-atlas/SKILL.md) | Works out how your coding agent stores memory, then reads every memory across the projects where it has sessions and writes a per-project atlas (Markdown plus an offline HTML preview) that flags stale, duplicate and conflicting entries. |

The four ambition skills (`zoom-out`, `expert-prompt`, `expert-team`, `push-further`) are based on Felix Rieseberg's article [How to be more ambitious: a tutorial for humans with models](https://felixrieseberg.com/how-to-be-more-ambitious-a-tutorial-for-humans-with-models/).

## Install

Install with the [skills.sh](https://skills.sh) CLI:

```bash
# all skills
npx skills add przeprogramowani/labs

# a single skill
npx skills add przeprogramowani/labs --skill 10x-labs-zoom-out

# list available skills without installing
npx skills add przeprogramowani/labs --list
```

Add `-g` to install globally (user-level) instead of into the current project.
