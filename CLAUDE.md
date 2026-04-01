# Claude Configuration — Tobi Koyejo

This repo is the version-controlled source of truth for Claude's operating instructions across all surfaces.

## Structure

- `sop/` — Development SOP and operational procedures
- `skills/` — Custom skill definitions (synced to `.claude/skills/`)
- `templates/` — Reusable templates for stories, ADRs, retros
- `memory/` — Exported memory snapshots (reference only — live memory is in `.auto-memory/`)

## Quick Reference

**Source of truth:** `sop/claude-development-sop.md` (v2.0, 679 lines)

**Tool allocation:**
- Jira = issue tracking source of truth
- Notion = sprint ceremonies (planning DBs, retro DBs, velocity)
- Confluence = technical documentation (ADRs, runbooks, specs)
- GitHub = code and version control
- Monday.com = HHN operations only
- PocketBase + HTML dashboard = BD CRM

**Persistence across surfaces:**
| Surface | Mechanism | File |
|---------|-----------|------|
| Cowork | CLAUDE.md | `/mnt/.claude/CLAUDE.md` |
| Claude Code | CLAUDE.md | `~/.claude/CLAUDE.md` |
| Desktop / web | Personal Preferences | Manual paste |
| This repo | Git | `sop/claude-development-sop.md` |
