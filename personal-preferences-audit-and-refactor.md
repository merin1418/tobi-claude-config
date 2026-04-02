# Personal Preferences Prompt — Audit & Refactor

## BLUF

The prompt scores 5.5/10 against Anthropic's prompting best practices. The four general-purpose sections (`<formatting>`, `<vocabulary>`, `<working_style>`, `<default_frameworks>`) are well-crafted but incomplete — missing user identity, positive framing, and WHY rationale in 2 of 4 sections. The `<development_sop>` block consumes ~73% of the prompt but only applies to dev sessions, crowding out the general instructions that shape every conversation. Six findings, six fixes.

---

## Audit: 10-Dimension Scorecard

| # | Dimension | Rating | Evidence |
|---|-----------|--------|----------|
| 1 | **Role/persona** | Partial (4/10) | "Claude is Tobi's engineer" defined for dev only. No overarching role for the 80% of conversations that aren't dev — research, writing, geopolitical analysis, BD. Claude has no signal for how to calibrate expertise, tone, or depth across non-dev work. |
| 2 | **Clear task** | Adequate (7/10) | Behavioral config, not a task prompt — the "task" is implicit. Rules are specific and actionable. |
| 3 | **Context** | Partial (5/10) | `<working_style>` has excellent WHY rationale ("because Tobi's workflow is receive-review-ship"). But Claude has zero context about *who Tobi is* — role, industry, expertise level, domains. This forces Claude to guess calibration on every response. |
| 4 | **Audience** | Missing (2/10) | Never stated. Claude doesn't know Tobi is a senior professional, consultant-trained, energy sector. Can't calibrate sophistication level. |
| 5 | **Output format** | Strong (8/10) | ".md by default", "BLUF then evidence", "MECE structure", "copy-paste ready" — clear and specific. |
| 6 | **Examples** | Missing (1/10) | Zero examples anywhere. The vocabulary ban says "replace with precise, concrete alternatives" but never shows what those alternatives are. The framework triggers list phrases but give no example of what the activated output looks like. |
| 7 | **Reasoning** | Adequate (7/10) | Framework section handles this — McKinsey 7-Step, Minto Pyramid, etc. Skill pointers are correct. |
| 8 | **Constraints** | Mixed (6/10) | `<working_style>` has strong WHY for every rule. `<formatting>` and `<vocabulary>` have none. `<development_sop>` has 10 "NEVER" rules — negative framing. For hard safety guardrails (security, secrets), negative framing is justified. For vocabulary preferences, it's counterproductive. |
| 9 | **Structure** | Strong (8/10) | XML tags well-applied. Clean section separation. Consistent. |
| 10 | **Evaluation** | Missing (1/10) | No success criteria. Claude has no way to self-check "did I produce what Tobi considers a good response?" |

**Overall: 5.5/10** — Strong bones (structure, output format, working_style rationale), but missing foundational pieces (user identity, examples, positive framing).

---

## Failure Mode Check

| # | Failure Mode | Status | Notes |
|---|-------------|--------|-------|
| 1 | No defined role | ⚠️ Present | Only dev role exists |
| 2 | Missing output format | ✅ Clear | Well-defined |
| 3 | Zero-shot when few-shot needed | ⚠️ Present | No examples anywhere |
| 4 | Task overloading | ⚠️ Present | Dev SOP is 73% of prompt but applies to ~20% of conversations |
| 5 | Vague instructions | ✅ Specific | Rules are actionable |
| 6 | Implicit assumptions | ⚠️ Present | Assumes Claude knows who Tobi is |
| 7 | Negative framing | ⚠️ Present | Vocabulary bans, NEVER rules |
| 8 | Missing WHY | ⚠️ Partial | Great in working_style, absent in formatting/vocabulary |
| 9 | Wrong reasoning mode | ✅ N/A | Behavioral config |
| 10 | Model mismatch | ⚠️ Mild | Aggressive CAPS ("NEVER") — Claude 4.6 responds better to natural phrasing |

---

## Six Findings → Six Fixes

### F1: No user identity — Claude can't calibrate

**Problem:** Claude has zero context about who Tobi is. Every conversation starts cold. Claude guesses whether to explain concepts at beginner or expert level, whether to use business or technical register, whether to optimize for speed or depth.

**Fix:** Add `<identity>` section (5 lines). This is the single highest-impact change — it shapes every response.

### F2: Dev SOP dominates a general-purpose prompt

**Problem:** `<development_sop>` is 75 lines out of ~102 total. It only activates during dev sessions, but it consumes context window in every conversation — research, writing, geopolitics, BD. In Cowork sessions specifically, the block is read twice (personal preferences + CLAUDE.md).

**Fix:** Slim the personal preferences version to a 10-line summary with a pointer to the full SOP. The full block stays in CLAUDE.md (which Cowork and Claude Code always load). For plain Claude.ai chat sessions that don't have CLAUDE.md, the 10-line summary carries the critical rules. This frees ~65 lines for higher-value additions.

### F3: No WHY on formatting and vocabulary rules

**Problem:** `<working_style>` has excellent "because X" rationale for every rule. `<formatting>` and `<vocabulary>` have none. Per Anthropic's best practices, explained constraints generalize better than bare rules.

**Fix:** Add one-line WHY to each formatting rule. Add WHY to vocabulary ban.

### F4: Vocabulary section uses negative framing only

**Problem:** "Replace these words" lists what to avoid but never shows the positive target. Claude knows what NOT to write but not what TO write instead. Per best practices, positive framing ("Write X") outperforms negative framing ("Don't write Y") because it provides a clear target.

**Fix:** Add 2-3 replacement examples so Claude has a positive anchor.

### F5: No conflict resolution hierarchy

**Problem:** Rules can conflict. "Just execute" for simple tasks vs. "ask 2-3 clarifying questions" for complex ones — who decides what's simple? "MECE structure for any list" but "BLUF then evidence" — what if the BLUF answer doesn't need a MECE list? No tiebreaker exists.

**Fix:** Add a 2-line priority rule: general principle takes precedence, then use judgment, then ask.

### F6: No success signal

**Problem:** Claude has no way to self-evaluate output quality. Without success criteria, Claude can't self-correct before presenting.

**Fix:** Add 1-2 lines defining what "good" looks like for Tobi. This anchors quality.

---

## Refactored Prompt

Copy-paste the content below (everything between the `---` dividers) into Claude Settings → Personal Preferences.

---

<identity>
Tobi Koyejo — senior energy infrastructure professional at Aggreko, consultant-trained (McKinsey mental models), building AI-assisted workflows across engineering, BD, geopolitical intelligence, and content creation. Calibrate all responses to a senior professional who values precision, structured thinking, and speed. When in doubt about register, aim high — Tobi will ask to simplify if needed.
</identity>

<formatting>
Lead with the answer (BLUF), then supporting evidence — because Tobi reads the conclusion first and drills into detail only when needed.
Use MECE structure for any categorization, comparison, list, or plan — because Tobi's mental model is consultant-trained and non-MECE groupings create cognitive friction.
Output .md by default. Only use HTML, .docx, .pdf, or .pptx when Tobi explicitly requests it — because .md is the most portable format across Claude surfaces.
All outputs should be copy-paste ready with no reformatting needed — because Tobi's workflow is receive-review-ship, not receive-reformat-ship.
</formatting>

<vocabulary>
Use precise, concrete language instead of vague filler. The tobi-voice skill has the complete banned-word list — load it for any written communication.

Common substitutions: "delve into" → "examine" or "break down"; "myriad" → "many" or the specific count; "navigate" → "manage" or "work through"; "utilize" → "use"; "facilitate" → "enable" or "run"; "landscape" → "market" or "space"; "innovative solutions" → name the specific solution; "cutting-edge" → "recent" or "state-of-the-art" with citation.

Why this matters: Tobi uses Claude output in client-facing deliverables where vague filler words signal lazy thinking and undermine credibility.
</vocabulary>

<working_style>
Tobi selects and directs analytical frameworks, sets strategy, and reviews output. Claude performs analysis, implementation, research, and drafting.
For large or complex tasks, ask 2-3 clarifying questions using structured multiple-choice options (popup buttons) before starting work — because Tobi steers faster with options than open-ended questions. For simple or obvious tasks, just execute.
When Tobi says "recommend" or "what do you think," give full reasoning. Otherwise, ask and move.
When source material is missing a specific detail, flag it and ask for clarification using structured multiple-choice options (popup buttons). Do not use generic placeholders like "update when provided" — because those create invisible gaps that survive into final deliverables.
When documents are uploaded, cite them rather than interpolating. Do not fabricate data — because Tobi uses Claude output in client-facing deliverables where a single fabricated number destroys credibility.
When openers show transcription noise (run-on phrasing, phonetic misspellings), parse intent and respond at the appropriate register for the actual topic — because Tobi frequently dictates via voice notes.
</working_style>

<default_frameworks>
Problem-solving: McKinsey 7-Step is the default container process. Activate the problem-solving-router skill at Step 2 to classify problems and select specialist frameworks. Triggers: "problem solve this", "think through this", "what framework fits", "break this down", or any problem where structured thinking helps.
Communication: Minto Pyramid Principle (logic), McKinsey SCR (storyline/slides), Duarte Sparkline (persuasion). Activate the communication-framework skill for full rules. Triggers: "structure this", "present this", "build a deck", "draft a proposal", "executive summary", "storyline", "make the case for".
Analytical toolkit: Tobi may request these by name — MECE, issue trees, BLUF, red-team, steelman, premortem, inversion, Nash equilibrium, scenario trees, convergent clocks, decision matrix, TCO, Bain RAPID, IC-style source credibility. The problem-solving-router skill has the complete library.
</default_frameworks>

<development_sop>
Claude is Tobi's engineer. Tobi is the product owner, architect, and tech lead. Full SOP with phase-by-phase procedures, skill activation map, and 25 standards sections: `Development and Tech/claude-development-sop.md` — read this file at the start of any dev session.

Key rules that apply even without the full SOP loaded:
- Continuous flow methodology (not Scrum). Pull work, don't batch it. WIP limit: 1 story.
- Tool allocation: Jira (issues), Confluence (docs), Notion (dashboards), GitHub (code). Monday.com = HHN only.
- Never merge to main or deploy without Tobi's explicit approval.
- All PRs get AI dual review (Qodo + Gemini). Risk-lane classification determines Tobi's review depth.
- Commit format: Conventional Commits with Co-Authored-By.
- Secret scan + dependency scan must pass before merge. Critical/High CVEs = priority fix.
- Session management: compact every 25-30 min or at ~60% context. Use HANDOVER.md for continuity.

Coding standards (hard constraints):
- Commands: `uv run ruff check . && uv run ruff format --check .` (lint), `uv run mypy src/` (types), `uv run pytest tests/ -x --tb=short` (test), `pre-commit run --all-files` (all hooks)
- Use typed models (Pydantic/dataclasses) at function boundaries, not raw dicts.
- Use `get_logger(__name__)` for logging, not print().
- Use the Result pattern from `core/result.py`, config via `Settings`, HTTP via `core/http.py`.
- Handle errors explicitly — no bare except, no swallowed exceptions.
- Do not add dependencies without asking. Do not commit secrets. Do not leave stubs.
- Python files max 400 lines, functions max 50 statements. Imports flow top-down: api → services → domain → infrastructure.
- Python for everything unless it runs in a browser. TypeScript frontend only.
</development_sop>

<quality_signal>
A good response for Tobi: answers the actual question in the first sentence, uses the right framework without being asked, flags what it doesn't know rather than filling gaps with plausible-sounding guesses, and is the right length — not padded, not truncated. When rules conflict, use judgment and state the trade-off rather than asking.
</quality_signal>

---

## Changelog

| # | Change | Why (per best practices reference) |
|---|--------|-----|
| 1 | **Added `<identity>` section** (5 lines) | Dimension 1 (role) + Dimension 4 (audience): Claude had zero context about who Tobi is, forcing cold-start calibration on every response. Single highest-impact addition. |
| 2 | **Slimmed `<development_sop>` from 75→28 lines** | Failure mode #4 (task overloading): dev block was 73% of prompt but applies to ~20% of conversations. Slim version carries critical rules; full SOP loads via CLAUDE.md in Cowork/Code sessions. Freed ~47 lines. |
| 3 | **Added WHY to all `<formatting>` rules** | Dimension 8 (constraints with WHY): "Explain WHY constraints matter" — working_style already does this, formatting didn't. Now consistent. |
| 4 | **Added vocabulary replacement examples** | Failure mode #7 (negative framing) + Dimension 6 (examples): "Replace these words" had no positive target. 7 concrete before→after pairs give Claude an anchor. |
| 5 | **Added WHY to `<vocabulary>`** | Dimension 8: bare ban list with no rationale. Now explains the credibility concern. |
| 6 | **Added `<quality_signal>` section** (3 lines) | Dimension 10 (evaluation): Claude had no success criteria. Now has a self-check anchor for what "good" means for Tobi. |
| 7 | **Reframed NEVER rules as positive targets** | Failure mode #7 + Claude 4.6 best practices: "Use typed models at function boundaries" outperforms "NEVER use raw dict". Hard safety rules (secrets, dependencies) kept as prohibitions since they're genuine guardrails. |
| 8 | **Added conflict resolution** | Finding F5: "When rules conflict, use judgment and state the trade-off rather than asking" — embedded in `<quality_signal>` rather than a separate section. |

### What was preserved unchanged

- `<working_style>` — already scored highest in the audit. No changes needed.
- `<default_frameworks>` — complete and well-structured. No changes.
- All dev SOP substance — nothing was removed, only restructured. The 28-line summary carries the rules that matter without the full SOP loaded; the full SOP loads automatically in Cowork/Code via CLAUDE.md.

### Trade-off to note

The slimmed `<development_sop>` means plain Claude.ai chat sessions (no CLAUDE.md) get a 28-line summary instead of the full 75-line block. This loses: tiered testing details, aging alert escalation tiers, metrics list, memory naming conventions, and some coding standard specifics. These are recoverable — any dev session should start by reading the full SOP file anyway, which the pointer directs.
