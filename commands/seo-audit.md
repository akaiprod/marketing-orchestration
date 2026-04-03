---
description: "Run a comprehensive SEO audit covering technical SEO, content optimization, AI search readiness, and schema markup"
argument-hint: "<url or site section> [--focus technical|content|ai-seo|full] [--depth quick|deep]"
---

# SEO Audit Orchestrator

## CRITICAL BEHAVIORAL RULES

1. **Follow phase order strictly.** Never skip ahead — each phase depends on the previous.
2. **Read state files before acting.** Every step reads relevant prior outputs before starting.
3. **Checkpoint before proceeding.** Each phase ends with a user approval checkpoint.
4. **Halt on failure.** If any step fails or produces low-quality output, stop and report.
5. **Use only local agents.** All `subagent_type` references use agents from this plugin or `general-purpose`.
6. **Never enter plan mode.** This command IS the plan — execute it directly.

## Pre-flight Checks

1. Check if `.agents/marketing-context.md` exists
   - If NO → note this, proceed with limited context
   - If YES → read it and extract SEO goals, target keywords, and competitor info
2. Parse `$ARGUMENTS` for target URL/section, focus area, and depth
3. Initialize state directory:

```
.seo-audit/
├── state.json           # Phase tracking
├── technical-audit.md   # Phase 1 output
├── content-audit.md     # Phase 2 output
├── ai-seo-review.md     # Phase 3 output
├── schema-plan.md       # Phase 3 output
└── action-plan.md       # Phase 4 output
```

Initialize `state.json`:
```json
{
  "target": "$URL_OR_SECTION",
  "focus": "$FOCUS",
  "depth": "$DEPTH",
  "phase": "technical",
  "started_at": "$TIMESTAMP"
}
```

---

## Phase 1: Technical Audit (Step 1)

### Step 1: Technical SEO Analysis

Task:
  subagent_type: "mkt-seo-specialist"
  description: "Technical SEO audit for: $URL_OR_SECTION"
  prompt: |
    Read `.agents/marketing-context.md` if it exists.

    Perform a technical SEO audit for: $URL_OR_SECTION

    Analyze and report on:
    1. **Crawlability**: robots.txt, XML sitemap, crawl errors
    2. **Indexation**: Index status, canonical tags, noindex directives
    3. **Core Web Vitals**: LCP, INP, CLS assessment
    4. **Mobile-friendliness**: Responsive design, touch targets, viewport
    5. **URL structure**: Hierarchy, slugs, parameters, trailing slashes
    6. **Internal linking**: Link depth, orphan pages, link equity distribution
    7. **Page speed**: Asset optimization, render-blocking resources, compression

    Severity rating per issue: CRITICAL / HIGH / MEDIUM / LOW
    Write output to `.seo-audit/technical-audit.md`

Update `state.json`: `"phase": "technical_complete"`

---

## PHASE CHECKPOINT 1 — User Approval Required

Present technical findings with severity breakdown and ask:
1. **Approve & continue** — proceed to content audit
2. **Fix critical issues first** — pause audit, address blockers
3. **Skip to specific phase** — jump to content, AI SEO, or schema

---

## Phase 2: Content SEO Audit (Step 2)

### Step 2: Content Optimization Analysis

Task:
  subagent_type: "mkt-seo-specialist"
  description: "Content SEO audit for: $URL_OR_SECTION"
  prompt: |
    Read `.agents/marketing-context.md` if it exists.
    Read `.seo-audit/technical-audit.md` for technical context.

    Perform a content SEO audit:
    1. **Keyword coverage**: Target keywords vs. current rankings, gaps
    2. **Content quality**: Thin pages, duplicate content, freshness
    3. **On-page optimization**: Title tags, meta descriptions, headings, image alts
    4. **Topic authority**: Pillar-cluster coverage, topical completeness
    5. **User intent alignment**: Search intent match per key page
    6. **Content cannibalization**: Pages competing for same keywords
    7. **Competitor content gaps**: Topics competitors rank for that we don't cover

    Priority ranking per opportunity: QUICK WIN / HIGH IMPACT / LONG TERM
    Write output to `.seo-audit/content-audit.md`

Update `state.json`: `"phase": "content_complete"`

---

## Phase 3: AI Search & Schema (Steps 3-4) — Parallel

### Step 3: AI Search Optimization Review

Task:
  subagent_type: "mkt-seo-specialist"
  description: "AI search optimization review"
  prompt: |
    Read `.agents/marketing-context.md` if it exists.
    Read `.seo-audit/content-audit.md` for content context.

    Assess AI search readiness:
    1. **AEO (Answer Engine Optimization)**: Featured snippet readiness, question targeting
    2. **GEO (Generative Engine Optimization)**: Content structure for AI synthesis
    3. **LLMO (LLM Optimization)**: Brand mention likelihood in AI responses
    4. **Entity strategy**: Knowledge panel potential, entity disambiguation
    5. **Structured content**: FAQ sections, how-to formats, comparison tables

    Write output to `.seo-audit/ai-seo-review.md`

### Step 4: Schema Markup Plan

Task:
  subagent_type: "mkt-seo-specialist"
  description: "Schema markup audit and implementation plan"
  prompt: |
    Read `.seo-audit/technical-audit.md` for existing markup status.
    Read `.seo-audit/content-audit.md` for page types.

    Create a schema markup plan:
    1. **Current state**: Existing schema, validation errors, missing types
    2. **Recommended schemas**: Per page type (Organization, Product, FAQ, HowTo, Article, BreadcrumbList, LocalBusiness)
    3. **JSON-LD templates**: Ready-to-implement code for each schema type
    4. **Rich result targets**: Which schemas unlock which SERP features
    5. **Priority order**: Implement highest-impact schemas first

    Write output to `.seo-audit/schema-plan.md`

Update `state.json`: `"phase": "ai_schema_complete"`

---

## PHASE CHECKPOINT 2 — User Approval Required

Present AI SEO findings and schema plan, then ask:
1. **Approve** — proceed to action plan synthesis
2. **Deep dive** — expand on a specific area
3. **Pause** — save state and resume later

---

## Phase 4: Action Plan (Step 5) — Synthesis

### Step 5: Prioritized Action Plan

Task:
  subagent_type: "mkt-marketing-strategist"
  description: "Synthesize SEO audit into prioritized action plan"
  prompt: |
    Read all audit outputs:
    - `.seo-audit/technical-audit.md`
    - `.seo-audit/content-audit.md`
    - `.seo-audit/ai-seo-review.md`
    - `.seo-audit/schema-plan.md`

    Synthesize into a single prioritized action plan:
    1. **Critical fixes** (do immediately): Technical blockers, indexation issues
    2. **Quick wins** (this week): Low-effort, high-impact optimizations
    3. **High impact** (this month): Content gaps, schema implementation
    4. **Long term** (this quarter): Authority building, AI SEO, programmatic pages

    For each action item include:
    - What to do (specific and actionable)
    - Expected impact (traffic, rankings, visibility)
    - Effort estimate (hours)
    - Dependencies

    Write output to `.seo-audit/action-plan.md`

Update `state.json`: `"phase": "complete"`

---

## Completion

Present the full action plan summary with priority tiers.
Update `state.json`: `"phase": "delivered", "completed_at": "$TIMESTAMP"`
List all output files and their locations.
