# Agent Memory & Config Path Reference

Different agent platforms store memory and project config in different locations. Use this table during Step One inventory to find the right paths for your platform.

## Claude Code

| Purpose | Path |
|---|---|
| Cross-session memory (global) | `~/.claude/projects/<encoded-project-path>/memory/` |
| Memory index file | `~/.claude/projects/<...>/memory/MEMORY.md` |
| Global instructions | `~/.claude/CLAUDE.md` |
| Project-level instructions | Project root `CLAUDE.md` (can be nested hierarchically) |
| Skills directory | `~/.claude/skills/<name>/SKILL.md` |

Memory files use YAML frontmatter: `name`, `description`, `type` (user / feedback / project / reference).

## OpenAI Codex

| Purpose | Path |
|---|---|
| Cross-session instructions (global) | `~/.codex/AGENTS.md` or `$CODEX_HOME/AGENTS.md` |
| Project-level instructions | Project root `AGENTS.md` (can be nested hierarchically) |
| Project-level override | `AGENTS.override.md` (if present, overrides same-directory AGENTS.md) |
| Skills directory | `~/.codex/skills/<name>/SKILL.md` or in-project `.codex/skills/<name>/` |

Codex has no independent "memory file + index" mechanism — all cross-session information goes directly into `AGENTS.md`. During sync, put "project facts" content in AGENTS.md.

If you find `TEAM_GUIDE.md` or `.agents.md` in the project, read those too — they are Codex fallback filenames.

## OpenClaw

| Purpose | Path |
|---|---|
| User-level skills | `~/.openclaw/skills/<name>/SKILL.md` (auto-created on first run) |
| Project-level skills | `.openclaw/skills/<name>/SKILL.md` (under repo root) |
| Workspace skills | Current workspace's `skills/` directory |

**Loading priority**: workspace > project-agent > personal-agent > managed/local > bundled > extra dirs. Same-named skills at higher priority override lower priority.

OpenClaw has no independent "memory file + index" mechanism. Cross-session information can go in project root markdown files (CLAUDE.md / AGENTS.md / equivalent), following the Codex approach. Frontmatter supports `metadata.openclaw` fields for load-time gating (by OS, env vars, binary dependencies), but this is not required for neat-freak.

## OpenCode

| Purpose | Path |
|---|---|
| Global config | `~/.config/opencode/` |
| Project config | `.opencode/` |
| Skills directory (project) | `.opencode/skills/`, `.claude/skills/`, `.codex/skills/` are all scanned |
| Skills directory (global) | `~/.config/opencode/skills/`, `~/.claude/skills/`, `~/.codex/skills/` |

OpenCode reads both Claude Code and Codex directories, so a skill installed under `~/.claude/skills/` is recognized by all three. OpenClaw uses its own `~/.openclaw/skills/` and needs a separate install (or a symlink).

## If the Current Agent Has No Independent Memory System

Skip the "memory" layer and focus all effort on:
- Project root markdown (CLAUDE.md / AGENTS.md / platform equivalent)
- README.md
- docs/

This is still an effective sync — memory is a bonus; docs are the project's knowledge floor.

## Cross-Platform Coexistence Strategy

If a project is used by both Claude Code users and Codex users, the recommended approach is:

- **Keep both `CLAUDE.md` and `AGENTS.md` in the project root**, with content either symlinked or maintained in both
- Or maintain one primary content file + have the other contain a single line: `See CLAUDE.md`
- docs/ and README are platform-neutral and don't need to be duplicated
