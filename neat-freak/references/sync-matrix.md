# Change Impact Matrix

Use this table when you're unsure which files need syncing after a change. **Check both directions**: gap-filling (which files to add to) + anti-bloat (which files to remove from).

## Reverse: What to Remove from CLAUDE.md / Memory

CLAUDE.md / AGENTS.md is not a changelog. Remove or migrate whenever you spot these anti-patterns:

| Anti-Pattern | Action |
|---|---|
| Blockquotes of the form "as of X, feature Y went live, see docs/Z.md" | Delete — the pointer role is already served by the deep-docs pointer table; narrative belongs in git log / `/changelog` / `docs/CHANGES.md` |
| Copying detailed mechanisms / data flows / scoring formulas from docs/ into CLAUDE.md | Delete — AI will read docs when it touches that code; CLAUDE.md should only keep "boundary rules" |
| "New feature launched" narrative that has been stable ≥ 7 days | Absorb into project overview where appropriate; delete the rest |
| One-time incident postmortem details ("at X, service Y was down 30min because Z") | Keep a 1-line red-line rule ("never bare-run systemctl stop X"); move incident details to docs/PLAYBOOK.md or delete |
| "Intermediate state" narrative superseded by newer version ("5/6 changed X, 5/8 changed it to Y") | Keep only the final-state rule; delete intermediate history |
| Single memory entry > 100 lines + all incident postmortem | Distill into one ≤ 30-line "rule + Why + How to apply" entry; delete the rest |
| Memory entries containing "superseded by X" / "deprecated" / "kept for history" | 99% of the time these really can be deleted — docs is the authority |

Litmus test: **If the AI doesn't see this line next time it writes code, will it make a mistake?** No → delete or migrate.

## Code-Layer Changes → Doc-Layer Changes

| What Happened in This Conversation | Files to Update (by audience) |
|---|---|
| New API / route | Project root markdown route list · `docs/integration-guide.md` API cheat sheet · `docs/architecture.md` Routes section |
| New / renamed env var | Project root markdown env var table · `docs/operator-runbook.md` env var section · `docs/integration-guide.md` (if downstream needs to configure it) |
| New database table / column | Project root markdown DB table · `docs/architecture.md` Data Model |
| New / changed user flow | Project root markdown user flow · README command-line examples · `docs/handoff.md` What Exists Today |
| Major new feature (cross-file) | All of the above + `docs/architecture.md` new section + `docs/handoff.md` completed list |
| New term / renamed concept | `docs/integration-guide.md` glossary (if exists) + global search-and-replace old term |
| Deployment param / infra change | `docs/operator-runbook.md` · project root markdown deploy section |
| Downstream integration method changed | Downstream project's `docs/<integration>.md` · upstream project's `integration-guide.md` |

## Memory-Layer Changes

| Situation | Action |
|---|---|
| Stale fact | Edit the memory file; also update the index (e.g. MEMORY.md) description |
| Relative time ("today," "recently") | Convert all to absolute dates (`2026-04-29`, not "today") |
| Duplicate entries (multiple entries saying the same thing) | Merge into one; update index |
| Completed TODO | Delete — the knowledge base is not a historical archive |
| Overturned decision | Delete old entry; keep the new decision |
| One-shot temporary context from a previous session | Delete |

## Cross-Project Impact Check

Most frequently missed scenarios:

- **Upstream API changed → downstream SDK docs**: Protocol changes must be aligned on both sides
- **Shared subdomain / route / env var changed → setup docs for all consumer projects**
- **Auth middleware changed → integration guide for all connected apps**
- **Shared component / infrastructure upgraded → operator-runbooks wherever version numbers are mentioned**

How to check: Does this change involve an SDK, subdomain, shared config, or cross-process protocol? If yes, search all dependent projects for docs that mention it.

## Documentation Structure Conventions

The standard action for documenting a new capability (API, flow, feature) is to **update four places**:

1. **integration-guide / external-perspective doc**: How to use it (curl / SDK examples / error codes)
2. **architecture**: How it works (data flow, state machine, design tradeoffs)
3. **runbook**: How to operate it (smoke-test commands, troubleshooting, env vars)
4. **handoff / CHANGELOG**: What's completed

API cheat sheets, env var tables, and glossaries are high-frequency lookup structures — they **must stay current**.
