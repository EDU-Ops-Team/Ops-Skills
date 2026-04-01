# Iteration Protocol

## Dependency-Triggered Re-Runs

After the coding agent fixes issues from iteration N, determine which
squads must re-run based on what was changed:

| If fixes touched... | These squads MUST re-run |
|---------------------|------------------------|
| Entry points, routes, handlers | Logic Squad, Performance Squad |
| Security (auth, validation, secrets) | Safety Squad, Performance Squad |
| Database queries, schema, models | Safety Squad, Performance Squad |
| Performance (async, caching, limits) | Performance Squad, Logic Squad |
| Tests, documentation, naming | Quality Squad only |
| Multiple domains | ALL squads |

**MANDATORY**: Performance Squad (Impact & Consistency detective) ALWAYS
re-runs on iteration 2+. It is the regression safety net. A fix in one
domain can cascade to break another.

## Iteration Flow

### Iteration 1 (always full):
1. Phase 1: All 4 squads (parallel)
2. Phase 2: CoVe on priority claims
3. Phase 3: Lessons Enforcer
4. Phase 4: Report
5. Hand findings to coding agent

### Iteration 2+ (dependency-triggered):
1. Coding agent applies fixes
2. Determine which squads must re-run (see table above)
3. Phase 1: Re-run triggered squads only (parallel)
4. Phase 2: CoVe on NEW findings only (from re-run squads)
5. Phase 3: Lessons Enforcer on CHANGED code only
6. Phase 4: Updated report (show delta from previous iteration)

### Termination Criteria (ALL must be true):
- Minimum 2 iterations completed (unless --no-iterate flag)
- Zero Critical findings remaining
- Zero Important findings remaining (or user explicitly accepts)
- All CoVe cross-checks: CONFIRMED or UNCERTAIN (zero REFUTED)
- Lessons Enforcer: zero anti-pattern violations and zero dynamic violations
- Statement: "I've verified everything. Nothing was missed."

### Maximum Iterations: 4
If issues persist after 4 iterations, stop looping and escalate to user
with this template:

```
## Review Escalation -- Maximum Iterations Reached

After 4 iterations, the following findings remain unresolved:

### Remaining Critical Findings
[list each with file:line + evidence + what was attempted]

### Remaining Important Findings
[list each with file:line + evidence]

### What Was Tried
- Iteration 1: [summary of findings and fixes]
- Iteration 2: [summary of findings and fixes]
- Iteration 3: [summary of findings and fixes]
- Iteration 4: [summary of findings and fixes]

### Why These Persist
[Explain: is this a fundamental design issue? A circular dependency
between fixes? An external constraint? Or diminishing returns?]

### Recommended Next Steps
1. [Option A: accept remaining findings as known issues]
2. [Option B: restructure approach via /qplan]
3. [Option C: specific manual action needed]

Please advise which path to take.
```

### Inter-Iteration Compaction

Before starting iteration N+1, the orchestrator MUST compact prior iteration state
to prevent context rot (Anthropic: "as token count increases, model recall accuracy
from that context decreases"):

1. **Summarize resolved findings**: Replace full finding details with:
   "RESOLVED in iteration N: [finding title] at [file:line] -- [1-line fix description]"

2. **Carry forward unresolved findings**: Keep full details ONLY for findings
   that remain open. These are the inputs for the next iteration's squads.

3. **Discard raw squad reports**: After Phase 4 synthesis, the raw squad reports
   from iteration N are no longer needed. The Phase 4 report IS the compacted
   summary.

4. **Discard raw CoVe evidence**: Keep only the verdict (CONFIRMED/REFUTED/UNCERTAIN)
   and 1-line evidence summary per claim. Discard full agent transcripts.

5. **Carry forward Lessons Enforcer violations**: These persist until resolved.

Template for compacted iteration state:

```
## Iteration N Summary (compacted)
- Findings found: X critical, Y important
- Findings resolved: [list with 1-line each]
- Findings remaining: [full details -- these feed iteration N+1]
- CoVe verdicts: N confirmed, N refuted, N uncertain
- Lessons violations: [list -- carried until resolved]
- Files modified in fixes: [list]
```

This compacted state replaces ALL raw data from iteration N in the orchestrator's
context. Target: <5k tokens per compacted iteration summary.

If orchestrator context exceeds 80k tokens at any point, apply compaction
immediately before continuing.

### Fix-Regression Circuit Breaker

After each iteration's fixes are applied and re-run results are in, compute:

```
net_progress = findings_resolved_this_iteration - new_findings_introduced_this_iteration
```

**Healthy iteration**: net_progress > 0 (more resolved than introduced)
**Stalled iteration**: net_progress == 0 (treading water)
**Regressing iteration**: net_progress < 0 (fixes are making things worse)

Behavior:
- **Healthy**: Continue to next iteration normally
- **Stalled (1x)**: Continue but flag to user: "Warning: iteration N made no net
  progress. Remaining issues may require architectural changes rather than local fixes."
- **Stalled (2x consecutive)**: Stop iterating. Escalate to user:
  "Two consecutive iterations made no progress. Remaining findings may require
  /qplan to restructure the approach rather than incremental fixes."
- **Regressing (1x)**: Stop iterating immediately. Escalate to user:
  "Iteration N introduced more issues than it resolved. The fixes are destabilizing
  the code. Recommend reverting iteration N fixes and using /qplan to design a
  more targeted fix strategy."

### Oscillation Detection

In addition to net_progress, check for oscillating fixes -- the same finding
appearing, getting fixed, then reappearing because the fix for something else
undid it:

```
For each finding in iteration N:
  Check: was this SAME finding (same file:line, same issue type) present in
  iteration N-2 but absent in iteration N-1?
  If yes: this is an oscillating finding.
```

**Oscillation detected (any count)**: Stop iterating immediately. Escalate:
"Finding '[title]' at [file:line] is oscillating -- it was fixed in iteration
N-1 but reintroduced in iteration N. The fixes for [other finding] and
[this finding] are mutually incompatible. This requires /qplan to design a
solution that addresses both simultaneously."

Oscillation is the strongest signal that incremental fixes cannot converge.
It always requires architectural rethinking.

### Circuit Breaker Summary

The circuit breaker catches 3 failure modes, ordered by severity:

1. **Oscillation** (highest) -- same finding flip-flops. Stop immediately.
2. **Regression** -- net negative progress. Stop immediately.
3. **Stall** -- zero progress. Warn once, stop on second consecutive.

All three fire BEFORE the max-4-iteration limit and provide more actionable
guidance than "max iterations reached."

The orchestrator's review_state tracks `regression count`, `convergence trend`,
and per-finding history across iterations -- the circuit breaker reads those values.

### Iteration Report Delta Format
```
## Iteration N Report

### Changes Since Iteration N-1
- Files modified: [list]
- Fixes applied: [list of findings addressed]

### Re-Run Results
- Squads re-run: [list with reason]
- New findings: N
- Resolved findings: N
- Remaining findings: N
- Net progress: [+N / 0 / -N]
- Circuit breaker: [HEALTHY / STALLED / REGRESSING]

### Status: [PASS / NEEDS WORK / FAIL]
[Updated Level 1 executive summary]
```

---

# Error Handling

## Squad Failures

| Failure | Detection | Behavior |
|---------|-----------|----------|
| Squad agent times out (>120s) | Agent tool returns timeout | Report "[Squad] timed out -- findings incomplete" in Level 1. Mark affected domains as UNVERIFIED. Re-try that squad on iteration 2. |
| Squad returns empty output | Response has no findings section | Flag "SUSPICIOUS: [Squad] returned no findings. This usually means the agent didn't execute the protocol." Re-run that squad immediately (single retry). |
| Squad returns garbled/partial | Response missing expected sections | Extract whatever findings are parseable. Flag missing sections as UNVERIFIED. Note in report. |
| All squads fail | No squad returns usable results | FALLBACK: Execute a single-agent sequential review (v1-style). Read all 4 squad reference files yourself and execute the protocols sequentially. This is slower but ensures the review happens. |

## CoVe Failures

| Failure | Behavior |
|---------|----------|
| CoVe agent times out | Keep original squad finding unchanged. Add "CoVe: UNVERIFIED (timeout)" |
| CoVe agent returns "UNABLE TO VERIFY" | Keep original finding. Add "CoVe: UNCERTAIN" |
| All CoVe agents fail | Skip Phase 2. Note "CoVe verification skipped due to agent failures -- findings are UNVERIFIED" |

## Lessons Enforcer Failures

| Failure | Behavior |
|---------|----------|
| Enforcer times out | Skip Phase 3. Note "Lessons check skipped -- consider running /review again" |
| Enforcer can't read squad reports | Run Enforcer with code files only (skip cross-referencing squad findings) |
| Agent memory file not found | Run without institutional memory. Note "No project-specific memory available" |

## File Access Failures

| Failure | Behavior |
|---------|----------|
| Cannot read changed file | Report "Could not read [file] -- manual review needed" as IMPORTANT finding |
| Cannot determine changed files | Ask user: "Which files should I review?" via AskUserQuestion |
| File too large (>5000 lines) | Read in chunks. Note "Large file -- review may be incomplete" |

## Orchestrator Operational Failures

| Failure | Detection | Behavior |
|---------|-----------|----------|
| `git diff` fails or not a git repo | Command returns error or empty | Ask user via AskUserQuestion: "Which files should I review? I couldn't detect changes via git." |
| Reference file not found | Read tool returns error | CRITICAL: Cannot proceed without squad protocol. Check skill installation. Report: "check installation may be incomplete -- references/[file].md not found" |
| review_state tracking breaks | State document becomes inconsistent | Reset state from most recent Phase 4 report. Note: "State tracking reset -- iteration count may be inaccurate" |
| Context gathering returns nothing | No changed files, no user request identifiable | Ask user via AskUserQuestion: "I need more context. Please either: (A) provide the files to review, (B) describe what you built, or (C) point me to a git branch/commit." |
| Conversation history too large to extract requirements | Context budget exceeded before squad spawn | Summarize conversation to 3-5 bullet points of requirements. Note: "Requirements extracted from summarized conversation -- may miss details." |
| CLAUDE.md or patterns.md not found | Read returns error | Proceed without project context. Note: "No project configuration found -- reviewing without project-specific rules." |

## Agent Concurrency Limits

The blueprint assumes 4+ parallel Agent calls work when spawned in a single
message. If the runtime serializes or limits concurrent sub-agents:

| Scenario | Detection | Behavior |
|----------|-----------|----------|
| All 4 squads run in parallel | Normal -- proceed as designed | Expected case |
| Agent tool serializes calls (runs 1 at a time) | Squad wall time ~4x expected (>240s for Phase 1) | Acceptable -- slower but still works. Note in report: "Squads ran sequentially (Nx slower)" |
| Agent tool limits to 2 concurrent | Squad wall time ~2x expected | Acceptable -- minor slowdown |
| Agent tool rejects >N concurrent calls | Error on spawn attempt | Batch squads: spawn Logic+Safety first, then Performance+Quality after first two return |

If concurrency limits are discovered on day 1, update SKILL.md Step 2 to
batch squad deployment (2+2 instead of 4 parallel) and adjust time estimates.

## CoVe Model -- Known Limitation

The Claude Code Agent tool does NOT support a `model` parameter override.
All CoVe verification agents run on the session model (Opus/Sonnet).

This is a non-issue for accuracy -- context isolation is what makes CoVe work,
not model capability. Token budget in Section 14 already reflects the higher
session-model estimates (~15-30k for CoVe instead of ~5-10k for Haiku).

## General Recovery Principle

Never report "no issues found" when you actually failed to check.
"Unable to verify" is always more honest than "looks good to me."
