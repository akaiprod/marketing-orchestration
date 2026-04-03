---
description: "Audit and optimize conversion rates across landing pages, forms, signup flows, and onboarding"
argument-hint: "<page URL or flow name> [--focus signup|checkout|onboarding|all]"
---

# CRO Audit Orchestrator

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
   - If YES → read it and extract ICP, current conversion data, and product context
2. Parse `$ARGUMENTS` for target page/flow and focus area
3. Initialize state directory:

```
.cro-audit/
├── state.json              # Phase tracking
├── page-analysis.md        # Phase 1 output
├── form-optimization.md    # Phase 2 output
├── flow-redesign.md        # Phase 3 output
├── ab-test-plan.md         # Phase 4 output
└── action-plan.md          # Phase 5 output
```

Initialize `state.json`:
```json
{
  "target": "$TARGET",
  "focus": "$FOCUS",
  "phase": "analysis",
  "started_at": "$TIMESTAMP"
}
```

---

## Phase 1: Analysis (Step 1) — Page & Flow Audit

### Step 1: Landing Page / Flow Analysis

Task:
  subagent_type: "mkt-cro-analyst"
  description: "Analyze conversion performance of: $TARGET"
  prompt: |
    Read `.agents/marketing-context.md` for ICP and product context.

    Perform a conversion audit of: $TARGET
    Focus area: $FOCUS

    Deliver:
    1. **Current state assessment**: Page structure, messaging hierarchy, visual flow
    2. **Friction analysis**: Where users likely drop off and why
    3. **Copy audit**: Headline strength, value proposition clarity, CTA effectiveness
    4. **Trust signals**: Social proof, guarantees, credibility indicators — what's missing
    5. **Mobile experience**: Responsive issues, tap targets, form usability on mobile
    6. **Page speed impact**: Above-the-fold content load, perceived performance

    Write output to `.cro-audit/page-analysis.md`

Update `state.json`: `"phase": "analysis_complete"`

---

## PHASE CHECKPOINT 1 — User Approval Required

Present the analysis and ask:
1. **Approve** — proceed to form and flow optimization
2. **Add data** — user provides analytics data to sharpen recommendations
3. **Narrow focus** — concentrate on specific friction points

---

## Phase 2: Form & Element Optimization (Step 2)

### Step 2: Form & CTA Optimization

Task:
  subagent_type: "mkt-cro-analyst"
  description: "Optimize forms and CTAs for: $TARGET"
  prompt: |
    Read `.cro-audit/page-analysis.md` for friction points identified.
    Read `.agents/marketing-context.md` for ICP context.

    Optimize conversion elements:
    1. **Form redesign**: Field reduction, progressive disclosure, smart defaults
    2. **CTA optimization**: Copy variants, placement, visual hierarchy, urgency/scarcity
    3. **Popup/modal strategy**: Timing, trigger conditions, exit-intent recommendations
    4. **Micro-copy**: Error messages, helper text, confirmation messages
    5. **Social proof placement**: Where and what type (testimonials, numbers, logos)
    6. **Objection handling**: Pre-emptive answers to buying hesitations

    Write output to `.cro-audit/form-optimization.md`

Update `state.json`: `"phase": "forms_complete"`

---

## Phase 3: Flow Redesign (Step 3)

### Step 3: User Flow Optimization

Task:
  subagent_type: "mkt-cro-analyst"
  description: "Redesign user flow for: $TARGET"
  prompt: |
    Read `.cro-audit/page-analysis.md` for current state.
    Read `.cro-audit/form-optimization.md` for element-level fixes.
    Read `.agents/marketing-context.md` for ICP and product context.

    Design the optimized user flow:
    1. **Signup/checkout flow**: Step reduction, progress indicators, save state
    2. **Onboarding sequence**: First-run experience, activation milestones, time-to-value
    3. **Paywall/pricing page**: Tier presentation, anchor pricing, feature comparison
    4. **Error recovery**: What happens when things go wrong (payment fails, validation errors)
    5. **Cross-sell/upsell points**: Natural moments to expand value

    Write output to `.cro-audit/flow-redesign.md`

Update `state.json`: `"phase": "flow_complete"`

---

## PHASE CHECKPOINT 2 — User Approval Required

Present form optimizations and flow redesign, then ask:
1. **Approve** — proceed to A/B test planning
2. **Revise** — adjust specific recommendations
3. **Pause** — save state and resume later

---

## Phase 4: A/B Test Plan (Step 4)

### Step 4: Experiment Design

Task:
  subagent_type: "mkt-campaign-analyst"
  description: "Design A/B tests for CRO improvements on: $TARGET"
  prompt: |
    Read all files in `.cro-audit/` for the full set of recommendations.

    Design a prioritized A/B testing plan:
    1. **Test prioritization**: ICE score (Impact × Confidence × Ease) for each change
    2. **Test definitions**: For top 5 tests — hypothesis, control, variant, primary metric
    3. **Sample size estimates**: Required traffic per test for statistical significance
    4. **Test sequence**: Which tests to run first, which can run in parallel
    5. **Measurement plan**: Events to track, success criteria, guardrail metrics
    6. **Implementation notes**: Technical complexity and dependencies per test

    Write output to `.cro-audit/ab-test-plan.md`

Update `state.json`: `"phase": "testing_complete"`

---

## Phase 5: Action Plan (Step 5)

### Step 5: Consolidated Action Plan

Synthesize all outputs into a prioritized action plan:

Read all files in `.cro-audit/` and create:
1. **Quick wins** (implement this week): Changes with high confidence, low effort
2. **Medium-term** (2-4 weeks): A/B tests to run and flow changes to implement
3. **Strategic** (1-3 months): Larger redesigns and infrastructure changes
4. **Measurement cadence**: When to check metrics and what to look for

Write to `.cro-audit/action-plan.md`

Present the complete CRO package and ask:
1. **Ship it** — start implementing quick wins
2. **Revise section** — re-run specific phase
3. **Archive** — save for reference

## Completion

Update `state.json`: `"phase": "delivered", "completed_at": "$TIMESTAMP"`
Summarize all deliverables with file locations.
