# tobi-operating-system Cross-Reference Audit

## BLUF

The skill has 3 outbound references (problem-solving-router, communication-framework, tobi-voice) and 0 inbound references from other skills. After reading all 34 skills, I found **12 missing cross-references** across 4 categories: the Framework Activation Map is incomplete (covers only 3 of 14 routable skills), no skills reference back to tobi-operating-system as the behavioral authority, the dev SOP reference doesn't mention ai-test-engineer by name, and several content-creation skills lack pointers to brand guidelines they need.

---

## Current State

### tobi-operating-system references OUT (3):

| Referenced Skill | Where | Purpose |
|---|---|---|
| `problem-solving-router` | Framework Activation Map, framework-quick-ref.md | Problem-solving framework selection |
| `communication-framework` | Framework Activation Map, framework-quick-ref.md | Presentation/proposal structure |
| `tobi-voice` | Vocabulary section, framework-quick-ref.md | Voice profile + vocabulary constraints |

### tobi-operating-system references IN (0):

No skill currently references tobi-operating-system. The skill was just built and no other skills have been updated to point to it.

---

## Finding 1: Framework Activation Map is incomplete

**Problem:** The Framework Activation Map in SKILL.md covers 3 skills (problem-solving-router, communication-framework, tobi-voice). But framework-quick-ref.md routes to 14 skills. The SKILL.md body — which is always loaded — should route to the full skill portfolio, not just the analytical tier.

**Missing from SKILL.md body:** Content creation (4 LinkedIn skills), geopolitical analysis (geopolitical-crisis-analyst, iran-war-analyst, osint-researcher), research (reddit-research), dev testing (ai-test-engineer), brand guidelines (aggreko-brand-guidelines, merin-brand-guidelines), voice transcription (voice-note-transcription).

**Recommendation:** Expand the Framework Activation Map section in SKILL.md to include a compact routing table covering all skill families. This doesn't mean listing every skill — group them by domain:

```
Content creation: Load `tobi-voice` for any written communication. For
LinkedIn posts, also load the domain-specific skill: `linkedin-thought-
leadership` (energy/industry), `linkedin-personal-motivation` (leadership/
personal), `linkedin-educational-frameworks` (C&I education), or `linkedin-
repost-amplification` (reshares). For Aggreko-branded content, also load
`aggreko-brand-guidelines`. For Mẹ́rin-branded content, load `merin-brand-
guidelines`.

Geopolitical analysis: For crisis/conflict analysis, load `geopolitical-
crisis-analyst`. For Iran/Middle East conflict specifically, load `iran-war-
analyst` (orchestrates 10 helper skills automatically). For deep research
on any entity or event, load `osint-researcher`.

Research: For Reddit community intelligence, load `reddit-research`.

Dev testing: Run `ai-test-engineer` for all new code per the tiered testing
policy in the dev SOP.

Transcription: When processing voice notes, load `voice-note-transcription`
(mobile) or `voice-note-transcription-local` (Mac/batch).
```

**Impact:** HIGH. Without this, Claude loads tobi-operating-system but doesn't know the full skill portfolio exists for content, geopolitical, and research tasks.

---

## Finding 2: No skills reference back to tobi-operating-system

**Problem:** The skill is designed as the "single source of truth" but no other skill knows it exists. When `tobi-voice` loads independently (e.g., "write an email"), it has its own identity section and voice profile but no pointer to the behavioral config. When `communication-framework` loads, it has the three-layer architecture but doesn't mention the meta-layer above it.

**Skills that should add a pointer to tobi-operating-system:**

| Skill | Why | Proposed addition |
|---|---|---|
| `tobi-voice` | Has its own identity snapshot that duplicates tobi-operating-system's identity section. Should acknowledge the canonical source. | Add to top of SKILL.md: "For Tobi's full behavioral config (output standards, working style, frameworks), see the `tobi-operating-system` skill. This skill governs voice specifically." |
| `communication-framework` | Defines the three-layer architecture but doesn't mention the meta-layer. | Add to the three-layer description: "`tobi-operating-system` provides the behavioral meta-layer (output standards, BLUF, MECE) that sits above all three." |
| `problem-solving-router` | Says "The user's default problem-solving approach is McKinsey 7-step" without sourcing it. | Add: "This default is set in the `tobi-operating-system` skill." |
| `ai-test-engineer` | Used in dev sessions governed by the SOP but doesn't reference it. | Add: "Testing tiers (S/M = unit + 80%; L/XL = mutation + property-based) are defined in the dev SOP. See `tobi-operating-system` for the full config." |
| `doc-coauthoring` | Produces structured documents but doesn't reference Tobi's output standards (BLUF, MECE) or voice preferences. | Add: "For Tobi's output format preferences, see `tobi-operating-system`. For voice, also load `tobi-voice`." |

**Impact:** MEDIUM. These skills still work independently, but adding the pointer ensures consistency and helps Claude understand the skill hierarchy.

---

## Finding 3: Dev SOP reference doesn't mention ai-test-engineer

**Problem:** The SKILL.md Development SOP section and references/development-sop.md both mention "Run ai-test-engineer for all new code" in the Engineering Rules, which is correct. But the SKILL.md *body* (the always-loaded summary) doesn't mention it. The tiered testing policy is only visible if Claude reads the full reference file.

**Recommendation:** Add one line to the SKILL.md coding standards summary:

```
- Run `ai-test-engineer` skill for all new code. Tiered: S/M = unit + 80%
  coverage; L/XL = add mutation + property-based.
```

**Impact:** LOW-MEDIUM. The full reference already has this, but the SKILL.md summary is what governs behavior when the reference isn't loaded.

---

## Finding 4: Content-creation skills lack brand guideline pointers

**Problem:** `linkedin-thought-leadership` mentions Aggreko extensively (inline hashtags, solution knowledge, proof points) but never references `aggreko-brand-guidelines` for visual identity when producing carousels. Same for `linkedin-educational-frameworks`. If Canva or Figma connectors are used to generate carousel visuals, the brand colors, typography, and logo rules in `aggreko-brand-guidelines` should be consulted.

**Skills that should reference brand guidelines:**

| Skill | Brand Guideline | Why |
|---|---|---|
| `linkedin-thought-leadership` | `aggreko-brand-guidelines` | Carousel generation via Canva/Figma uses Aggreko visual identity |
| `linkedin-educational-frameworks` | `aggreko-brand-guidelines` | Carousel slides with Aggreko CTA need brand-consistent design |
| `linkedin-repost-amplification` | `aggreko-brand-guidelines` | Occasionally generates visual content for reshares |

**Recommendation:** Add to each skill's Tool Routing section: "When generating visuals via Canva or Figma, also consult `aggreko-brand-guidelines` for colors (#FD6E39 accent, #1D2C35 background), typography (Inter Tight), and logo usage rules."

**Impact:** MEDIUM. Visual consistency on branded carousel content.

---

## Finding 5: tobi-voice identity is duplicated, not referenced

**Problem:** `tobi-voice` has its own identity line: "Nigerian-American energy BD leader (Aggreko ETS), strategy consultant, former McKinsey/Accenture, Yale FDCE." `tobi-operating-system` has: "senior energy infrastructure professional at Aggreko (ETS/IPP), consultant-trained (McKinsey mental models)." These are consistent today but maintained independently. If one changes, the other won't know.

**Recommendation:** This is a SHOULD-fix, not a MUST-fix. Two options:

1. **Reference in place** (cleanest): tobi-voice says "For Tobi's identity, see `tobi-operating-system`. This skill adds voice-specific detail: Nigerian-American, Yale FDCE, practitioner lens."
2. **Accept the duplication** (simplest): The two descriptions serve different purposes — tobi-operating-system calibrates response level, tobi-voice calibrates writing authenticity. Keep both. Just ensure they don't contradict.

I recommend Option 2 for now, with a note in both skills to cross-check if identity details change.

**Impact:** LOW. No current inconsistency, but drift risk over time.

---

## Finding 6: framework-quick-ref.md is solid but missing 3 skills

**Problem:** The reference file routes to 14 skills but misses:

1. `prediction-market-tracker` — For "what do prediction markets think" queries
2. `voice-note-transcription` / `voice-note-transcription-local` — For audio processing
3. `prompt-steelman` — For prompt improvement tasks

**Recommendation:** Add three rows to the routing table:

```
| Prediction market data        | `prediction-market-tracker`     | Polymarket + event probabilities    |
| Transcribe audio/voice notes  | `voice-note-transcription`      | Mobile; use `-local` for Mac batch  |
| Improve/steelman a prompt     | `prompt-steelman`               | 10-dimension audit + improvement    |
```

**Impact:** LOW. These skills trigger fine on their own descriptions, but completeness in the routing table prevents blind spots.

---

## Summary: Prioritized Action List

| # | Action | Impact | Effort | Where |
|---|--------|--------|--------|-------|
| 1 | Expand Framework Activation Map in SKILL.md to cover all skill families | HIGH | ~20 lines added | tobi-operating-system/SKILL.md |
| 2 | Add ai-test-engineer mention to SKILL.md coding standards summary | MED | 2 lines | tobi-operating-system/SKILL.md |
| 3 | Add tobi-operating-system pointer to tobi-voice, communication-framework, problem-solving-router | MED | 1-2 lines each in 3 skills |
| 4 | Add aggreko-brand-guidelines pointer to LinkedIn content skills | MED | 1-2 lines each in 3 skills |
| 5 | Add 3 missing rows to framework-quick-ref.md | LOW | 3 lines | tobi-operating-system/references/ |
| 6 | Add doc-coauthoring + ai-test-engineer back-pointers to tobi-operating-system | LOW | 1-2 lines each in 2 skills |
| 7 | Cross-check identity descriptions between tobi-voice and tobi-operating-system | LOW | Review only |

Items 1-2 are changes to the skill itself. Items 3-6 are changes to other skills that reference it. Item 7 is a review-only check.
