---
description: "Design and build email campaigns — cold outreach sequences, nurture flows, or lifecycle automation"
argument-hint: "<campaign goal> [--type cold|nurture|lifecycle|announcement] [--segments prospects|users|churned]"
---

# Email Campaign Orchestrator

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
   - If YES → read it and extract ICP, brand voice, and current email infrastructure
2. Parse `$ARGUMENTS` for campaign goal, type, and target segments
3. Initialize state directory:

```
.email-campaign/
├── state.json            # Phase tracking
├── strategy.md           # Phase 1 output
├── sequences.md          # Phase 2 output
├── copy.md               # Phase 3 output
├── tracking.md           # Phase 4 output
└── launch-plan.md        # Phase 5 output
```

Initialize `state.json`:
```json
{
  "goal": "$GOAL",
  "type": "$TYPE",
  "segments": "$SEGMENTS",
  "phase": "strategy",
  "started_at": "$TIMESTAMP"
}
```

---

## Phase 1: Strategy (Step 1) — Campaign Architecture

### Step 1: Email Strategy

Task:
  subagent_type: "mkt-growth-marketer"
  description: "Design email campaign strategy for: $GOAL"
  prompt: |
    Read `.agents/marketing-context.md` for audience and brand context.

    Design an email campaign strategy for: $GOAL
    Campaign type: $TYPE
    Target segments: $SEGMENTS

    Deliver:
    1. **Campaign objective**: Specific, measurable outcome we're driving toward
    2. **Audience segmentation**: Detailed segment definitions with entry/exit criteria
    3. **Sequence architecture**: Number of emails, timing cadence, trigger events
    4. **Channel integration**: How email connects to other touchpoints (site, ads, social)
    5. **Personalization plan**: Dynamic fields, conditional blocks, segment-specific content
    6. **Deliverability considerations**: Warm-up needs, send volume, domain reputation

    Write output to `.email-campaign/strategy.md`

Update `state.json`: `"phase": "strategy_complete"`

---

## PHASE CHECKPOINT 1 — User Approval Required

Present the strategy and ask:
1. **Approve** — proceed to sequence design
2. **Adjust segments** — refine targeting criteria
3. **Change cadence** — modify timing or sequence length

---

## Phase 2: Sequence Design (Step 2) — Flow Architecture

### Step 2: Sequence Building

Task:
  subagent_type: "mkt-growth-marketer"
  description: "Build email sequences for: $GOAL"
  prompt: |
    Read `.agents/marketing-context.md` for brand context.
    Read `.email-campaign/strategy.md` for campaign architecture.

    Build detailed email sequences:

    For each sequence in the strategy:
    1. **Email-by-email breakdown**: Subject, purpose, CTA, send timing
    2. **Trigger conditions**: What event or delay triggers each email
    3. **Branch logic**: If/then rules (opened vs didn't, clicked vs didn't, replied vs didn't)
    4. **Exit conditions**: When to stop the sequence (converted, unsubscribed, replied)
    5. **A/B test slots**: Which emails to test and what to vary
    6. **Fallback paths**: What happens if the primary sequence doesn't convert

    Write output to `.email-campaign/sequences.md`

Update `state.json`: `"phase": "sequences_complete"`

---

## Phase 3: Copywriting (Steps 3-4) — Parallel

### Step 3: Email Copy

Task:
  subagent_type: "mkt-content-strategist"
  description: "Write email copy for: $GOAL campaign"
  prompt: |
    Read `.agents/marketing-context.md` for brand voice.
    Read `.email-campaign/strategy.md` for campaign context.
    Read `.email-campaign/sequences.md` for sequence structure.

    Write full copy for every email in every sequence:
    1. **Subject lines**: 2-3 options per email (aim for curiosity, specificity, or urgency)
    2. **Preview text**: Complements the subject, not repeats it
    3. **Body copy**: Conversational, scannable, one CTA per email
    4. **CTA text**: Specific action-oriented button/link copy
    5. **PS lines**: Where appropriate (cold outreach especially)

    Write output to `.email-campaign/copy.md`

### Step 4: Copy Humanization

Task:
  subagent_type: "mkt-content-strategist"
  description: "Humanize and polish email copy"
  prompt: |
    Read `.email-campaign/copy.md`
    Read `.agents/marketing-context.md` for brand voice reference.

    Review and humanize all email copy:
    1. Remove AI-sounding patterns (hedging, filler phrases, generic openings)
    2. Add personality — read like a real person wrote it
    3. Check cold emails don't sound salesy — value-first, not pitch-first
    4. Ensure each email earns the right to send the next one
    5. Verify reply-friendly tone (especially for cold outreach)

    Update `.email-campaign/copy.md` with the polished versions.

Update `state.json`: `"phase": "copy_complete"`

---

## PHASE CHECKPOINT 2 — User Approval Required

Present all email copy and ask:
1. **Approve** — proceed to tracking setup
2. **Revise specific emails** — point out which need work
3. **Pause** — save state and resume later

---

## Phase 4: Tracking & Launch (Step 5)

### Step 5: Measurement & Launch Plan

Task:
  subagent_type: "mkt-campaign-analyst"
  description: "Design tracking and launch plan for email campaign"
  prompt: |
    Read `.email-campaign/strategy.md` for objectives.
    Read `.email-campaign/sequences.md` for sequence structure.

    Create tracking and launch plan:
    1. **KPIs per sequence**: Open rate, click rate, reply rate, conversion rate targets
    2. **UTM parameters**: Per email, per sequence
    3. **A/B test measurement**: Statistical significance thresholds, test duration
    4. **Reporting cadence**: Daily/weekly metrics, what triggers intervention
    5. **Launch checklist**: DNS records, warm-up schedule, suppression lists, test sends
    6. **Iteration triggers**: When to pause, when to optimize, when to kill a sequence

    Write output to `.email-campaign/tracking.md`

Synthesize a launch plan combining all phases → `.email-campaign/launch-plan.md`

Update `state.json`: `"phase": "complete"`

---

## PHASE CHECKPOINT 3 — Final Review

Present the complete email campaign package and ask:
1. **Ship it** — all sequences, copy, and tracking approved
2. **Revise section** — re-run specific phase
3. **Archive** — save for later launch

## Completion

Update `state.json`: `"phase": "delivered", "completed_at": "$TIMESTAMP"`
Summarize all deliverables with file locations.
