---
description: "Deep competitive intelligence — analyze competitors' positioning, content, pricing, and find strategic gaps"
argument-hint: "<competitor name or URL> [--vs self|alternative] [--depth quick|deep]"
---

# Competitor Intelligence Orchestrator

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
   - If YES → read it and extract own positioning, ICP, and competitive landscape
2. Parse `$ARGUMENTS` for competitor name/URL, comparison mode, and depth
3. Initialize state directory:

```
.competitor-intel/
├── state.json             # Phase tracking
├── profile.md             # Phase 1 output
├── content-analysis.md    # Phase 2 output
├── positioning-map.md     # Phase 3 output
├── gap-analysis.md        # Phase 4 output
└── action-plan.md         # Phase 5 output
```

Initialize `state.json`:
```json
{
  "competitor": "$COMPETITOR",
  "mode": "$MODE",
  "depth": "$DEPTH",
  "phase": "profiling",
  "started_at": "$TIMESTAMP"
}
```

---

## Phase 1: Profiling (Step 1) — Competitor Overview

### Step 1: Competitor Profile

Task:
  subagent_type: "mkt-campaign-analyst"
  description: "Build competitor profile for: $COMPETITOR"
  prompt: |
    Read `.agents/marketing-context.md` for our own positioning context.

    Build a comprehensive competitor profile for: $COMPETITOR

    Deliver:
    1. **Company overview**: What they do, who they serve, company stage/size
    2. **Product offering**: Features, pricing tiers, free vs paid, key differentiators
    3. **Target audience**: Who they're going after (overlap with our ICP?)
    4. **Messaging analysis**: Tagline, value prop, key claims, tone of voice
    5. **Channel presence**: Website, social, email, community, partnerships
    6. **Strengths & weaknesses**: Honest assessment from a customer's perspective

    Write output to `.competitor-intel/profile.md`

Update `state.json`: `"phase": "profile_complete"`

---

## PHASE CHECKPOINT 1 — User Approval Required

Present the profile and ask:
1. **Approve** — proceed to content and SEO analysis
2. **Add competitors** — expand analysis to include more players
3. **Add context** — user provides insider knowledge

---

## Phase 2: Content & SEO Analysis (Steps 2-3) — Parallel

### Step 2: Content Strategy Analysis

Task:
  subagent_type: "mkt-campaign-analyst"
  description: "Analyze competitor content strategy for: $COMPETITOR"
  prompt: |
    Read `.competitor-intel/profile.md` for competitor context.
    Read `.agents/marketing-context.md` for our content context.

    Analyze their content strategy:
    1. **Content inventory**: Blog, guides, case studies, video — volume and frequency
    2. **Topic clusters**: What themes they own, what they're investing in
    3. **Content quality**: Depth, originality, production value assessment
    4. **Distribution**: Where they promote content, social engagement levels
    5. **Lead magnets**: Free tools, templates, calculators, gated content
    6. **Content gaps**: Topics they haven't covered that their audience needs

    Write output to `.competitor-intel/content-analysis.md`

### Step 3: SEO & Visibility Analysis

Task:
  subagent_type: "mkt-seo-specialist"
  description: "Analyze competitor SEO for: $COMPETITOR"
  prompt: |
    Read `.competitor-intel/profile.md` for competitor context.
    Read `.agents/marketing-context.md` for our SEO context.

    Analyze their search presence:
    1. **Keyword overlap**: Terms we both target, terms only they rank for
    2. **Content structure**: How they organize for SEO (silos, clusters, flat)
    3. **Technical SEO signals**: Site speed, schema usage, mobile experience
    4. **Backlink profile indicators**: Types of sites linking to them
    5. **AI search presence**: How they appear in AI-generated answers
    6. **Vulnerability opportunities**: Where their SEO is weak and we can win

    Append to `.competitor-intel/content-analysis.md`

Update `state.json`: `"phase": "content_complete"`

---

## Phase 3: Positioning & Pricing (Step 4)

### Step 4: Positioning Map

Task:
  subagent_type: "mkt-marketing-strategist"
  description: "Map competitive positioning for: $COMPETITOR vs us"
  prompt: |
    Read `.competitor-intel/profile.md` for competitor data.
    Read `.competitor-intel/content-analysis.md` for content/SEO comparison.
    Read `.agents/marketing-context.md` for our positioning.

    Create a positioning analysis:
    1. **Positioning map**: 2x2 matrix on the most relevant dimensions
    2. **Pricing comparison**: Tier-by-tier feature and price comparison
    3. **Messaging comparison**: Side-by-side value prop analysis
    4. **Audience overlap**: Where we compete directly vs serve different segments
    5. **Perception gaps**: How customers likely perceive us vs them
    6. **Positioning opportunities**: Unoccupied positions we could claim

    Write output to `.competitor-intel/positioning-map.md`

Update `state.json`: `"phase": "positioning_complete"`

---

## PHASE CHECKPOINT 2 — User Approval Required

Present content analysis and positioning map, then ask:
1. **Approve** — proceed to gap analysis and action plan
2. **Deep dive** — explore specific positioning angle further
3. **Pause** — save state and resume later

---

## Phase 4: Gap Analysis & Action Plan (Step 5)

### Step 5: Strategic Recommendations

Task:
  subagent_type: "mkt-marketing-strategist"
  description: "Generate strategic recommendations from competitive analysis"
  prompt: |
    Read all files in `.competitor-intel/` for the full analysis.
    Read `.agents/marketing-context.md` for our current state.

    Synthesize into actionable strategy:
    1. **Win themes**: Where we already beat them — double down
    2. **Catch-up items**: Where they're ahead and we must close the gap
    3. **Flanking opportunities**: Angles they've ignored that we can own
    4. **Content battleground**: Specific keywords and topics to contest
    5. **Messaging ammunition**: Claims we can make that they can't
    6. **90-day roadmap**: Prioritized actions to improve competitive position

    Write output to `.competitor-intel/action-plan.md`

---

## PHASE CHECKPOINT 3 — Final Review

Present the complete competitive intelligence package and ask:
1. **Ship it** — use findings to inform strategy
2. **Expand scope** — add more competitors to the analysis
3. **Archive** — save as competitive baseline for future comparison

## Completion

Update `state.json`: `"phase": "delivered", "completed_at": "$TIMESTAMP"`
Summarize all deliverables with file locations.
