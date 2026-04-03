---
description: "Design and run growth experiments — from hypothesis through A/B test design to measurement and iteration"
argument-hint: "<hypothesis or area to test> [--metric signups|revenue|activation|retention] [--urgency low|medium|high]"
---

# Growth Experiment Orchestrator

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
   - If YES → read it and extract current metrics, growth stage, and constraints
2. Parse `$ARGUMENTS` for hypothesis, target metric, and urgency
3. Initialize state directory:

```
.growth-experiment/
├── state.json              # Phase tracking
├── hypothesis.md           # Phase 1 output
├── experiment-design.md    # Phase 2 output
├── implementation.md       # Phase 3 output
├── tracking.md             # Phase 4 output
└── analysis-template.md    # Phase 5 output
```

Initialize `state.json`:
```json
{
  "hypothesis": "$HYPOTHESIS",
  "target_metric": "$METRIC",
  "urgency": "$URGENCY",
  "phase": "hypothesis",
  "started_at": "$TIMESTAMP"
}
```

---

## Phase 1: Hypothesis (Step 1) — Problem & Opportunity Framing

### Step 1: Hypothesis Development

Task:
  subagent_type: "mkt-growth-marketer"
  description: "Develop growth hypothesis for: $HYPOTHESIS"
  prompt: |
    Read `.agents/marketing-context.md` for current metrics and growth context.

    Develop a rigorous growth hypothesis for: $HYPOTHESIS
    Target metric: $METRIC

    Deliver:
    1. **Problem statement**: What's underperforming and evidence it matters
    2. **Root cause analysis**: Why we believe this is happening (data + intuition)
    3. **Hypothesis statement**: "If we [change], then [metric] will [improve] because [reason]"
    4. **Expected impact**: Quantified estimate of improvement (conservative, expected, optimistic)
    5. **Risk assessment**: What could go wrong, what we'd learn even if it fails
    6. **Alternative hypotheses**: 2-3 other explanations to test against

    Write output to `.growth-experiment/hypothesis.md`

Update `state.json`: `"phase": "hypothesis_complete"`

---

## PHASE CHECKPOINT 1 — User Approval Required

Present the hypothesis and ask:
1. **Approve** — proceed to experiment design
2. **Refine hypothesis** — sharpen the problem statement or expected impact
3. **Switch hypothesis** — pursue an alternative hypothesis instead

---

## Phase 2: Experiment Design (Step 2) — Test Architecture

### Step 2: Experiment Blueprint

Task:
  subagent_type: "mkt-campaign-analyst"
  description: "Design experiment for: $HYPOTHESIS"
  prompt: |
    Read `.agents/marketing-context.md` for constraints and traffic levels.
    Read `.growth-experiment/hypothesis.md` for the hypothesis.

    Design a rigorous experiment:
    1. **Test type**: A/B, multivariate, sequential, or holdout — justify choice
    2. **Control vs variant**: Exact description of what changes between groups
    3. **Sample size calculation**: Required n for statistical significance at 95% confidence
    4. **Duration estimate**: How long to run based on current traffic/conversion rates
    5. **Segmentation**: Run for all users or specific segments? Why?
    6. **Guardrail metrics**: What must NOT degrade (revenue, engagement, satisfaction)
    7. **Success criteria**: Exact threshold — "we ship if [metric] improves by [X]%"
    8. **Kill criteria**: When to stop early (significant negative impact, technical issues)

    Write output to `.growth-experiment/experiment-design.md`

Update `state.json`: `"phase": "design_complete"`

---

## Phase 3: Implementation (Step 3) — Build the Variant

### Step 3: Implementation Spec

Task:
  subagent_type: "mkt-cro-analyst"
  description: "Spec implementation details for growth experiment"
  prompt: |
    Read `.growth-experiment/hypothesis.md` for what we're testing.
    Read `.growth-experiment/experiment-design.md` for test architecture.

    Create implementation specifications:
    1. **UI/UX changes**: Exact copy, layout, or flow changes for the variant
    2. **Technical requirements**: Feature flags, code changes, tool configuration
    3. **Content/copy variants**: If testing messaging — exact copy for each variant
    4. **Targeting rules**: User inclusion/exclusion criteria, percentage allocation
    5. **QA checklist**: What to verify before launching (rendering, tracking, edge cases)
    6. **Rollback plan**: How to revert quickly if something breaks

    Write output to `.growth-experiment/implementation.md`

Update `state.json`: `"phase": "implementation_complete"`

---

## PHASE CHECKPOINT 2 — User Approval Required

Present experiment design and implementation spec, then ask:
1. **Approve** — proceed to tracking setup
2. **Simplify** — reduce scope of what we're testing
3. **Pause** — save and revisit when ready to implement

---

## Phase 4: Tracking (Step 4) — Measurement Infrastructure

### Step 4: Tracking & Analytics Setup

Task:
  subagent_type: "mkt-campaign-analyst"
  description: "Set up tracking for growth experiment"
  prompt: |
    Read `.growth-experiment/experiment-design.md` for metrics and success criteria.
    Read `.growth-experiment/implementation.md` for what to track.

    Design the measurement infrastructure:
    1. **Event tracking plan**: Events, properties, and naming conventions
    2. **Dashboard spec**: Real-time monitoring during experiment
    3. **Statistical analysis plan**: How to analyze results (frequentist vs Bayesian)
    4. **Reporting schedule**: When to check, when to share updates
    5. **Peeking policy**: Rules to prevent premature conclusions
    6. **Post-experiment analysis template**: What to document regardless of outcome

    Write output to `.growth-experiment/tracking.md`

Update `state.json`: `"phase": "tracking_complete"`

---

## Phase 5: Analysis Template (Step 5) — Pre-built for Results

### Step 5: Analysis Framework

Synthesize all phases into an analysis-ready template:

Read all files in `.growth-experiment/` and create:
1. **Experiment summary**: One-paragraph description for stakeholders
2. **Results template**: Pre-formatted table for filling in metrics
3. **Decision framework**: Ship / iterate / kill criteria mapped to possible outcomes
4. **Learning documentation**: What to capture regardless of win/loss
5. **Next experiment suggestions**: Follow-up tests based on possible outcomes

Write to `.growth-experiment/analysis-template.md`

Present the complete experiment package and ask:
1. **Launch it** — all specs approved, ready to implement
2. **Revise section** — re-run specific phase
3. **Archive** — save for when traffic/resources allow

## Completion

Update `state.json`: `"phase": "delivered", "completed_at": "$TIMESTAMP"`
Summarize all deliverables with file locations.
