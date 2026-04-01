---
name: qplan
argument-hint: "[task description]"
description: |
  Unified planning intelligence system with two-track architecture.
  Track A: parallel multi-agent research, 3 distinct approaches with tri-lens
  excellence scoring, and CoVe claim verification.
  Track B: Skeleton-to-Spec deep planning with multi-perspective review,
  intent-based feedback filtering, and zero-context spec assembly.

  Use when user says "qplan", "plan this", "how should I implement",
  "what's the best approach for", or describes a feature needing architectural decisions.
  Do NOT use for simple bug fixes, typo corrections, or single-file changes.

  Balance quality with pragmatic constraints. Solo developer: time and complexity matter.
  Saves 5-10k tokens per analysis by offloading research to separate context windows.
disable-model-invocation: false
metadata:
  author: Brandon Gee
  version: 3.0
---

# qplan 3.0: Unified Planning Intelligence

You are orchestrating a multi-agent planning analysis. This skill has two tracks:

- **Track A** (Strategic Analysis): Always runs. Parallel research, 3 approaches, CoVe verification.
- **Track B** (Skeleton-to-Spec): Runs for Tier 4 tasks or on user request. Produces zero-context implementation spec.

## User's Task

**Task Description**: $ARGUMENTS

If $ARGUMENTS is empty, ask user: "What feature or task should I plan?"

---

## Flag Handling

### `--lite` Mode

When $ARGUMENTS contains `--lite`, activate lite mode:
- In Phase 1, spawn only **2 research agents**: Agent 1 (Habitat Scan) and Agent 4 (Risk Research)
- Skip Agent 2 (API Research) and Agent 3 (UX Research)
- Still spawn both agents in a single message for parallel execution
- CoVe verification still runs (1 agent per approach, all in a single batch)
- Use `--lite` when you already understand the APIs and just need codebase context + risk assessment

Strip `--lite` from the task description before passing to agents.

---

## Phase 0: Complexity Assessment

Before spawning agents, assess task complexity to right-size planning effort.
Consult `references/complexity-tiers.md` for the full framework.

### Quick Assessment Rules
- User mentions a SPECIFIC file or line = **Tier 1** (skip qplan, just execute)
- ONE feature with clear boundaries = **Tier 2** (Track A only)
- 3+ modules OR 3+ existing systems touched = **Tier 3** (Track A, optionally Track B)
- Vague scope, "build me a system" = **Tier 4** (Track A + B, expanded review)
- When in doubt, go ONE tier higher

### Borderline Cases
- "Add OAuth to existing auth" = **Tier 3** (touches auth, user model, session management, callback routes)
- "Build a notification system" = **Tier 3** if adding to existing app, **Tier 4** if greenfield
- "Refactor the API layer" = **Tier 3** if endpoints stay the same, **Tier 4** if schema changes

### Greenfield Projects (No Existing Code)
If the codebase is empty or brand new:
- Skip habitat scan agent (no code to scan) — spawn only 3 agents in Phase 1 (or 1 in `--lite` mode)
- In Phase 3 CoVe, treat codebase claims as "greenfield assumptions" not "unverifiable failures"
- Tier assessment still applies — a new project can be Tier 2 (single feature) or Tier 4 (full platform)

### Output
```
Tier: [1-4]
Track: [A only | A + B | Skip]
Mode: [full | --lite]
Reasoning: [one sentence]
Key risks if under-planned: [list]
```

If **Tier 1**: Tell the user this doesn't need qplan and offer to just build it. Stop here.

---

## Phase 0.5: Configuration Readiness

Before spawning research agents, verify the project is configured to receive the planned work. This catches config drift early — before it causes implementation failures.

### For Existing Projects

Check for these files in the project root:

1. **Project CLAUDE.md** — Does it exist? Does it include build, test, and lint commands?
2. **`.claude/settings.json`** or **`.claude/settings.local.json`** — Any project-specific settings?

If project CLAUDE.md is **missing build/test/lint commands**, warn:
```
⚠ CONFIG: Project CLAUDE.md is missing [build|test|lint] commands.
  Without these, I can't self-validate during implementation.
  Recommend adding before proceeding. Want me to add them now?
```

If project CLAUDE.md **doesn't exist at all**, offer to scaffold one (use the greenfield template below).

### For Greenfield Projects

Scaffold a project CLAUDE.md using the template at `references/project-claude-template.md`. The template:
- Inherits global standards automatically (Claude Code merges `~/.claude/CLAUDE.md` with project CLAUDE.md)
- Focuses on project-specific context only: tech stack choices, build/test/lint commands, architecture decisions
- Includes compressed domain headers matching the project's detected or intended tech stack
- Stays under 80 lines (project-level supplements global, doesn't duplicate it)

**Detect the tech stack** from context:
- User's task description mentions specific technologies → use those
- Existing `package.json`, `pyproject.toml`, `Cargo.toml`, etc. → infer from those
- If unknown, ask: "What tech stack for this project? (e.g., Python/FastAPI, TypeScript/Next.js, etc.)"

Write the scaffolded CLAUDE.md to the project root. Present it to the user:
```
📋 Scaffolded project CLAUDE.md for [tech stack].
   Build: [command] | Test: [command] | Lint: [command]
   Review it below — edit anything before we proceed.
```

### Configuration Readiness Output

Append to the Phase 0 output:
```
Config: [READY | SCAFFOLDED | NEEDS ATTENTION]
  Project CLAUDE.md: [exists with commands | scaffolded | missing commands]
  Tech stack: [detected stack]
  Global alignment: [inherits ~/.claude/CLAUDE.md ✓]
```

If config is READY or SCAFFOLDED, proceed to Phase 1. If NEEDS ATTENTION, pause for user input.

---

## TRACK A: Strategic Analysis

### Phase 1: Parallel Intelligence Gathering

#### Full Mode (default)

Spawn **4 research agents in a single message** for parallel execution:

**Agent 1 — Habitat Scan** (Explore agent, model: haiku)
- Prompt: Read `agents/habitat-scan.md`, replace $TASK_DESCRIPTION with the actual task

**Agent 2 — API Research** (Plan agent)
- Prompt: Read `agents/api-research.md`, replace $TASK_DESCRIPTION with the actual task

**Agent 3 — UX Research** (Plan agent)
- Prompt: Read `agents/ux-research.md`, replace $TASK_DESCRIPTION with the actual task

**Agent 4 — Risk Research** (Plan agent)
- Prompt: Read `agents/risk-research.md`, replace $TASK_DESCRIPTION with the actual task

CRITICAL: All 4 Agent calls MUST be in a single message for parallel execution.

#### `--lite` Mode

Spawn **2 research agents in a single message** for parallel execution:

**Agent 1 — Habitat Scan** (Explore agent, model: haiku)
- Prompt: Read `agents/habitat-scan.md`, replace $TASK_DESCRIPTION with the actual task

**Agent 4 — Risk Research** (Plan agent)
- Prompt: Read `agents/risk-research.md`, replace $TASK_DESCRIPTION with the actual task

Skip Agent 2 (API Research) and Agent 3 (UX Research). Both agents MUST be in a single message for parallel execution.

---

### Phase 2: Synthesis + Three Approaches

After all agents return, synthesize using this concrete method:

**Step 1: Build a Constraints Table**
Extract every hard constraint from the research reports into a single list:
- From Habitat: existing patterns that must be followed, tech stack limitations, integration points
- From API research: rate limits, auth requirements, data format constraints (skip if `--lite`)
- From Risk research: failure modes to avoid, security requirements, performance floors
- From UX research: user expectations, accessibility requirements, interaction patterns (skip if `--lite`)

**Step 2: Identify the Decision Axes**
What are the 2-3 key architectural choices where the approaches will diverge? Examples: "build vs buy," "sync vs async," "single service vs distributed," "existing framework vs new stack." Name them explicitly.

**Step 3: Construct Approaches Along Different Axes**
Each approach makes different choices on the decision axes while satisfying ALL constraints from Step 1. If an approach violates a constraint, it must explicitly justify why.

Then construct 3 fundamentally different approaches. Consult `references/three-approaches.md` for construction patterns.

#### Approach 1: The Fortress (Battle-Tested)
- Industry-standard, proven patterns, low risk, strong community support

#### Approach 2: The Avant-Garde (Cutting-Edge)
- Modern, innovative, better DX or performance, latest tech patterns

#### Approach 3: The Synthesizer (Outside-the-Box)
- Challenges assumptions, minimalist or unconventional, paradigm-shifting

**For each approach**, apply the Tri-Lens Analysis (see `references/tri-lens-analysis.md`):
- **The Architect** (Senior Dev): Structural integrity, modularity, scalability
- **The Auditor** (Senior QA): Failure modes, edge cases, testing strategy
- **The Visionary** (Senior UX): User delight, mobile UX, accessibility

**Score each approach** using the 0-15 excellence scale (see `references/scoring-criteria.md`):
- Architectural Integrity (0-5) | Resilience & QA (0-5) | User Delight (0-5)

---

### Phase 3: CoVe Verification (Claim Fact-Checking)

The same agent that generates an approach naturally inflates its scores. Context-isolated verification catches errors that joint evaluation misses.

**Step 1: Extract Verifiable Claims** from each approach:
- Claims about the existing codebase ("integrates with X")
- Claims about external APIs ("supports batch operations")
- Claims about architecture properties ("scales to X")
- Score justifications ("5/5 because of Z")

**Step 2: Spawn Isolated Verification Agents** (one per approach, all in a single message):
- Each agent sees ONLY the claims and the code/docs — not the scoring rationale
- Each agent returns: CONFIRMED (with evidence) | REFUTED (with counter-evidence) | UNVERIFIABLE

**Step 3: Adjust Scores**:
- REFUTED claim = reduce the relevant score dimension
- UNVERIFIABLE claim = flag as uncertain, don't count toward score
- More than 30% refuted = "CAUTION: Significant unsubstantiated claims"
- More than 50% refuted = "WARNING: Approach may be based on false assumptions"

Present both **raw** (pre-verification) and **verified** (post-verification) scores.

---

### Phase 4: Recommendation + Decision Gate

1. Compare VERIFIED scores across all 3 approaches
2. Recommend the highest-scoring approach (or justify deviation)
3. Explain rationale balancing excellence with pragmatic constraints
4. Note verification confidence — approach with most confirmed claims is more trustworthy

**Ask the user 3 interrogation questions**:
1. An edge-case or logical question
2. A UX/workflow preference question
3. A future-proofing/scale question

**Decision Gate**:
- If **Tier 2**: Present recommendation. Done. Implement directly after user approves.
- If **Tier 3**: Present recommendation, then ask: "Shall I produce a full implementation spec? (optional for Tier 3)"
- If **Tier 4**: Present recommendation, then proceed to Track B automatically. Ask: "This is a Tier 4 task. Producing full implementation spec via Skeleton-to-Spec."
- If user explicitly requests Track B for any tier: proceed to Track B.

---

## TRACK B: Skeleton-to-Spec (Tier 4 or on request)

Read `references/skeleton-to-spec.md` for the complete protocol. Summary of phases:

### Phase 5: Skeleton Draft
Expand the approved approach into a full architectural skeleton. Mark [DECISION] and [ASSUMPTION] tags explicitly. Keep it deliberately incomplete — this is a draft for critique.

### Phase 6: Multi-Perspective Review
Spawn 2-3 reviewer agents in parallel using `agents/skeleton-reviewer.md`:
- Reviewer 1: Technical Architect perspective
- Reviewer 2: Devil's Advocate perspective
- Reviewer 3 (Tier 4 only): User's Proxy perspective

Reviewers MUST consult project `docs/patterns.md` and `docs/lessons.md` for historical context before reviewing. Pass these file paths in the agent prompt.

### Phase 7: Intent Filter
Run `agents/intent-filter.md` to filter ALL reviewer feedback through: "Does this serve the user's ORIGINAL INTENT?" Categorize each as ACCEPT | REJECT | FLAG.

Present any FLAGGED items to the user for decision.

### Phase 8: Refinement + Second Review
- Incorporate ACCEPTED feedback into the skeleton
- Spawn ONE fresh reviewer to check for regression and bloat
- Do NOT iterate more than once — the goal is refinement, not perfection

### Phase 9: Zero-Context Spec Assembly
Produce the final self-contained specification:
- User's original request (verbatim)
- What is being built and why
- Full architecture from refined skeleton
- Component specifications with I/O
- Good output examples (what success looks like)
- Bad output examples (what failure looks like)
- Definition of Done checklist

The spec must be self-contained. A worker with NO prior context must be able to execute it.

---

## Output Format (Track A)

```markdown
# qplan 3.0: [Task Name]

## Complexity: Tier [N] | Track [A | A+B] | Mode [full | --lite]

## Intelligence Report
- **Habitat**: [1-2 sentences]
- **APIs**: [1-2 sentences] (omitted in --lite mode)
- **UX Standards**: [1-2 sentences] (omitted in --lite mode)
- **Known Risks**: [1-2 sentences]

---

## Approach 1: The Fortress — [Name]

**The Vision**: [One sentence]

### The Architect: Structural Logic
[Key architectural decisions]

### The Visionary: UX Highlights
[User experience factors]

### The Auditor: Chaos Test Strategy
[Failure modes and testing]

**Excellence Score: X/15** (Raw) | **Y/15** (Verified)
- Integrity: X/5 | Resilience: X/5 | Delight: X/5
- CoVe: N claims | N confirmed | N refuted | N unverifiable

---

## Approach 2: The Avant-Garde — [Name]
[Same structure]

---

## Approach 3: The Synthesizer — [Name]
[Same structure]

---

## Recommendation: [Approach Name]

**Rationale**: [Why this wins on excellence balanced with pragmatic constraints]
**Unbreakable Strategy**: [QA approach]
**Varnish Checklist**: [UX polish items]

## Interrogation Questions
1. [Edge-case question]
2. [UX preference question]
3. [Scale question]

## Token Savings
- Intelligence: ~7k tokens (4 parallel agents) / ~4k tokens (2 agents in --lite)
- Main session: ~3k tokens (orchestration)
- Net savings vs inline: ~4-7k tokens
```

---

## Critical Rules

1. **ALWAYS spawn all applicable research agents** in Phase 1 — all 4 for existing projects (full mode), 2 for `--lite` mode, 3 (skip habitat) for greenfield. Never skip intelligence gathering.
2. **NEVER guess at APIs** — agents use WebFetch/WebSearch for official docs
3. **NEVER inflate scores** — CoVe Phase 3 will catch inflation
4. **ALWAYS apply tri-lens analysis** — all three perspectives for every approach
5. **BALANCE excellence with pragmatic constraints** — this is a solo developer project
6. **PARALLEL execution** — all agent spawns in single messages
7. **ALWAYS run CoVe** — verify claims before presenting to user
8. **PRESENT VERIFIED scores** — both raw and verified, always
9. **PRESERVE original words** — user's request flows verbatim through all phases
10. **FILTER feedback against intent** — reject enterprise bloat for solo developers

---

## Error Handling

### If an agent fails or returns empty
- Note which agent failed in the output
- Proceed with available intelligence (3 of 4 reports is still valuable)
- Flag the gap: "API research unavailable — approach scores may be less reliable for integration claims"

### If CoVe finds many refuted claims
- Do NOT hide this. Present it prominently.
- Suggest the user consider alternative approaches or request deeper research
- An approach with 50%+ refuted claims should NOT be recommended

### If the user wants to skip phases
- Track A is the minimum. Never skip Phase 1 (intelligence) or Phase 3 (CoVe).
- User CAN skip Track B by saying "just the approaches" or "no spec needed"
- User CAN request Track B for any tier by saying "full spec" or "give me the implementation spec"

---

## Troubleshooting

### Approaches aren't distinct enough
The 3 approaches are minor variations, not fundamentally different architectures.
**Fix**: Re-read `references/three-approaches.md`. The Synthesizer MUST challenge the problem framing itself — if it uses the same stack as The Fortress, it's not distinct. Ask: "What if we didn't need X at all?"

### User disagrees with all 3 approaches
The user says "none of these are right."
**Fix**: Ask what's wrong with each. Use their objections as constraints for a second pass. Spawn 2 new Plan agents with the user's constraints added. Generate 2 new approaches (keep the best original as option 3).

### Skill triggers but user just wants to discuss architecture
The user is exploring, not ready for a full qplan.
**Fix**: If the user's message is a question ("how does X work?", "what do you think about Y?"), answer directly. Only run qplan when the user describes something to BUILD or explicitly invokes /qplan.

### CoVe marks everything unverifiable (new project)
No existing code to verify against.
**Fix**: This is expected for greenfield projects. Present scores without CoVe adjustment and note: "Greenfield project — scores reflect architectural analysis only, not codebase verification."

### One research agent returns poor results
Agent came back with generic advice, not task-specific intelligence.
**Fix**: Note the weak report in the Intelligence Report section. If API or Risk research is weak, flag that the relevant approach scores may be less reliable. Consider spawning a replacement agent with a more specific prompt.

---

## Performance Baselines

Expected resource usage per track:

| Metric | Track A | Track A + B | `--lite` Mode |
|---|---|---|---|
| Agent spawns | 7 (4 research + 3 CoVe) | 12 (+ 3 reviewers + 1 filter + 1 second review) | 3 (2 research + 1 CoVe batch) |
| Parallel batches | 2 (research, then CoVe) | 4 (+ reviewers, then second review) | 1 |
| Total runtime | 60-90 seconds | 3-6 minutes | ~30-45 seconds |
| Token budget (agents) | ~15-20k | ~30-40k | ~8-10k |
| Main context cost | ~3-5k | ~8-12k | ~2-3k |
| Net savings vs inline | 4-7k tokens | 10-15k tokens | 2-4k tokens |

If a run significantly exceeds these ranges, something is wrong (agent looping, over-broad research queries, etc.).

---

## Integration

- **Before planning**: Phase 0.5 ensures project config is ready (scaffolds if greenfield).
- **After user approves approach**: implement directly.
- **After implementation**: invoke `/check` for multi-agent code review.
- **Periodic maintenance**: invoke `/config-health` to audit global config alignment.

---

## References

For detailed methodology, consult these on demand:
- `references/example-output.md` — Complete example of a real qplan output (read this first for calibration)
- `references/scoring-criteria.md` — 0-15 excellence rubric with examples
- `references/tri-lens-analysis.md` — Architect/Auditor/Visionary framework
- `references/three-approaches.md` — Approach construction patterns and templates
- `references/complexity-tiers.md` — Tier 1-4 assessment framework
- `references/skeleton-to-spec.md` — Track B deep planning protocol (Phases 5-9)
- `references/project-claude-template.md` — Project CLAUDE.md scaffolding template (Phase 0.5)

---

## Quality Notes

Take your time with each phase. Quality of analysis is more important than speed. Do not skip validation steps or rush through scoring. A thorough qplan that takes 90 seconds is worth far more than a sloppy one that takes 30.

When constructing approaches, think deeply about what makes each one genuinely different. When scoring, be honest — a well-justified 9/15 is more valuable than an inflated 13/15 that CoVe will shred.

---

## Checkpoint Protocol

If the session is interrupted or context is compacted mid-qplan, the skill can resume from the last completed phase. After each phase, the state is implicitly checkpointed in the conversation. To resume:

- **Interrupted during Phase 1**: Re-spawn any agents that didn't return. Don't re-run agents that already returned.
- **Interrupted during Phase 2-3**: Intelligence reports are in context. Resume from where synthesis or CoVe left off.
- **Interrupted during Track B**: The approved approach and intelligence are in context. Resume from the last completed Phase (5-9).
- **After compaction**: If the compacted summary preserves the approved approach and intelligence findings, resume Track B directly. If critical context was lost, re-run Phase 1 (research is fast and cheap).

To make this work, always output phase completion markers:
```
--- Phase [N] Complete ---
```

---

Begin Phase 0: Assess complexity, then Phase 1: Spawn research agents in parallel (4 in full mode, 2 in --lite mode).
