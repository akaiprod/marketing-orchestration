---
description: "Define or refine brand positioning, messaging framework, and voice guidelines"
argument-hint: "<brand or product name> [--stage startup|growth|rebrand] [--focus positioning|messaging|voice|all]"
---

# Brand Positioning Orchestrator

## CRITICAL BEHAVIORAL RULES

1. **Follow phase order strictly.** Never skip ahead — each phase depends on the previous.
2. **Read state files before acting.** Every step reads relevant prior outputs before starting.
3. **Checkpoint before proceeding.** Each phase ends with a user approval checkpoint.
4. **Halt on failure.** If any step fails or produces low-quality output, stop and report.
5. **Use only local agents.** All `subagent_type` references use agents from this plugin or `general-purpose`.
6. **Never enter plan mode.** This command IS the plan — execute it directly.

## Pre-flight Checks

1. Check if `.agents/marketing-context.md` exists
   - If YES → read it as the starting point — we're refining existing positioning
   - If NO → this is a fresh build — that's fine, we'll create the context
2. Parse `$ARGUMENTS` for brand name, stage, and focus area
3. Initialize state directory:

```
.brand-positioning/
├── state.json               # Phase tracking
├── market-landscape.md      # Phase 1 output
├── positioning-strategy.md  # Phase 2 output
├── messaging-framework.md   # Phase 3 output
├── voice-guidelines.md      # Phase 4 output
└── brand-playbook.md        # Phase 5 output
```

Initialize `state.json`:
```json
{
  "brand": "$BRAND",
  "stage": "$STAGE",
  "focus": "$FOCUS",
  "phase": "landscape",
  "started_at": "$TIMESTAMP"
}
```

---

## Phase 1: Market Landscape (Step 1) — Where We Play

### Step 1: Market & Audience Analysis

Task:
  subagent_type: "mkt-marketing-strategist"
  description: "Analyze market landscape for: $BRAND positioning"
  prompt: |
    Read `.agents/marketing-context.md` if it exists (existing positioning to build on).

    Analyze the market landscape for: $BRAND
    Company stage: $STAGE

    Deliver:
    1. **Market definition**: What market are we in? (not what we think — what customers think)
    2. **Category analysis**: Is there an established category or are we creating one?
    3. **Audience segments**: 2-3 ICPs with demographics, psychographics, and jobs-to-be-done
    4. **Competitive landscape**: Key players, their positions, and white space
    5. **Market trends**: What's changing that creates opportunity or threat
    6. **Our current perception**: How the market sees us today (if applicable)

    Write output to `.brand-positioning/market-landscape.md`

Update `state.json`: `"phase": "landscape_complete"`

---

## PHASE CHECKPOINT 1 — User Approval Required

Present the landscape analysis and ask:
1. **Approve** — proceed to positioning strategy
2. **Correct assumptions** — provide insider context about market/audience
3. **Expand scope** — include additional segments or competitors

---

## Phase 2: Positioning Strategy (Step 2) — What We Stand For

### Step 2: Positioning Framework

Task:
  subagent_type: "mkt-marketing-strategist"
  description: "Develop positioning strategy for: $BRAND"
  prompt: |
    Read `.brand-positioning/market-landscape.md` for market context.
    Read `.agents/marketing-context.md` if it exists.

    Develop the positioning strategy:
    1. **Positioning statement**: For [target], [brand] is the [category] that [key benefit] because [reason to believe]
    2. **3 positioning options**: Different strategic angles with trade-offs
    3. **Recommended position**: Which one and why — with competitive defensibility analysis
    4. **Key differentiators**: 3-5 things that are uniquely true about us
    5. **Proof points**: Evidence that supports each differentiator
    6. **Category strategy**: Compete in existing category vs create new one — recommendation

    Write output to `.brand-positioning/positioning-strategy.md`

Update `state.json`: `"phase": "positioning_complete"`

---

## PHASE CHECKPOINT 2 — User Approval Required

Present positioning options and ask:
1. **Approve recommended** — proceed with the suggested position
2. **Choose alternative** — pick a different positioning angle
3. **Blend** — combine elements from multiple options

---

## Phase 3: Messaging Framework (Step 3) — How We Say It

### Step 3: Messaging Architecture

Task:
  subagent_type: "mkt-content-strategist"
  description: "Build messaging framework for: $BRAND"
  prompt: |
    Read `.brand-positioning/positioning-strategy.md` for the approved position.
    Read `.brand-positioning/market-landscape.md` for audience context.
    Read `.agents/marketing-context.md` if it exists.

    Build the messaging framework:
    1. **Tagline options**: 3-5 options that capture the positioning in <8 words
    2. **Elevator pitch**: 30-second, 60-second, and 2-minute versions
    3. **Message hierarchy**: Primary message → supporting messages → proof points
    4. **Audience-specific messaging**: Tailored value props per ICP segment
    5. **Objection handling**: Top 5 objections and how to address them in messaging
    6. **Boilerplate copy**: About us (short, medium, long versions)

    Write output to `.brand-positioning/messaging-framework.md`

Update `state.json`: `"phase": "messaging_complete"`

---

## Phase 4: Voice & Tone (Step 4) — How We Sound

### Step 4: Voice Guidelines

Task:
  subagent_type: "mkt-content-strategist"
  description: "Define brand voice guidelines for: $BRAND"
  prompt: |
    Read `.brand-positioning/positioning-strategy.md` for brand position.
    Read `.brand-positioning/messaging-framework.md` for messaging tone.
    Read `.agents/marketing-context.md` if it exists.

    Define the brand voice:
    1. **Voice attributes**: 3-4 adjectives that define how we sound (e.g., "confident but not arrogant")
    2. **Voice spectrum**: For each attribute — this, not that (with examples)
    3. **Tone variations**: How voice shifts by context (marketing, support, error messages, social)
    4. **Vocabulary guide**: Words we use, words we avoid, jargon policy
    5. **Writing examples**: Before/after rewrites showing voice in action
    6. **Do/Don't list**: Concrete dos and don'ts for anyone writing as the brand

    Write output to `.brand-positioning/voice-guidelines.md`

Update `state.json`: `"phase": "voice_complete"`

---

## Phase 5: Brand Playbook (Step 5) — Consolidated Reference

### Step 5: Brand Playbook

Synthesize all outputs into a single reference document:

Read all files in `.brand-positioning/` and create:
1. **One-page brand summary**: Position + key messages + voice — fits on one page
2. **Full brand playbook**: Everything organized for team reference
3. **Marketing context update**: Updated `.agents/marketing-context.md` reflecting new positioning
4. **Quick-reference card**: Cheat sheet for daily use (tagline, elevator pitch, voice dos/don'ts)

Write playbook to `.brand-positioning/brand-playbook.md`
Update `.agents/marketing-context.md` with the new positioning

Present the complete brand package and ask:
1. **Ship it** — adopt as official brand guidelines
2. **Revise section** — re-run specific phase
3. **Workshop** — use as starting point for team discussion

## Completion

Update `state.json`: `"phase": "delivered", "completed_at": "$TIMESTAMP"`
Summarize all deliverables with file locations.
