---
description: "Orchestrate a product or feature launch campaign across content, channels, and analytics"
argument-hint: "<product or feature to launch> [--date YYYY-MM-DD] [--budget low|medium|high]"
---

# Launch Campaign Orchestrator

## CRITICAL BEHAVIORAL RULES

1. **Follow phase order strictly.** Never skip ahead — each phase depends on the previous.
2. **Read state files before acting.** Every step reads relevant prior outputs before starting.
3. **Checkpoint before proceeding.** Each phase ends with a user approval checkpoint.
4. **Halt on failure.** If any step fails or produces low-quality output, stop and report.
5. **Use only local agents.** All `subagent_type` references use agents from this plugin or `general-purpose`.
6. **Never enter plan mode.** This command IS the plan — execute it directly.

## Pre-flight Checks

1. Check if `.agents/marketing-context.md` exists
   - If NO → run `marketing-strategist` to create it FIRST — launch without context is reckless
   - If YES → read it and extract positioning, ICP, and brand voice
2. Parse `$ARGUMENTS` for product/feature name, launch date, and budget tier
3. Initialize state directory:

```
.launch-campaign/
├── state.json           # Phase tracking
├── strategy.md          # Phase 1 output
├── content-assets.md    # Phase 2 output
├── channel-plan.md      # Phase 3 output
├── tracking-plan.md     # Phase 4 output
└── launch-checklist.md  # Phase 5 output
```

Initialize `state.json`:
```json
{
  "product": "$PRODUCT",
  "launch_date": "$DATE",
  "budget": "$BUDGET",
  "phase": "strategy",
  "started_at": "$TIMESTAMP"
}
```

---

## Phase 1: Strategy (Step 1) — Launch Planning

### Step 1: Launch Strategy

Task:
  subagent_type: "mkt-marketing-strategist"
  description: "Develop launch strategy for: $PRODUCT"
  prompt: |
    Read `.agents/marketing-context.md` for positioning and audience context.

    Create a launch strategy for: $PRODUCT
    Launch date: $DATE
    Budget tier: $BUDGET

    Deliver:
    1. **Launch narrative**: The story we're telling and why now
    2. **Target segments**: Primary and secondary audiences with messaging per segment
    3. **Positioning**: How this fits into our broader product story
    4. **Launch phases**: Pre-launch (buzz), launch day (impact), post-launch (sustain)
    5. **Channel strategy**: Which channels for which phases, with budget allocation
    6. **Success metrics**: What numbers define success at 1 week, 1 month, 3 months
    7. **Risk mitigation**: What could go wrong and contingency plans

    Write output to `.launch-campaign/strategy.md`

Update `state.json`: `"phase": "strategy_complete"`

---

## PHASE CHECKPOINT 1 — User Approval Required

Present the launch strategy and ask:
1. **Approve** — proceed to content asset creation
2. **Adjust scope** — modify budget, timeline, or target segments
3. **Pivot** — fundamentally change the approach

---

## Phase 2: Content Assets (Steps 2-3) — Parallel

### Step 2: Launch Content Creation

Task:
  subagent_type: "mkt-content-strategist"
  description: "Create launch content assets for: $PRODUCT"
  prompt: |
    Read `.agents/marketing-context.md` for brand voice.
    Read `.launch-campaign/strategy.md` for launch narrative and messaging.

    Create these content assets:
    1. **Launch announcement**: Blog post / landing page copy
    2. **Product description**: Feature highlights, benefit statements, use cases
    3. **Social media launch posts**: 3-5 posts per platform (from strategy's channel list)
    4. **Email announcement**: For existing subscribers/customers
    5. **Press/outreach template**: For partners, influencers, or media

    Write all assets to `.launch-campaign/content-assets.md`

### Step 3: SEO Preparation

Task:
  subagent_type: "mkt-seo-specialist"
  description: "SEO-optimize launch content for: $PRODUCT"
  prompt: |
    Read `.launch-campaign/strategy.md` for target keywords and positioning.
    Read `.launch-campaign/content-assets.md` for the content to optimize.

    Prepare SEO for launch:
    1. **Target keywords**: Primary and secondary keywords for launch content
    2. **Landing page SEO**: Title, meta, headings, schema markup (JSON-LD)
    3. **Internal linking plan**: How to link launch content to existing pages
    4. **AI search optimization**: Structure content for AI search visibility
    5. **Launch-specific schema**: Product, Event, or Offer markup as appropriate

    Append SEO recommendations to `.launch-campaign/content-assets.md`

Update `state.json`: `"phase": "content_complete"`

---

## PHASE CHECKPOINT 2 — User Approval Required

Present all content assets with SEO recommendations and ask:
1. **Approve** — proceed to channel execution planning
2. **Revise content** — provide feedback on specific assets
3. **Pause** — save state and resume later

---

## Phase 3: Channel Execution (Step 4) — Campaign Setup

### Step 4: Channel Campaign Plans

Task:
  subagent_type: "mkt-growth-marketer"
  description: "Design channel execution plan for $PRODUCT launch"
  prompt: |
    Read `.agents/marketing-context.md` for audience context.
    Read `.launch-campaign/strategy.md` for channel strategy and budget.
    Read `.launch-campaign/content-assets.md` for available content.

    Create detailed execution plans per channel:

    For each channel in the strategy:
    1. **Timeline**: Day-by-day schedule for pre-launch, launch, post-launch
    2. **Content mapping**: Which asset goes where and when
    3. **Audience targeting**: Segments, lookalikes, retargeting setup
    4. **Budget allocation**: Per-channel daily/weekly spend
    5. **Ad creative specs**: If paid — headlines, descriptions, image/video specs
    6. **Email sequences**: If email — trigger events, send times, segments

    Write output to `.launch-campaign/channel-plan.md`

Update `state.json`: `"phase": "channels_complete"`

---

## Phase 4: Analytics & Tracking (Step 5)

### Step 5: Tracking & Measurement Setup

Task:
  subagent_type: "mkt-campaign-analyst"
  description: "Design analytics tracking for $PRODUCT launch"
  prompt: |
    Read `.launch-campaign/strategy.md` for success metrics.
    Read `.launch-campaign/channel-plan.md` for channels being used.

    Design the measurement infrastructure:
    1. **Event tracking plan**: Events to track, properties, naming convention
    2. **UTM strategy**: UTM parameters per channel and campaign
    3. **Conversion goals**: Primary and secondary conversion definitions
    4. **Attribution setup**: Model recommendation and implementation notes
    5. **Dashboard specification**: Key metrics, refresh frequency, alert thresholds
    6. **Launch day monitoring**: Real-time metrics to watch, escalation triggers

    Write output to `.launch-campaign/tracking-plan.md`

Update `state.json`: `"phase": "tracking_complete"`

---

## PHASE CHECKPOINT 3 — Final Review

### Step 6: Launch Checklist

Synthesize all outputs into a single launch checklist:

Read all files in `.launch-campaign/` and create:
1. **T-7 days**: Pre-launch tasks (content ready, tracking live, emails scheduled)
2. **T-1 day**: Final checks (links work, tracking fires, team briefed)
3. **Launch day**: Hour-by-hour execution plan
4. **T+1 to T+7**: Post-launch monitoring and follow-up tasks
5. **T+30**: First month review — what to measure and decide

Write to `.launch-campaign/launch-checklist.md`

Present the complete launch package and ask:
1. **Ship it** — all assets and plans approved
2. **Revise section** — re-run specific phase
3. **Archive** — save for a later launch date

## Completion

Update `state.json`: `"phase": "delivered", "completed_at": "$TIMESTAMP"`
Summarize all deliverables with file locations.
