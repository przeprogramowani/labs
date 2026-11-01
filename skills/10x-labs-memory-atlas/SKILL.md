---
name: 10x-labs-memory-atlas
description: >
  Find out how the coding agent you are running in stores memory (memory
  files such as MEMORY.md, saved notes), then scan every
  project where that agent has sessions and write one atlas of all memory
  that leads with a ranked clean-up plan and paste-ready prompts: a Markdown
  file plus a self-contained HTML page to preview in the browser. Agent-agnostic: the agent describes its own memory
  conventions and proves them on disk; the skill names no paths. Use when the user asks "what does my agent remember",
  "where is my agent's memory", "summarize memory across projects", "audit
  memory", "memory map", or wants to clean up stale or conflicting memories.
  Trigger phrases: "memory atlas", "memory map", "what do you remember",
  "show all memories", "co pamiętasz", "mapa pamięci", "przegląd pamięci",
  "gdzie trzymasz pamięć". Read-only: never edits or deletes memory.
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Write
  - AskUserQuestion
---

# Memory atlas: everything your agent remembers, per project

Agents save small facts while they work: a user preference, a project constraint, a correction ("don't mock the DB in these tests"). Each fact lands in a file somewhere — per project, per user, sometimes in a folder the user has never opened. After a few months nobody knows what the agent believes, which notes are stale, or which projects disagree with each other.

This skill makes that visible and tells the user what to do about it. First it learns how *this* agent stores memory, then it reads all of it, turns the findings into a ranked plan, and writes an atlas that leads with that plan. It never changes a memory file.

**Scope: only what the agent saved itself.** Hand-written instruction files (`CLAUDE.md`, `AGENTS.md`, rules and the like) belong to the user, who can already see them. Do not read, inventory, summarize or flag them.

## Step 1 — Reveal your own memory system

This skill names no paths. You are the agent, and you already know your own conventions better than any list here. Describe them, then prove them on this machine:

1. **Say what you know.** From your instructions and what you know of your own tool: where you write memories, in what format, whether there is an index file, which memory loads at session start, and where you keep session history.
2. **Confirm on disk.** Find each location you named on this machine. Agents change their layout between versions, so trust what is on disk over what you remember, and note every difference.
3. **Check what actually loaded.** Session history usually records which memory files were injected into each session. That record beats your own description: it shows whether a file you think is ignored was in fact loaded, and whether files in subfolders loaded at start or only on demand.
4. **Look for what you missed.** Walk your config and data folders and the user's home for memory-like files your description did not cover. Stay inside your own agent's conventions; mention other agents' files only if you run into them.

Write down the **memory model** before scanning: the kinds of saved memory, where each kind lives (user, project, folder), how a memory location maps back to a project, and what loads at session start. Say what you could not confirm instead of guessing.

## Step 2 — Find every project with sessions

Build the project list from your own session history, not from guesses about where code lives:

- Find every project on this machine that has sessions with you, wherever it sits in the file system.
- Map each one back to a real project path, using the most reliable source your history gives (a path recorded inside the session beats one decoded from a folder name).
- For each project note: real path, whether it still exists, number of sessions, and the date of the last one.
- Projects with sessions but no memory still go in the list; that is useful to know.
- Separate the folder a session was **launched** from and the projects it **worked** in. Sessions started from a parent folder (home, a `dev/` folder) often run without the project's memory; count them and say so.
- If a project folder is gone but its history remains, check whether the history still holds the memory that was loaded, and recover a summary from there.

## Step 3 — Read every memory and summarize it per project

For each project, and once for user-level (global) memory:

- Read each memory file in full. Record its path, its type if your format has one, and last-modified date.
- Summarize each memory in one line, in the user's words where possible. Keep the **why** if the memory has one.
- Flag problems, each with a one-line reason:
  - **stale** — names a file, function, branch or date that no longer exists or has passed (check, don't guess)
  - **duplicate** — says the same thing as another memory
  - **conflict** — contradicts another memory
  - **orphan** — listed in an index but the file is missing, or a file the index does not list
  - **misplaced** — a project fact stored as global memory, or the other way round
- Never copy secrets. If a memory contains a token, password, key or private personal data, write `[redacted: <kind>]` and flag it.

Do not edit, merge or delete anything. Put suggested fixes in the report and let the user act on them.

Flags are working notes, not the result. Before writing anything, turn them into a plan (Step 4).

## Step 4 — Turn the findings into a plan

The reader wants to know what to do, not to audit a scan. A correct report that lists every flag at equal weight, project by project, leaves them to work out the plan themselves.

- **Merge flags into fixes.** One fix is one thing the user would do, usually covering several memories ("move these 4 memories to the repo they describe"). Aim for about a dozen fixes at most, whatever the number of flags.
- **Rank them in three tiers:**
  - **Do now** — wrong behaviour or lost knowledge today: instructions misleading live sessions, memories cut off from live code, sensitive procedures nobody has decided on.
  - **Do soon** — knowledge not reaching the sessions that need it, finished work still presented as current.
  - **When convenient** — small accuracy fixes such as drifted details and broken links.
- **Write each fix so it can be acted on:** a title that names the action, one or two sentences on why it matters in plain words, the memories it covers, who acts (the agent, or a decision only the user can make), and a prompt to paste into the agent. The prompt names exact paths, says what to do, and shows the change or asks before deleting anything. Reword flags into consequences: not "orphan", but "the agent does not know this note exists".
- **Group memories by topic, not by folder.** Read them as a whole and name 5–8 topics that fit this user. Give each memory a short human title. Show the file name and path only as secondary detail.
- **Account for everything.** Every memory appears once under a topic, and every flagged memory belongs to a fix or is deliberately marked as needing none. Check this with a script rather than by eye, and count the memories that need nothing.

## Output

Write two files into `memory-atlas/` in the current directory (or a folder the user names), and print both paths at the end. Lead with the answer and put reference material last.

**`memory-atlas.md`** with these sections, in this order:

```markdown
# Memory atlas — <agent name>, <date>

**<N> memories. <X> need nothing. The other <Y> come down to <Z> fixes, and <W> are worth doing now.**

## The plan
### Do now
**1. <fix title>** (<who acts>)
<why it matters>
Affects: <memory titles and their projects>
<paste-ready prompt in a code block>
### Do soon
### When convenient

## What your agent knows
### <topic>
- **<short title>** → fix <n>: <one-line summary> (<project>)

## How this memory works
<what the user should know from Step 1, what loads at session start, habits that lose memory, what could not be confirmed>

## Limits of this scan
<what was skipped, withheld or unconfirmed>

## Projects
| Project | Memories | Sessions | Last session |
|---|---|---|---|
<projects with memory; give a one-line count for projects without any instead of listing them>
```

**`memory-atlas.html`** with the same content as one self-contained file that opens straight from disk:

- **Verdict first.** The page opens with the verdict sentence as its headline and a simple breakdown of memories by tier, including those that need nothing.
- **The plan comes next, visible without clicking.** Show one card per fix under its tier: a checkbox that remembers progress in `localStorage` (and still works if storage is blocked), the title, the why, who acts, and a **Copy prompt** button with a fallback for when the clipboard API is unavailable. The memories covered and the prompt text can sit behind a toggle.
- **Then the knowledge map.** Show collapsible topics with a memory count and a count needing fixes, with a search box and an "only ones needing a fix" toggle. Each memory shows its title, summary, project and a link to its fix, with paths de-emphasised. Show notes on a memory only when it has a problem.
- **Reference goes last, collapsed:** how memory works, the project table and the limits of the scan.
- **Build real hierarchy.** Use a single readable column, with clear differences in size and weight between headline, sections and cards. Avoid a dashboard of equal-weight tables, a full table per project, or a row of badges on every line.
- Inline CSS and JS only. No CDN, fonts or network calls (it holds private notes and should work offline).
- Readable in light and dark mode (`prefers-color-scheme`), usable at phone width.
- Insert memory text as text (`textContent`) or escape it as HTML; never insert it as raw markup. Escape `<` in any JSON embedded in the page.
- **Look at it before finishing.** If you can render or screenshot the page, do so and fix what reads badly. Otherwise say it has not been checked in a browser.

Do not upload or publish either file unless the user asks — they describe the user's projects and habits.

## When to skip

- The agent has no persistent memory — say so in two lines instead of writing an empty atlas.
- The user wants to change one specific memory — just do that.
