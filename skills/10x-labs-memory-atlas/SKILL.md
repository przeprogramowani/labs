---
name: 10x-labs-memory-atlas
description: >
  Find out how the coding agent you are running in stores memory (memory
  files such as MEMORY.md, instruction files, saved notes), then scan every
  project where that agent has sessions and write one atlas of all memory,
  grouped by project: a Markdown file plus a self-contained HTML page to
  preview in the browser. Agent-agnostic: the agent describes its own memory
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

This skill makes that visible. It runs in two parts: first it learns how *this* agent stores memory, then it reads all of it and writes an atlas grouped by project. It never changes a memory file.

## Step 1 — Reveal your own memory system

This skill names no paths. You are the agent, and you already know your own conventions better than any list here. Describe them, then prove them on this machine:

1. **Say what you know.** From your instructions and what you know of your own tool: where you write memories, in what format, whether there is an index file, which instruction files you load at session start (user-level and per project), and where you keep session history.
2. **Confirm on disk.** Find each location you named on this machine. Agents change their layout between versions, so trust what is on disk over what you remember, and note every difference.
3. **Check what actually loaded.** Session history usually records which memory and instruction files were injected into each session. That record beats your own description: it shows whether a file you think is ignored was in fact loaded, and whether files in subfolders loaded at start or only on demand.
4. **Look for what you missed.** Walk your config and data folders and the user's home for memory-like files your description did not cover. Stay inside your own agent's conventions; mention other agents' files only if you run into them.

Write down the **memory model** before scanning: the kinds of memory (saved facts vs. hand-written instruction files), where each kind lives (user, project, folder), how a memory location maps back to a project, and what loads at session start. Say what you could not confirm instead of guessing.

## Step 2 — Find every project with sessions

Build the project list from your own session history, not from guesses about where code lives:

- Find every project on this machine that has sessions with you, wherever it sits in the file system.
- Map each one back to a real project path, using the most reliable source your history gives (a path recorded inside the session beats one decoded from a folder name).
- For each project note: real path, whether it still exists, number of sessions, and the date of the last one.
- Projects with sessions but no memory still go in the list; that is useful to know.
- Separate the folder a session was **launched** from and the projects it **worked** in. Sessions started from a parent folder (home, a `dev/` folder) often run without the project's rules; count them and say so.
- If a project folder is gone but its history remains, check whether the history still holds the memory that was loaded, and recover a summary from there.

## Step 3 — Read every memory and summarize it per project

For each project, and once for user-level (global) memory:

- Read each memory file in full. Record its path, its type if your format has one, and last-modified date.
- Summarize each memory in one line, in the user's words where possible. Keep the **why** if the memory has one.
- Flag problems, each with a one-line reason:
  - **stale** — names a file, function, branch or date that no longer exists or has passed (check, don't guess)
  - **duplicate** — says the same thing as another memory
  - **conflict** — contradicts another memory or an instruction file
  - **orphan** — listed in an index but the file is missing, or a file the index does not list
  - **misplaced** — a project fact stored as global memory, or the other way round
- Never copy secrets. If a memory contains a token, password, key or private personal data, write `[redacted: <kind>]` and flag it.

Do not edit, merge or delete anything. Put suggested fixes in the report and let the user act on them.

## Output

Write two files into `memory-atlas/` in the current directory (or a folder the user names), and print both paths at the end.

**`memory-atlas.md`** with exactly these sections:

```markdown
# Memory atlas — <agent name>, <date>

## How this agent stores memory
<the memory model from Step 1: kinds, locations, index files, what loads at session start, what could not be confirmed>

## Overview
| Project | Path | Sessions | Last session | Memories | Flags |
|---|---|---|---|---|---|

## Global (user-level) memory
| Memory | Type | Summary | Updated | Flags |
|---|---|---|---|---|

## <project name>
Path: ... · Sessions: ... · Last session: ...
| Memory | Type | Summary | Updated | Flags |
|---|---|---|---|---|
<repeat per project; projects with no memory get one line saying so>

## Suggested clean-up
- <flagged item → suggested action>
```

**`memory-atlas.html`** with the same content as one self-contained file that opens straight from disk:

- Inline CSS and JS only. No CDN, fonts or network calls (it holds private notes and should work offline).
- A sidebar or top list of projects with memory counts; a search box that filters memories as you type; flag badges you can filter by.
- One section per project, collapsible, with the memory table; the "how memory works" section at the top.
- Readable in light and dark mode (`prefers-color-scheme`), usable at phone width.
- Escape all memory text as HTML; never insert it as raw markup.

Do not upload or publish either file unless the user asks — they describe the user's projects and habits.

## When to skip

- The agent has no persistent memory and the projects have no instruction files — say so in two lines instead of writing an empty atlas.
- The user wants to change one specific memory — just do that.
