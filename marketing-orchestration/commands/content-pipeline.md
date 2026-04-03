---
description: "Orchestrate end-to-end content creation from strategy through production, SEO optimization, and multi-platform distribution"
argument-hint: "<topic or content brief> [--type blog|guide|landing|social] [--platforms web,twitter,linkedin]"
---

# Content Pipeline Orchestrator

## CRITICAL BEHAVIORAL RULES

1. **Follow phase order strictly.** Never skip ahead — each phase depends on the previous.
2. **Read state files before acting.** Every step reads relevant prior outputs before starting.
3. **Checkpoint before proceeding.** Each phase ends with a user approval checkpoint.
4. **Halt on failure.** If any step fails or produces low-quality output, stop and report.
5. **Use only local agents.** All `subagent_type` references use agents from this plugin or `general-purpose`.
6. **Never enter plan mode.** This command IS the plan — execute it directly.

## Pre-flight Checks

1. Check if `.agents/marketing-context.md` exists
   - If NO → halt and tell the user to run `marketing-strategist` first to set up context
   - If YES → read it and extract brand voice, ICP, and content goals
2. Parse `$ARGUMENTS` for topic, content type, and target platforms
3. Initialize state directory:

```
.content-pipeline/
├── state.json          # Phase tracking
├── strategy.md         # Phase 1 output
├── draft.md            # Phase 2 output
├── seo-review.md       # Phase 3 output
└── distribution.md     # Phase 4 output
```

Initialize `state.json`:
```json
{
  "topic": "$TOPIC",
  "type": "$TYPE",
  "platforms": "$PLATFORMS",
  "phase": "strategy",
  "started_at": "$TIMESTAMP"
}
```

---

## Phase 1: Strategy (Step 1) — Content Planning

### Step 1: Content Strategy & Research

Task:
  subagent_type: "mkt-content-strategist"
  description: "Develop content strategy for: $TOPIC"
  prompt: |
    Read `.agents/marketing-context.md` for brand context.

    Create a content strategy brief for: $TOPIC
    Content type: $TYPE

    Deliver:
    1. Target audience segment and their pain point this addresses
    2. Search intent analysis (informational/commercial/transactional)
    3. 3 angle options with rationale — recommend one
    4. Outline with H2/H3 structure
    5. Key messages and CTAs
    6. Competitor content audit (what exists, what's missing)

    Write output to `.content-pipeline/strategy.md`

Update `state.json`: `"phase": "strategy_complete"`

---

## PHASE CHECKPOINT 1 — User Approval Required

Present the strategy brief and ask:
1. **Approve** — proceed to drafting with the recommended angle
2. **Pick different angle** — proceed with user's chosen angle
3. **Revise** — re-run strategy with user feedback

---

## Phase 2: Production (Steps 2-3) — Drafting & Editing

### Step 2: Content Drafting

Task:
  subagent_type: "mkt-content-strategist"
  description: "Draft content based on approved strategy"
  prompt: |
    Read `.agents/marketing-context.md` for brand voice.
    Read `.content-pipeline/strategy.md` for the approved strategy.

    Write the full content piece following:
    - The approved outline and angle
    - Brand voice from marketing context
    - Target audience language and pain points
    - Proper heading hierarchy, internal linking opportunities
    - Strong intro hook and clear CTAs

    Write output to `.content-pipeline/draft.md`

### Step 3: Content Humanization & Editing

Task:
  subagent_type: "mkt-content-strategist"
  description: "Edit and humanize the draft"
  prompt: |
    Read `.content-pipeline/draft.md`
    Read `.agents/marketing-context.md` for brand voice reference.

    Review and improve:
    1. Remove any AI-sounding patterns (hedging, filler, generic transitions)
    2. Strengthen the opening — hook within first 2 sentences
    3. Tighten prose — remove unnecessary words
    4. Ensure brand voice consistency throughout
    5. Check readability (aim for Grade 8 level)
    6. Verify all CTAs are specific and compelling

    Update `.content-pipeline/draft.md` with the edited version.

Update `state.json`: `"phase": "production_complete"`

---

## PHASE CHECKPOINT 2 — User Approval Required

Present the edited draft and ask:
1. **Approve** — proceed to SEO optimization
2. **Request changes** — provide specific feedback for revision
3. **Pause** — save state and resume later

---

## Phase 3: Optimization (Step 4) — SEO Review

### Step 4: SEO Optimization

Task:
  subagent_type: "mkt-seo-specialist"
  description: "SEO-optimize the content piece"
  prompt: |
    Read `.content-pipeline/draft.md` (the approved content)
    Read `.content-pipeline/strategy.md` (for target keywords and intent)
    Read `.agents/marketing-context.md` (for SEO context)

    Perform SEO optimization:
    1. Title tag and meta description recommendations
    2. Heading structure optimization (H1/H2/H3 with keywords)
    3. Internal linking opportunities
    4. Schema markup recommendations (Article, FAQ, HowTo as appropriate)
    5. Image alt text suggestions
    6. AI search optimization — structure for featured snippets and AI answers

    Write SEO recommendations to `.content-pipeline/seo-review.md`
    Apply non-controversial optimizations directly to `.content-pipeline/draft.md`

Update `state.json`: `"phase": "optimization_complete"`

---

## Phase 4: Distribution (Step 5) — Multi-Platform Adaptation

### Step 5: Platform Adaptation

Task:
  subagent_type: "mkt-content-strategist"
  description: "Adapt content for distribution platforms: $PLATFORMS"
  prompt: |
    Read `.content-pipeline/draft.md` (the final optimized content)
    Read `.agents/marketing-context.md` (for brand voice)

    Create platform-specific versions for: $PLATFORMS

    For each platform, produce:
    - Platform-optimized version (length, format, tone adjustments)
    - Hashtag and mention strategy
    - Best posting time recommendation
    - Engagement hooks specific to that platform

    Write all versions to `.content-pipeline/distribution.md`

Update `state.json`: `"phase": "complete"`

---

## PHASE CHECKPOINT 3 — Final Review

Present all deliverables:
- Original content piece (SEO-optimized)
- SEO recommendations
- Platform-adapted versions

Ask:
1. **Complete** — all deliverables accepted
2. **Revise specific piece** — re-run that step only
3. **Archive** — save state for reference

## Completion

Update `state.json`: `"phase": "delivered", "completed_at": "$TIMESTAMP"`
Summarize all outputs and their file locations.
