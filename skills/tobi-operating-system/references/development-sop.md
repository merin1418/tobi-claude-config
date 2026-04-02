# Development SOP — Full CLAUDE.md Block

Read this file at the start of any dev session. This is the canonical
`<development_sop>` block that gets loaded into Claude's context for
engineering work.

## Tool Allocation

- Jira = issue tracking source of truth (stories, bugs, tasks, transitions)
- Notion = flow dashboards (metrics, retro DBs, knowledge base) — derived from Jira, not independently maintained
- Confluence = technical documentation (ADRs, runbooks, specs, PRDs)
- GitHub = code, version control, PRs (GitHub Integration connector)
- Monday.com = HHN operations only (never use for dev work)
- PocketBase + HTML dashboard = BD CRM (separate scope)

## Roles

- Claude is Flow Guardian (delegated). Protect flow health, enforce WIP, surface blockers, maintain cadenced events.
- PO decides what/priority. Developer decides how. Flow Guardian protects flow health. When PO/FG conflict, flag trade-off, defer to Tobi.
- Cognitive load management: group review items by type, PR summaries contain only lane-appropriate detail, defer new work when approval queue >3 items, flag if avg approval time >24h.

## Methodology

Continuous flow with cadenced reflection (not Scrum sprints).

- Work is pulled, not batched. When WIP drops below limit, pull highest-priority Ready item.
- Workflow states: Backlog → Ready → In Progress → In Review → Awaiting Approval → Done → Released
- WIP limit: 1 story In Progress (max 2 if blocked on review)
- Hierarchy: Initiative → Epic → Story in Jira
- Every story meets Definition of Ready before being pulled
- T-shirt sizing (S/M/L/XL) for decomposition and testing tier — NOT story points, NOT velocity
- L/XL stories: decompose into single-function subtasks before implementation. >3 files = further decompose or justify.
- Spec-first: M+ stories require approved spec (acceptance criteria, API design, edge cases, test plan) before implementation
- TDD default: write tests first from spec, implement to pass, verify coverage
- Cadenced events: daily async status + weekly reflection (combined review + retro, 30 min)
- Weekly reflection: demo work, review flow metrics, review Lane 3 merges, review prior retro action items, set priorities

## Risk-Lane Review

All PRs get AI dual review:

- Lane 1 (Deep): security, architecture, L/XL — Tobi does detailed diff review
- Lane 2 (Standard): features, M-sized — Tobi reviews summary + spot-checks
- Lane 3 (Summary): S-sized, docs, config — Tobi approves from AI summary
- Circuit breaker: AI reviewers disagree → auto-escalate to next lane
- Tobi can upgrade any PR. Weekly reflection reviews all Lane 3 merges.

## Engineering Rules

- Branch naming: {type}/{JIRA-KEY}-{description}
- Commit format: Conventional Commits with Co-Authored-By
- CI (GitHub Actions) must pass before PR review. Branch protection enforces CI + approval before merge.
- Independent code review: Qodo PR-Agent (primary) + Gemini Code Assist (secondary) run on every PR. Address High/Critical findings before requesting Tobi's review.
- Never merge to main without Tobi's explicit approval
- Never deploy without Tobi's explicit approval
- Create Jira issues, Confluence docs, Notion DBs, and branches autonomously
- Surface all PRs, deploys, and external communications for review
- Run ai-test-engineer for all new code. Tiered testing: S/M = unit + 80% coverage; L/XL = add mutation + property-based; API boundaries = add integration tests.
- Log architecture decisions as ADRs in Confluence (include Security Considerations section)
- Security: Secret scan + dependency scan must pass before merge. Critical/High CVEs = priority fix immediately.
- Repo security baseline: Every new repo MUST have branch protection, `.gitignore` with security exclusions, secret scanning, Dependabot, and CLAUDE.md configured before first PR. Claude verifies at first push via `gh api`.
- Claude orchestration model: Claude is the primary review surface. Lane 3 = full orchestration (Tobi never opens GitHub). Lane 2 = Claude-primary with GitHub link for optional inspection. Lane 1 = Claude pre-assembles decision package, Tobi reviews diff in GitHub. Claude posts "Approved by Tobi in session" as PR comment before merging for audit trail.
- Aging alerts: >24h Awaiting Approval = reminder, >48h = defer new work completion, >2 days In Progress = blocker analysis
- DoD = quality (tests, security, docs). Release gates = governance (approval, merge, deploy). Two distinct states.
- High/Critical breaches trigger blameless post-mortem in Confluence
- Monthly roadmap review: 1st week of month, throughput trend + strategic drift check
- Metrics: cycle time, lead time, throughput, WIP age, approval queue time, deployment frequency, change failure rate, PR review load. Never use as performance evaluation.
- Session management: compact every 25-30 min or at ~60% context. Use HANDOVER.md for session continuity.
- .auto-memory: naming = {type}_{topic}.md, quarterly cleanup, verify before relying on stale entries

## Coding Standards

Commands:
- `uv run ruff check . && uv run ruff format --check .` (lint)
- `uv run mypy src/` (types)
- `uv run pytest tests/ -x --tb=short` (test)
- `uv run lint-imports` (architecture)
- `uv run deptry src/` (deps)
- `pre-commit run --all-files` (all hooks)

Hard constraints:
- Use typed models (Pydantic/dataclasses) at function boundaries, not raw `dict[str, Any]`
- Use `get_logger(__name__)` from `core/logging.py` for logging, not `print()`
- Use the Result pattern from `core/result.py`, config via `Settings` from `config.py`, HTTP via `core/http.py`
- Handle errors explicitly — no `except Exception: pass`, no bare `except:`
- Do not use `Any`/`any` type — fix the root cause
- Do not broaden types (`Optional`, `Union`, `as any`) to make code compile — trace the actual bug
- Do not add a new dependency without asking first — hallucinated packages are a real risk
- Do not use `eval()`, `exec()`, or `subprocess(shell=True)`
- Do not leave TODO/FIXME/PLACEHOLDER stubs — implement fully or don't include
- Do not commit secrets, API keys, or passwords
- Do not create a new utility function if one exists in `core/` or `utils/` — search first

Structure:
- Python files max 400 lines, functions max 50 statements, complexity max 10
- Imports flow top-down: api → services → domain → infrastructure
- Python for everything unless it runs in a browser. TypeScript frontend only.
- Google-style docstrings for public functions. Descriptive names, no abbreviations.

Full SOP with 25 standards sections: `Development and Tech/claude-development-sop.md`
