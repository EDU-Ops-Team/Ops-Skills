# Skeleton-to-Spec: Deep Planning Protocol (Track B)

## Purpose

Transform an approved approach from Track A into a zero-context implementation specification that any worker — human or agent — can execute without prior knowledge.

**When this runs**: Tier 4 tasks automatically, Tier 3 on request, or any tier when user requests "full spec."

**Research grounding**: Implements Anthropic's Evaluator-Optimizer pattern with context-isolated verification (CoVe), extended with intent-based feedback filtering to prevent enterprise bloat.

---

## Phase 5: Skeleton Draft

**Input**: Approved approach from Phase 4 + all intelligence reports from Phase 1

**Process**:
1. Expand the approved approach into a full architectural skeleton
2. Include: component list, data flow, integration points, API contracts, data models
3. Mark explicit decision points with "[DECISION: ...]" tags
4. Mark assumptions with "[ASSUMPTION: ...]" tags — these become verification targets
5. Keep it deliberately incomplete — this is a draft for critique, not a final spec

**Output structure**:
```markdown
## Skeleton: [Approach Name]

### Architecture
[Component diagram, data flow, integration points]

### Components
[For each: name, purpose, inputs, outputs, dependencies]

### Data Model
[Schemas, collections, tables]

### Integration Points
[How this connects to existing systems]

### Open Decisions
[DECISION: ...] tags with options and trade-offs

### Assumptions
[ASSUMPTION: ...] tags to be verified by reviewers
```

---

## Phase 6: Multi-Perspective Review

**Spawn 2-3 reviewer agents in parallel**, each with a different lens:

### Reviewer 1: Technical Architect
- Does the architecture hold together?
- Are there structural weaknesses?
- Are the component boundaries clean?
- Can each component be built and tested independently?

### Reviewer 2: Devil's Advocate
- What assumptions are wrong?
- What will break in production?
- What edge cases aren't covered?
- What's the weakest link in the chain?

### Reviewer 3: Solo Dev Proxy (Tier 4 only)
- Does this match what the user actually wants?
- Is anything over-engineered for a solo developer?
- Are there simpler alternatives the architect missed?
- What would the user push back on?

**Critical rule**: Reviewers MUST INFER what the user wants but hasn't said. Read between the lines of the original request. Check decision patterns from lessons.md and patterns.md.

**Each reviewer returns structured feedback**:
```
{
  "strengths": [...],
  "concerns": [
    {
      "issue": "description",
      "severity": "blocker|warning|suggestion",
      "suggested_fix": "concrete alternative"
    }
  ],
  "missing": [...],
  "assumptions_challenged": [...]
}
```

Use `agents/skeleton-reviewer.md` as the agent prompt template.

---

## Phase 7: Intent Filter

**Purpose**: Filter every piece of reviewer feedback through one question: "Does this serve the user's ORIGINAL INTENT?"

This is the critical gate that prevents enterprise bloat. Multi-agent reviewers naturally suggest patterns appropriate for large teams. The intent filter strips those out.

**Process**:
1. Read the user's original request (verbatim, never paraphrased)
2. For each piece of reviewer feedback, ask:
   - Does this directly serve the stated need?
   - Would the user approve this addition/change?
   - Is this an enterprise pattern being suggested for a one-person system?
   - Does this add complexity without proportional value?
3. Categorize each as: **ACCEPT** (with reason) | **REJECT** (with reason) | **FLAG** (needs user input)

**Never reject feedback about**: security, data integrity, error handling, or correctness.
**Always reject feedback about**: team coordination, enterprise governance, unnecessary abstraction layers, premature optimization.

Use `agents/intent-filter.md` as the agent prompt.

---

## Phase 8: Refinement + Second Review

### 8a: Incorporate Accepted Feedback

Update the skeleton with all ACCEPTED feedback. For each change:
- Note what changed and why (traceability)
- Resolve any [DECISION] tags that reviewers clarified
- Update [ASSUMPTION] tags based on reviewer challenges

### 8b: Second Review (Fresh Eyes)

Spawn a SINGLE reviewer agent (different perspective from Phase 6) to check:
- **Regression**: Does the refined skeleton still solve the original problem?
- **Bloat**: Has accepted feedback made this more complex than necessary?
- **Coherence**: Do the incorporated changes fit together, or do they conflict?

If the second reviewer finds regression or bloat → flag specific issues and trim. Do NOT iterate more than once — the goal is refinement, not perfection.

---

## Phase 9: Examples + Zero-Context Spec Assembly

### 9a: Good and Bad Examples

For each key interaction or output the system produces:

**GOOD example**: What success looks like — concrete, specific, realistic
**BAD example**: What failure looks like — the specific anti-patterns to avoid

Examples must cover:
- The primary use case (happy path)
- The most common error case
- The most subtle edge case

### 9b: Zero-Context Spec Assembly

Assemble the final document. A worker with NO prior context must understand:
- **WHAT** to build (architecture + components)
- **WHY** the user wants it (original request + interpreted intent)
- **HOW** it should behave (data flow + integration points)
- **What GOOD looks like** (examples from 9a)
- **What BAD looks like** (anti-patterns from 9a)

**Document structure**:
```markdown
# [Project Name] — Implementation Specification

## User's Original Request
[Verbatim — never paraphrased]

## What This Is
[1-2 paragraphs: what is being built and why]

## Architecture
[Full design from refined skeleton]

## Component Specifications
[For each: name, purpose, I/O, implementation guidance]

## Data Model
[Schemas with field descriptions]

## Integration Points
[How this connects to existing systems]

## Good Output Examples
[From Phase 9a]

## Bad Output Examples
[From Phase 9a]

## Definition of Done
[Checklist: what "complete" means for this task]

## Key Decisions and Rationale
[From [DECISION] tags — what was decided and why]

## Planning Trace
[Trace ID, phases completed, key findings from each phase]
```

**Rules**:
- Self-contained. No external references required.
- User's original words appear verbatim.
- Every design decision includes the "why."
- Target length: 500-2000 words depending on complexity.
- Include planning trace for debugging and learning.
