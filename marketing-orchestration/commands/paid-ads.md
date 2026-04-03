---
description: "Plan and build paid advertising campaigns across Google, Meta, LinkedIn, and other platforms"
argument-hint: "<campaign objective> [--platform google|meta|linkedin|tiktok|all] [--budget amount] [--duration weeks]"
---

# Paid Ads Orchestrator

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
   - If YES → read it and extract ICP, positioning, and any existing ad performance data
2. Parse `$ARGUMENTS` for campaign objective, platform(s), budget, and duration
3. Initialize state directory:

```
.paid-ads/
├── state.json             # Phase tracking
├── strategy.md            # Phase 1 output
├── targeting.md           # Phase 2 output
├── creatives.md           # Phase 3 output
├── tracking.md            # Phase 4 output
└── launch-plan.md         # Phase 5 output
```

Initialize `state.json`:
```json
{
  "objective": "$OBJECTIVE",
  "platforms": "$PLATFORMS",
  "budget": "$BUDGET",
  "duration_weeks": "$DURATION",
  "phase": "strategy",
  "started_at": "$TIMESTAMP"
}
```

---

## Phase 1: Strategy (Step 1) — Campaign Architecture

### Step 1: Paid Media Strategy

Task:
  subagent_type: "mkt-growth-marketer"
  description: "Design paid ads strategy for: $OBJECTIVE"
  prompt: |
    Read `.agents/marketing-context.md` for audience and positioning context.

    Design a paid advertising strategy for: $OBJECTIVE
    Platform(s): $PLATFORMS
    Budget: $BUDGET over $DURATION weeks

    Deliver:
    1. **Campaign structure**: Account → Campaign → Ad group hierarchy
    2. **Objective mapping**: Platform objectives aligned to business goal (awareness/traffic/conversions)
    3. **Budget allocation**: Per platform, per campaign, daily spend caps
    4. **Funnel strategy**: TOFU/MOFU/BOFU campaigns and how they connect
    5. **Bidding strategy**: Manual vs automated, target CPA/ROAS recommendations
    6. **Timeline**: Launch phases — test (week 1-2), optimize (week 3-4), scale (week 5+)

    Write output to `.paid-ads/strategy.md`

Update `state.json`: `"phase": "strategy_complete"`

---

## PHASE CHECKPOINT 1 — User Approval Required

Present the strategy and ask:
1. **Approve** — proceed to audience targeting
2. **Adjust budget** — reallocate across platforms or campaigns
3. **Change objective** — shift focus (e.g., from conversions to awareness)

---

## Phase 2: Targeting (Step 2) — Audience Architecture

### Step 2: Audience Targeting

Task:
  subagent_type: "mkt-growth-marketer"
  description: "Design audience targeting for: $OBJECTIVE ads"
  prompt: |
    Read `.agents/marketing-context.md` for ICP definitions.
    Read `.paid-ads/strategy.md` for campaign structure.

    Build audience targeting per platform:

    For each platform in the strategy:
    1. **Core audiences**: Demographics, interests, behaviors — specific targeting parameters
    2. **Custom audiences**: Website visitors, email lists, app users — retargeting segments
    3. **Lookalike/similar audiences**: Source audiences and similarity percentages
    4. **Exclusions**: Who to exclude (existing customers, employees, irrelevant segments)
    5. **Audience layering**: How to combine targeting for precision
    6. **Testing plan**: Which audiences to test against each other

    Write output to `.paid-ads/targeting.md`

Update `state.json`: `"phase": "targeting_complete"`

---

## Phase 3: Creative Development (Steps 3-4) — Parallel

### Step 3: Ad Creative — Copy

Task:
  subagent_type: "mkt-content-strategist"
  description: "Write ad copy for: $OBJECTIVE campaign"
  prompt: |
    Read `.agents/marketing-context.md` for brand voice.
    Read `.paid-ads/strategy.md` for campaign structure and objectives.
    Read `.paid-ads/targeting.md` for audience segments.

    Write ad copy for each campaign and platform:
    1. **Headlines**: 5-10 options per ad group (platform char limits respected)
    2. **Descriptions**: 3-5 options per ad group
    3. **CTAs**: Platform-appropriate call-to-action text
    4. **Ad extensions**: Sitelinks, callouts, structured snippets (Google)
    5. **Primary text**: For social platforms — hook + value + CTA
    6. **Landing page recommendations**: Key elements the LP must have per campaign

    Write output to `.paid-ads/creatives.md`

### Step 4: Ad Creative — Visual Direction

Task:
  subagent_type: "mkt-content-strategist"
  description: "Define visual direction for ad creatives"
  prompt: |
    Read `.agents/marketing-context.md` for brand guidelines.
    Read `.paid-ads/strategy.md` for funnel position of each campaign.
    Read `.paid-ads/creatives.md` for the copy to pair with visuals.

    Define visual creative direction:
    1. **Format specs**: Image sizes, video lengths per platform
    2. **Visual concepts**: 3-5 creative concepts per campaign (describe what the image/video shows)
    3. **Copy overlay**: How text appears on creative (headline placement, CTA button)
    4. **Brand consistency**: Color usage, logo placement, font guidelines
    5. **Testing matrix**: Which visual concepts to A/B test against each other
    6. **Production checklist**: What assets need to be created (with specs)

    Append to `.paid-ads/creatives.md`

Update `state.json`: `"phase": "creatives_complete"`

---

## PHASE CHECKPOINT 2 — User Approval Required

Present targeting and creative packages, then ask:
1. **Approve** — proceed to tracking setup
2. **Revise copy** — adjust specific ad copy
3. **Pause** — save state and resume later

---

## Phase 4: Tracking & Launch (Step 5)

### Step 5: Measurement & Launch Plan

Task:
  subagent_type: "mkt-campaign-analyst"
  description: "Design tracking and launch plan for paid ads"
  prompt: |
    Read `.paid-ads/strategy.md` for objectives and budget.
    Read `.paid-ads/targeting.md` for audience structure.
    Read `.paid-ads/creatives.md` for creative variants.

    Create tracking and launch plan:
    1. **Pixel/tag setup**: Platform pixels, conversion events, custom conversions
    2. **UTM strategy**: Per campaign, per ad group, per creative
    3. **Attribution model**: Recommended model and why, cross-platform attribution notes
    4. **KPI targets**: CPC, CTR, CPA, ROAS targets per campaign (with benchmarks)
    5. **Optimization schedule**: When to check, what to adjust, budget reallocation triggers
    6. **Reporting template**: Weekly report structure with key decisions to make

    Write output to `.paid-ads/tracking.md`

Synthesize all phases into a launch checklist → `.paid-ads/launch-plan.md`:
1. **Pre-launch**: Account setup, pixel verification, audience creation, creative upload
2. **Launch day**: Campaigns to activate, initial bid settings, monitoring checklist
3. **Week 1**: Daily optimization actions
4. **Week 2+**: Optimization cadence, scaling criteria, kill criteria

Present the complete paid ads package and ask:
1. **Ship it** — all targeting, creatives, and tracking approved
2. **Revise section** — re-run specific phase
3. **Archive** — save for later launch

## Completion

Update `state.json`: `"phase": "delivered", "completed_at": "$TIMESTAMP"`
Summarize all deliverables with file locations.
