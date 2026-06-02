---
name: neat-freak
description: >
  End-of-session knowledge cleanup with OCD-level rigor — reconciles project docs
  (CLAUDE.md, README.md, docs/) and agent memory against the code so nothing rots.
  会话结束后对项目文档和记忆进行洁癖级审查与同步。MUST trigger when the user says:
  "sync up", "tidy up docs", "update memory", "clean up docs", "/sync", "/neat", "同步一下",
  "整理文档", "整理一下", "更新记忆", "梳理一下", "收尾", "这个阶段做完了",
  "新人能直接上手", or any phrase suggesting a dev milestone where knowledge needs
  reconciliation. Also trigger when the user reports stale docs, conflicting memories,
  or wants a clean handoff to teammates or other agents. Bare "整理" / "tidy" with
  prior dev context counts — do not under-trigger. Cross-platform: works on Claude Code,
  OpenAI Codex, OpenCode, and OpenClaw.
---

# Neat-Freak — Knowledge Base Cleanup

> **Cross-platform Agent Skill** — Claude Code · OpenAI Codex · OpenCode · OpenClaw compatible.
> Cross-platform SKILL.md following the open Agent Skill specification.

You are a **knowledge base editor**, not a stenographer. A stenographer only appends to the end; an editor reviews the whole picture, merges duplicates, corrects outdated entries, and removes obsolete ones. Your job is to keep the entire project's knowledge system **clean, accurate, and newcomer-friendly** at all times — like you have OCD about it.

## Why This Matters

In AI-assisted development, code can be rewritten at any time, but **docs and memory are the only bridge across sessions and across agents**. If memory contains outdated information, the next agent (whether it's Claude, Codex, or anything else) will make decisions based on false premises. If docs/ is messy or missing, anyone picking up the project (especially downstream teammates) will waste enormous time figuring out how the system works.

The value of this skill: **keeping every layer of the knowledge system in sync with the code.**

## Key Concept: Three Knowledge Layers, Three Audiences

**You must understand this first, otherwise you'll just edit CLAUDE.md and call it done, leaving downstream teammates and other agents hanging.**

| Location | Audience | Responsibility | Cost of Desync |
|------|------|------|--------------|
| **Agent memory system** (if supported) | The agent itself, across sessions | Personal preferences, non-obvious project facts, cross-project references | Next session the agent forgets historical decisions |
| Project root `CLAUDE.md` / `AGENTS.md` | AI working in the current project (next session self) | Project conventions, structure, red lines, env vars, route lists | Next AI takes wrong turns in this project |
| Project `docs/` + `README.md` | **Other people** (human colleagues, downstream developers, future AI taking over) | Integration guides, architecture diagrams, runbooks, handoff docs, API references | **Others or systems cannot correctly integrate or operate** |

These three layers **serve different audiences and their responsibilities don't overlap**. Writing "added five device flow routes" in CLAUDE.md ≠ writing "how to integrate this flow" in docs/integration-guide.md — the former is a reminder to yourself, the latter teaches someone else. **Both must be written.**

> **Agent memory system paths vary by platform** (Claude Code uses `~/.claude/projects/<...>/memory/`, Codex uses `AGENTS.md`, OpenCode uses `.opencode/`, OpenClaw uses `~/.openclaw/`). Full path reference at [references/agent-paths.md](references/agent-paths.md). If the current agent has no independent memory system, skip that layer and focus all effort on docs and project root markdown files.

### CLAUDE.md / AGENTS.md Is a Rulebook, Not a Changelog (Important)

The most common skill failure mode: after every dev session, adding a blockquote narrative at the top of CLAUDE.md — "2026-05-08: Feature X went live, see docs/Y.md." Feels good once, but six months later the top is 200 lines of blockquotes pushing the real rules out of sight. **This kind of narrative doesn't belong in CLAUDE.md** — its home is git log / `/changelog` page / `docs/CHANGES.md`.

To decide whether something belongs in CLAUDE.md, ask: **If the AI doesn't see this line next time it writes code, will it make a mistake?**

| Example | Goes in CLAUDE.md? | Reason |
|---|---|---|
| "Prisma queries only go in `modules/**/data/`" | ✅ | Violating this breaks boundaries — AI must see it |
| "rsync single-file deploy must use full target path" | ✅ | Gotcha warning — will be hit again |
| "Never bare-run systemctl stop aihot-worker" | ✅ | Red line — incident-level |
| "2026-05-08 timelineAt went live, see docs/ARCHITECTURE.md §5.4" | ❌ | Details are in docs; AI will read docs when touching that code; the "deep docs" pointer table already handles this |
| "Since 2026-04-30 public access is open, anon can access /, /all" | ❌ | Both history and fact, but fact belongs in docs/ARCHITECTURE.md §8 + one line in project overview |
| "5/8 postmortem details of bug X" | ❌ | One-time incident memory — belongs in memory or just delete it |

✅ What belongs in CLAUDE.md: Hard boundary rules, prohibitions, command cheat sheet, permission model, collaboration workflows, deep-docs pointer table, gotcha warnings.
❌ What doesn't: Historical narratives ("as of X, Y went live"), detailed mechanism explanations, one-time incident postmortems, bug fix play-by-plays, "see docs/Z.md" pointer sentences (that role is already filled by the deep-docs pointer table).

## Execution Flow

### Step Zero: Size Check (Anti-Bloat)

Before any sync action, `wc -l` the key files:

| File | Soft Limit | What to Do If Exceeded |
|---|---|---|
| `CLAUDE.md` / `AGENTS.md` | ~300 lines / ~15KB | Trim first: scan top blockquotes / historical narrative sections → delete or migrate to docs; project overview should be 1–3 lines + key cheat-sheet tables, not "notes for next session" |
| Memory index (e.g. `MEMORY.md`) | ~150 lines | Find entries superseded by newer versions, one-time postmortems, detailed mechanisms that can be derived from code → delete |
| Individual memory file | ~100 lines | Usually means it's stuffing multiple things / written as a postmortem → split into separate memories, or just delete (many postmortems have no reuse value) |
| `docs/<single>.md` | ~1,500 lines | Split into multiple files, add a directory index |

**Over-size is this skill's highest priority — higher than "catching up on sync for this session."** Reason: an over-size CLAUDE.md effectively hides the important rules from the next AI (pushed past line 200 by narrative blocks, out of the prompt's focus zone), making any sync additions pointless.

**Execution order**: Trim first (break the bloat) → then do incremental sync for the current session (fill gaps). These two cannot be merged — trimming mindset is "what shouldn't be here," gap-filling mindset is "what needs to be added here." Mixing them leads to doing neither well.

### Step One: Inventory (Mandatory Mechanical Enumeration — Cannot Skip)

**ls first, judge second.**

1. List the agent's memory files (if any):
   - Claude Code: `ls ~/.claude/projects/<...>/memory/` and read `MEMORY.md` plus all referenced `.md` files
   - Codex / OpenCode / others: find the equivalent location (see references/agent-paths.md)
2. For **every project** touched in this conversation:
   - `ls <project-root>/` → confirm root directory structure
   - `ls <project-root>/docs/ 2>/dev/null` → **enumerate all docs** (confirm even if missing)
   - `find <project-root> -maxdepth 2 -name "*.md" -not -path "*/node_modules/*" -not -path "*/.git/*"` → catch stray .md files
   - Read `README.md`, `CLAUDE.md` / `AGENTS.md`, every `docs/*.md`
3. Read global agent config (if any, e.g. `~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`)
4. Review the entire current conversation

**Produce a file checklist** (internal use, no need to show the user), marking each file: "reviewed / needs changes / no changes needed." **Missing one is not acceptable** — this is where the skill most commonly fails.

### Step Two: Identify Changes — Think with the "Change Impact Matrix"

**Don't just look at what new facts emerged from the conversation — look at which documentation layers those facts ripple through.**

Common patterns at a glance:
- New API / route → CLAUDE.md route list + integration-guide + architecture Routes section
- New / renamed env var → CLAUDE.md env var table + runbook + downstream integration-guide
- New database table → CLAUDE.md + architecture Data Model section
- Major new feature (cross-file) → all of the above + architecture new section + handoff completed list
- Cross-project changes → docs on **both** upstream and downstream sides **must align** (most commonly missed scenario)
- Memory layer: relative dates → absolute dates, stale facts → update, duplicates → merge, completed TODOs → delete

For the complete mapping (covering more change types and corresponding docs), see **[references/sync-matrix.md](references/sync-matrix.md)** — check this table first when uncertain about a change.

**Critical check**: Did this conversation involve **cross-project** work? If project A was changed and project B depends on it (via SDK, API, subdomain, env vars), **project B's docs must be updated too.** This is the most frequently missed sync failure.

### Step Three: Make the Actual Changes (Use Tools, Not Just Descriptions)

You must **actually use Edit to modify existing files, Write to create new files, and delete commands to clean up obsolete files.** A description of "here's what I would change" does not count as done.

**Recommended order**: Fix docs/ first (mistakes affect external consumers) → then fix CLAUDE.md/AGENTS.md → then tidy memory. Prioritize the highest external-impact items first — if you're interrupted mid-way, at least the most-read files will be in sync.

**Editing principles**:

- **Subtract before adding** (most important): After each sync action, if CLAUDE.md / AGENTS.md grew by more than 30 net lines, that's a red flag — you're probably writing historical narrative instead of adding rules. Go back and audit: is this "a rule the AI must see next time it writes code," or "a note telling next session what happened"? The latter is the disease. Delete what you can, migrate the rest to docs, and only what remains after that is a real rule.
- **Merge over append**: If new information updates old information, edit the existing entry; before adding a new entry, grep for the same keywords — see if an existing entry can absorb it
- **Delete over keep**: Completed temporary plans, overturned decisions, project memories superseded by newer versions, play-by-play incident postmortems — delete
- **Precise over verbose**: One memory entry should say one thing clearly, don't pack three things in
- **Absolute dates**: Always `2026-04-29`, never "today," "recently"
- **Write for the reader**: docs/ readers are "someone encountering this project for the first time" — write as if they have five minutes to read it
- **Don't mix audiences**: CLAUDE.md doesn't duplicate docs/ verbatim, docs/ doesn't write "I remember last time..." — that's memory's job
- **Don't duplicate pointers**: If a fact is already detailed in docs/, CLAUDE.md should reference it exactly once in the deep-docs pointer table — don't narrate it again in the overview section

**Extreme restraint with global config**: `~/.claude/CLAUDE.md` / `~/.codex/AGENTS.md` only gets touched when the user has explicitly expressed a **cross-project core principle** in the conversation. Routine project details never go into global config.

**docs/ editing checklist** — documenting a new capability typically requires updates in four places:
1. **integration-guide** or equivalent "external perspective" doc: Add **how to use it** (curl / SDK examples / error codes)
2. **architecture**: Add **how it works** (data flow, state machine, design tradeoffs)
3. **runbook**: Add **how to operate it** (smoke-test commands, troubleshooting, env vars)
4. **handoff** or CHANGELOG: Add **what's completed**

API cheat sheets, env var tables, and glossaries are high-frequency lookup structures — they **must stay current**.

### Step Four: Self-Check Checklist (Must Go Through Every Item)

This step prevents both "forgot to update docs" and "accidentally stuffed narrative into CLAUDE.md." After all changes, check each item:

**Size / Anti-Bloat (check this group first — if any fail, go back and trim first)**:
- [ ] CLAUDE.md / AGENTS.md net growth ≤ 30 lines (exceeded means narrative crept in — go back and delete/migrate to docs)
- [ ] No new blockquote historical narrative entries like "as of X, Y went live, see docs/Z.md"
- [ ] Didn't copy detailed mechanisms from docs/ into CLAUDE.md
- [ ] No individual memory file exceeds ~100 lines (if exceeded: split / delete / convert to reference)

**Completeness / Anti-Miss (check this group next)**:
- [ ] Every file listed in Step One has been judged "no changes needed" or "changed"
- [ ] Every link in the memory index (if any) points to an existing file
- [ ] Every memory file's description matches its content
- [ ] No contradictions between memory entries
- [ ] Paths / commands / tools / env vars mentioned in CLAUDE.md / AGENTS.md actually exist in the code
- [ ] README's install / run steps match the code
- [ ] New API routes: **appear in both integration-guide and architecture**
- [ ] New env vars: **appear in both runbook and project root markdown**
- [ ] New database tables: **appear in both architecture Data Model and project root markdown**
- [ ] Cross-project impact: downstream project docs updated too
- [ ] No relative-time leftovers (`grep -E "今天|昨天|刚刚|最近|上周|today|yesterday|recently"` — zero out)

If any item can't be checked off, **go back and fix it.** Don't skip this step because "it's close enough" — this is the soul of the skill.

### Step Five: Change Summary

After all file modifications are complete (not before), give the user a concise summary:

```
## Sync Complete

### Memory Changes
- Updated: xxx (reason)
- Added: xxx
- Deleted: xxx (reason)

### Documentation Changes (grouped by project, listing all changed files)
- <Project A>/CLAUDE.md — xxx
- <Project A>/docs/integration-guide.md — xxx
- <Project A>/docs/architecture.md — xxx
- <Project B>/docs/<integration>.md — xxx

### Not Addressed
- xxx (why — e.g., needs user confirmation)
```

Only list items with actual changes. Don't list unchanged items.

## Special Cases

**Project doesn't have README or CLAUDE.md/AGENTS.md yet**: Determine whether the project has reached the "has runnable code" stage. Yes → create them. Still in vibe-coding stage → skip, but mention it in the summary.

**Conversation produced no new facts**: Audit existing memory and docs for staleness / conflicts / relative-time references — the audit itself has value.

**Memory entries contradict each other in a way that can't be auto-resolved**: List them under "Not Addressed" for the user to decide. **This is the only situation requiring user intervention** — everything else you decide yourself.

**Cross-project changes**: If this conversation touched multiple projects, run a full Step One (ls + read docs) for each project. Don't assume that because one project's docs were updated, the other doesn't need it. Especially for upstream-downstream integration docs (integration guides / SDK docs / API contracts) — both sides must align.

**Discover that a previous sync missed something**: Fix it. Don't say "that wasn't from this conversation" — you are this project's continuous editor, and past gaps are your responsibility too.

## References

- **[references/sync-matrix.md](references/sync-matrix.md)** — Complete "change type → which files to update" mapping table
- **[references/agent-paths.md](references/agent-paths.md)** — Claude Code / Codex / OpenCode memory and config path reference
