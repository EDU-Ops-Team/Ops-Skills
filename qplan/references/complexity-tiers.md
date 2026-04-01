# Complexity Tier Assessment Framework

## Purpose

Right-size planning effort to task complexity. Over-planning simple tasks wastes tokens. Under-planning complex tasks produces shallow specs that fail in implementation.

## Tier Definitions

### TIER 1 — ATOMIC
Single, well-defined action. No ambiguity. No dependencies.

**Signals**: mentions a specific file, specific bug, specific small change
**Examples**: "Fix the typo in README", "Update the version number", "Run the tests"
**Planning depth**: None. Skip qplan entirely — just execute.

### TIER 2 — STANDARD
Single deliverable, clear scope, might need light design.

**Signals**: one feature/tool, bounded scope, familiar patterns
**Examples**: "Build a todo extractor", "Add email ingestion", "Fix the auth bug"
**Planning depth**: Track A only (intelligence → 3 approaches → CoVe → recommendation)

### TIER 3 — COMPLEX
Multiple components, cross-cutting concerns, needs architectural thinking.

**Signals**: multiple modules needed, integration points, new patterns, 3+ files affected
**Examples**: "Build a zoning compliance checker", "Refactor the search system"
**Planning depth**: Track A + Track B (full Skeleton-to-Spec produces zero-context implementation spec)

### TIER 4 — MAJOR
System-level work, many unknowns, significant architectural decisions.

**Signals**: vague scope, many components, needs research, affects multiple systems
**Examples**: "Build a real estate analysis platform", "Redesign the data pipeline"
**Planning depth**: Track A + Track B with expanded review (3+ reviewer perspectives, mandatory second review)

## Decision Rules

1. If the user mentions a SPECIFIC file or line → **Tier 1** (skip qplan)
2. If the user describes ONE feature with clear boundaries → **Tier 2**
3. If the task requires 3+ NEW modules or touches 3+ existing systems → **Tier 3**
4. If the task is vague, large, or user says "build me a system/platform" → **Tier 4**
5. When in doubt, go ONE tier higher (better to over-plan than under-plan)

## Borderline Cases (Grey Zone Between Tiers)

These are the hard calls. Use the reasoning below as precedent:

| Task | Tier | Why |
|---|---|---|
| "Add OAuth to existing auth" | 3 | Touches auth module, user model, session management, callback routes, env config — 5+ files, cross-cutting |
| "Build a notification system" (existing app) | 3 | New module but integrates with existing data + delivery channels |
| "Build a notification system" (greenfield) | 4 | No existing code to anchor against, many unknowns |
| "Refactor the API layer" (same endpoints) | 3 | Internal restructuring with stable interface |
| "Refactor the API layer" (schema changes) | 4 | Breaking changes propagate to clients, DB, tests |
| "Add caching to the search endpoint" | 2 | One feature, one endpoint, clear scope |
| "Add caching across all endpoints" | 3 | Cross-cutting concern touching many files |
| "Fix the auth bug" | 2 | Single deliverable even if debugging is hard |
| "Why is auth broken?" | 1 | Investigation question, not a build task — skip qplan |

**The test**: Count the number of files/systems that will change. If you can name them all in 5 seconds → Tier 2. If you need to think about it → Tier 3. If you're not sure what will change → Tier 4.

## Greenfield Projects (No Existing Code)

When the codebase is empty or brand new:
- **Skip habitat scan** in Phase 1 (no code to scan) — spawn only 3 research agents
- **CoVe adjustments**: Treat all codebase claims as "greenfield assumptions" rather than "unverifiable failures." Present scores without CoVe penalty and note: "Greenfield project — scores reflect architectural analysis, not codebase verification."
- **Tier assessment still applies**: A new project building one feature = Tier 2. A new project building a full platform = Tier 4.
- **Track B is especially valuable for greenfield**: No existing patterns to follow means the zero-context spec is the only architectural document. Bias toward Track B for greenfield Tier 2+.

## User Override

The user can always:
- Request deeper planning: "Give me the full spec" → forces Track B regardless of tier
- Request lighter planning: "Just the approaches" → stops after Track A regardless of tier
- Skip planning entirely: "Just build it" → no qplan, direct to implementation

## Output

```
Tier: [1-4]
Track: [A only | A + B | Skip]
Reasoning: [one sentence]
Key risks if under-planned: [list]
```
