---
name: 10x-lab-push-further
description: >
  Set a very hard, specific, measurable target for the work and keep going
  when progress stalls, instead of stopping at "good enough". Use at the
  START of optimization or improvement work (performance, cost, accuracy,
  coverage, conversion, bundle size, error rate) and DURING a session when
  the agent says it is done early, gives up, says "this is the best we can
  do", or reports a modest gain. Trigger phrases: "push further", "hard target",
  "keep going", "don't stop", "good enough?", "more ambitious
  target", "is that the limit", "ambitny cel", "nie poddawaj się", "idź
  dalej", "da się lepiej", "to za mało". Not for tasks with no measurable
  result.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Write
  - Edit
---

# Push further: ask for 200 ms, then keep going

Decades of goal-setting research show that specific, hard goals beat "do your best". Models behave the same way: ask for "faster" and you get a few safe tweaks and a polite stop. Ask for a number far past the obvious one, treat it as reachable, and the work changes — the agent stops polishing and starts questioning the design.

Source: Felix Rieseberg — *"You need your website to load in 5s instead of 10s? Ask for 200ms."* And when the model stalls: *"Reiterate that it's very much solvable, it just needs to keep going."*

## Part A — At the start: set the target

1. **Measure the baseline.** Find or build the command that measures the thing (benchmark, test, query, Lighthouse run, cost report). Run it and record the number. No measurement = no target; build the measurement first.
2. **Find the floor.** What is the physical or theoretical limit? (network round trip, size of the data that must be read, best published result, what the fastest competitor does). This shows how much room is really there.
3. **Set three numbers:**
   - *Expected* — what a normal effort would get (often 1.5–2× better).
   - *Hard target* — roughly an order of magnitude better than baseline, or close to the floor. This is the target you work towards.
   - *Guardrails* — what must not get worse (correctness, other metrics, cost). Include a **complexity budget**: how much harder to read and maintain the code may become. A 300× win in code nobody on the team can safely change is often worse than a 20× win in code everyone understands.
4. **Say what the hard target forces.** A 10× target usually rules out tweaks and points at a different design (cache, precompute, remove the work, change the algorithm, change the data layout). Write 2–3 such directions before touching code.

The hard target is a working target, not a promise to the user. Report honestly how far you got.

## Part B — During the session: don't stop early

When progress stalls or you are about to say "this is good enough", run this loop instead of stopping:

1. **Re-measure.** Current number vs baseline vs hard target. Where does the remaining time/cost actually go? Profile; don't guess.
2. **Name what was ruled out** and the evidence for each — so you don't try it again.
3. **Pick the next hypothesis** with the biggest possible gain, not the safest one. Prefer removing work over speeding work up, and a better algorithm or data layout over low-level tricks (bit-twiddling, manual unrolling) — the first kind of gain is usually bigger and stays readable.
4. **Try it, measure, keep or revert.**

Repeat. Treat the target as solvable: a stall means the current approach is used up, not that the problem is. Each loop should change something real; a loop that only rewords the plan does not count.

**Stop only when one of these is true** — and say which:
- the hard target is met (guardrails still hold),
- you are near the floor and can show the measurement,
- every remaining direction needs a decision or access only the user has (name it),
- the user's time/budget limit is reached,
- the next gain would break the complexity budget and the current result already removes the user's problem (e.g. the step is no longer the bottleneck — measure what is).

If you tried a fast-but-complex variant, keep the simpler one in the code unless the extra speed clearly matters, and tell the user both numbers so they can choose.

## Output

Keep a running log `push-further.md` (or print it) with:

```markdown
# Push further — <metric>

| | Value |
|---|---|
| Baseline | ... (command: `...`) |
| Floor | ... (why) |
| Expected | ... |
| Hard target | ... |
| Guardrails | ... |

## Directions the hard target forces
- ...

## Attempts
| # | Hypothesis | Result (measured) | Kept? |
|---|---|---|---|

## Ruled out (with evidence)
- ...

## Status
<current value, % of the way to hard target, stop reason if stopped, simpler/faster variants with their numbers, next best move>
```
