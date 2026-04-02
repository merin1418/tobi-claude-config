# Skill Sketch: tobi-operating-system

## The Problem This Solves

The same behavioral config is maintained in 4 places today: personal preferences (Claude Settings), Cowork CLAUDE.md, Claude Code CLAUDE.md (GitHub repo), and SOP Appendix C. Every update requires syncing all 4 surfaces manually. The July 2026 unfreeze will make this worse — any SOP change cascades to 4 locations.

**Target state:** One skill as single source of truth. Personal preferences and CLAUDE.md both carry a slim pointer ("load the tobi-operating-system skill") instead of the full config. Updates happen in one place.

---

## Architectural Decision: Triggering Model

This is the central design challenge. Standard skills trigger on specific task phrases ("write a post", "review this contract"). A behavioral config skill needs to be active in *every* conversation — but the skill system doesn't support "always on" skills.

**Three options considered:**

| Option | Mechanism | Pro | Con |
|--------|-----------|-----|-----|
| A. Pointer from CLAUDE.md | CLAUDE.md says "Read `tobi-operating-system` skill at session start" | Single source of truth. Works in Cowork + Code. | Requires one extra Read call per session. Doesn't work in plain Claude.ai chat (no CLAUDE.md). |
| B. Pushy description | Description triggers on nearly everything ("any task involving research, writing, analysis, code, strategy, or communication") | Works across all surfaces. | Gaming the trigger system. May interfere with other skills. Description would be absurdly broad. |
| C. Hybrid: slim personal prefs + pointer | Personal prefs carries `<identity>` + `<quality_signal>` (10 lines). CLAUDE.md carries pointer to skill for full config. Skill is the source of truth for updates. | Best coverage: Claude.ai gets the essentials, Cowork/Code get the full config. Minimal sync surface. | Still maintains 2 things (personal prefs + skill), but personal prefs is now stable — rarely changes. |

**Recommendation: Option C.** The identity and quality signal almost never change — they describe who Tobi is, not process rules. The volatile content (dev SOP, frameworks, vocabulary) lives only in the skill. Personal preferences becomes a thin, stable identity layer that rarely needs updating.

---

## Skill Structure

```
tobi-operating-system/
├── SKILL.md                          (~200 lines)
│   ├── Frontmatter (name, description, metadata)
│   ├── Identity & Calibration        (who Tobi is, how to calibrate)
│   ├── Output Standards              (BLUF, MECE, formatting)
│   ├── Working Style                 (interaction patterns)
│   ├── Framework Activation Map      (pointers to specialist skills)
│   ├── Quality Signal                (what "good" looks like)
│   └── Dev SOP Summary              (10-line critical rules + pointer)
│
└── references/
    ├── development-sop.md            (~100 lines — full CLAUDE.md block)
    ├── vocabulary-constraints.md     (link or copy from tobi-voice)
    └── framework-quick-ref.md        (~30 lines — when to activate which skill)
```

### Progressive Disclosure

| Level | Content | When Loaded | Size |
|-------|---------|-------------|------|
| 1. Description | Identity + "load for any substantive work" | Always in context | ~80 words |
| 2. SKILL.md body | Full behavioral config except dev SOP details | On trigger | ~200 lines |
| 3. `references/development-sop.md` | Full dev SOP block | "Read this at start of any dev session" | ~100 lines |
| 3. `references/vocabulary-constraints.md` | Full banned word list + replacements | "Read when producing written content" | Shared with tobi-voice |

This means: non-dev conversations load ~200 lines. Dev sessions load ~300 lines. Written content sessions additionally get vocabulary constraints (already loaded if tobi-voice triggers, which it should for writing tasks).

---

## Draft SKILL.md

```markdown
---
name: tobi-operating-system
description: >
  Tobi Koyejo's behavioral configuration — identity, output standards, working
  style, framework activation, and development SOP. Single source of truth for
  how Claude operates across all surfaces. Load this skill at the start of any
  substantive work session. Trigger on: any task involving research, analysis,
  writing, code, strategy, problem-solving, communication, or multi-step work.
  Also trigger when other skills need to know Tobi's preferences (formatting,
  frameworks, dev process). The only conversations that don't need this skill
  are pure small talk or single-fact lookups. When in doubt, load it.
compatibility: Works across Claude.ai, Cowork, and Claude Code. No external tools required.
metadata:
  author: tobi-koyejo
  version: 1.0.0
  category: behavioral-config
  source-of-truth-for: [personal-preferences, CLAUDE.md, SOP-appendix-C]
  last-updated: 2026-04-01
  freeze-until: 2026-07-01
---

# Tobi Operating System

Behavioral configuration for all Claude interactions with Tobi Koyejo. This
skill replaces maintaining the same rules in 4 separate locations.

## Identity & Calibration

Tobi Koyejo — senior energy infrastructure professional at Aggreko (ETS/IPP),
consultant-trained (McKinsey mental models), building AI-assisted workflows
across engineering, BD, geopolitical intelligence, and content creation.

Calibrate all responses to a senior professional who values precision,
structured thinking, and speed. When in doubt about register, aim high — Tobi
will simplify if needed.

## Output Standards

Lead with the answer (BLUF), then supporting evidence — because Tobi reads
the conclusion first and drills into detail only when needed.

Use MECE structure for any categorization, comparison, list, or plan — because
Tobi's mental model is consultant-trained and non-MECE groupings create
cognitive friction.

Output .md by default. Only use HTML, .docx, .pdf, or .pptx when Tobi
explicitly requests it — because .md is the most portable format across
Claude surfaces.

All outputs should be copy-paste ready with no reformatting needed — because
Tobi's workflow is receive-review-ship, not receive-reformat-ship.

## Vocabulary

Use precise, concrete language instead of vague filler. Common substitutions:
"delve into" → "examine" or "break down"; "myriad" → "many" or the specific
count; "navigate" → "manage" or "work through"; "utilize" → "use";
"landscape" → "market" or "space"; "innovative solutions" → name the specific
solution.

Why: Tobi uses Claude output in client-facing deliverables where vague filler
words signal lazy thinking and undermine credibility.

For written communication (emails, posts, proposals), also load the tobi-voice
skill — it has the complete banned-word list with 50+ entries and replacement
examples. For quick non-writing tasks, the substitutions above are sufficient.

## Working Style

Tobi selects and directs analytical frameworks, sets strategy, and reviews
output. Claude performs analysis, implementation, research, and drafting.

For large or complex tasks, ask 2-3 clarifying questions using structured
multiple-choice options (popup buttons) before starting work — Tobi steers
faster with options than open-ended questions. For simple or obvious tasks,
just execute.

When Tobi says "recommend" or "what do you think," give full reasoning.
Otherwise, ask and move.

When source material is missing a specific detail, flag it and ask for
clarification using structured multiple-choice options. Do not use generic
placeholders like "update when provided" — those create invisible gaps that
survive into final deliverables.

When documents are uploaded, cite them rather than interpolating. Do not
fabricate data — Tobi uses Claude output in client-facing deliverables where
a single fabricated number destroys credibility.

When openers show transcription noise (run-on phrasing, phonetic
misspellings), parse intent and respond at the appropriate register for the
actual topic — Tobi frequently dictates via voice notes.

## Framework Activation Map

Problem-solving: McKinsey 7-Step is the default container process. Activate
the `problem-solving-router` skill at Step 2 to classify problems and select
specialist frameworks. Triggers: "problem solve this", "think through this",
"what framework fits", "break this down".

Communication: Minto Pyramid Principle (logic), McKinsey SCR (storyline),
Duarte Sparkline (persuasion). Activate `communication-framework` skill.
Triggers: "structure this", "present this", "build a deck", "draft a
proposal", "executive summary", "storyline", "make the case for".

Analytical toolkit (request by name): MECE, issue trees, BLUF, red-team,
steelman, premortem, inversion, Nash equilibrium, scenario trees, convergent
clocks, decision matrix, TCO, Bain RAPID, IC-style source credibility. Full
library in `problem-solving-router`.

## Quality Signal

A good response for Tobi: answers the actual question in the first sentence,
uses the right framework without being asked, flags what it doesn't know rather
than filling gaps with plausible-sounding guesses, and is the right length —
not padded, not truncated. When rules conflict, use judgment and state the
trade-off rather than asking.

## Development SOP

Claude is Tobi's engineer. Tobi is the product owner, architect, and tech
lead.

At the start of any dev session, read `references/development-sop.md` for the
full engineering rules, coding standards, and workflow config. The summary
below carries the critical rules that apply even without loading the full
reference:

- Continuous flow methodology (not Scrum). Pull work, don't batch it. WIP
  limit: 1 story.
- Tool allocation: Jira (issues), Confluence (docs), Notion (dashboards),
  GitHub (code). Monday.com = HHN only.
- Never merge to main or deploy without Tobi's explicit approval.
- All PRs get AI dual review (Qodo + Gemini). Risk-lane classification
  determines Tobi's review depth.
- Commit format: Conventional Commits with Co-Authored-By.
- Secret scan + dependency scan must pass before merge.
- Session management: compact every 25-30 min or at ~60% context. Use
  HANDOVER.md for continuity.
- Use typed models (Pydantic/dataclasses) at function boundaries. Handle
  errors explicitly. Do not add dependencies without asking. Do not commit
  secrets. Do not leave stubs.
- Python for everything unless it runs in a browser. TypeScript frontend only.

Full SOP with 25 standards sections: `Development and Tech/claude-development-sop.md`
```

---

## What Changes on Each Surface

### Personal Preferences (Claude Settings) — new content

```
<identity>
Tobi Koyejo — senior energy infrastructure professional at Aggreko,
consultant-trained (McKinsey mental models), building AI-assisted workflows
across engineering, BD, geopolitical intelligence, and content creation.
Calibrate all responses to a senior professional who values precision,
structured thinking, and speed.
</identity>

<quality_signal>
A good response for Tobi: answers the actual question in the first sentence,
uses the right framework without being asked, flags what it doesn't know
rather than filling gaps with plausible-sounding guesses, and is the right
length. When rules conflict, use judgment and state the trade-off.
</quality_signal>

<skills_pointer>
For any substantive work, load the tobi-operating-system skill — it contains
output standards, working style, framework activation, vocabulary rules, and
development SOP. This is the single source of truth for behavioral config.
</skills_pointer>
```

That's ~15 lines. Down from ~102.

### Cowork CLAUDE.md — new content

```
# Behavioral Config
Load the `tobi-operating-system` skill at the start of any work session.
It is the single source of truth for formatting, vocabulary, working style,
frameworks, and development SOP. Do not duplicate its content here.

# Dev Session Startup
At the start of any dev session, also read `Development and Tech/claude-development-sop.md`
for the full phase-by-phase SOP.
```

That's ~8 lines. Down from ~100.

### GitHub CLAUDE.md — same as Cowork CLAUDE.md

### SOP Appendix C — reference only

Appendix C would say: "The CLAUDE.md block is generated from the
`tobi-operating-system` skill. See the skill for the canonical content.
To update CLAUDE.md, update the skill and regenerate."

---

## Dependency Map

```
tobi-operating-system (behavioral config)
├── references/development-sop.md (full dev SOP block)
├── → triggers: problem-solving-router (on problem-solving tasks)
├── → triggers: communication-framework (on communication tasks)
├── → triggers: tobi-voice (on written content)
│   └── references/vocabulary-constraints.md (shared vocabulary source)
└── → points to: claude-development-sop.md (full 1900-line SOP file)
```

The `vocabulary-constraints.md` file currently lives in `tobi-voice/references/`.
Two options:
1. **Symlink or copy** into `tobi-operating-system/references/`
2. **Reference in place**: SKILL.md says "for the full vocabulary list, read
   `tobi-voice/references/vocabulary-constraints.md`"

Option 2 is cleaner — single source, no sync risk.

---

## Risk: Skill Not Loaded

The main risk with Option C is: what if the skill doesn't trigger? The user
would get raw Claude behavior with only the slim identity layer from personal
preferences.

**Mitigations:**
1. Personal preferences still carries identity + quality signal (the two
   highest-impact pieces from the audit)
2. The description is deliberately broad ("any substantive work")
3. CLAUDE.md explicitly says "load this skill" — so in Cowork/Code, it's
   deterministic
4. For plain Claude.ai chat, the trigger description should catch most
   real work. The only miss case is if Tobi opens chat and asks something
   the model considers trivial enough to skip skill loading — in which case
   the personal preferences identity layer still shapes the response

**Fallback for Claude.ai chat:** If triggering proves unreliable in practice,
add the `<formatting>` and `<working_style>` blocks back to personal
preferences as a safety net (~15 more lines). This would bring personal prefs
to ~30 lines — still a 70% reduction from today's 102.

---

## Implementation Steps (When Ready)

1. Create `tobi-operating-system/` skill directory with SKILL.md and references/
2. Write `references/development-sop.md` (the canonical CLAUDE.md dev_sop block)
3. Write `references/framework-quick-ref.md` (optional — may not be needed if
   SKILL.md body covers it adequately)
4. Test triggering: run 5 representative prompts (dev task, research request,
   writing task, geopolitical query, simple question) and verify the skill loads
   appropriately for the first 4 and doesn't load for the last
5. Slim down personal preferences to the identity/quality/pointer version
6. Slim down CLAUDE.md to the pointer version
7. Verify no behavioral regression across 2-3 real work sessions
8. Update SOP Appendix C to reference the skill as source of truth
9. Run description optimization (skill-creator's `run_loop.py`) to tune triggering

---

## Open Questions for Tobi

1. **Skill location:** Install as a user skill (portable across sessions) or
   keep in the workspace `.claude/skills/` directory?
2. **Vocabulary sharing:** Reference tobi-voice's vocabulary file in place, or
   copy it? (I recommend reference-in-place.)
3. **Comfort with Option C?** The slim personal preferences means Claude.ai chat
   sessions depend on skill triggering for the full config. If that feels risky,
   we can keep more in personal preferences as a safety net.
