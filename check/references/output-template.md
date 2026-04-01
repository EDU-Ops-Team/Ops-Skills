# Output Template -- 3-Level Progressive Disclosure

## Level 1: Executive Summary (ALWAYS show first)

This is the FIRST thing the user sees. Must be scannable in 10 seconds.

```
# Review: [PASS / NEEDS WORK / FAIL]

| Metric | Value |
|--------|-------|
| Squads deployed | N/4 |
| Total findings | N critical, N important, N recommendations |
| CoVe verification | N claims checked, N confirmed, N refuted |
| Lessons enforcer | N/32 anti-patterns + dynamic sources checked |
| Iterations completed | N |

**Top issues:**
1. [Most critical finding] -- [file:line]
2. [Second most critical] -- [file:line]
3. [Third most critical] -- [file:line]
```

Verdict logic:
- **PASS**: Zero critical, zero important, CoVe confidence HIGH
- **NEEDS WORK**: Zero critical, 1+ important, OR CoVe confidence MEDIUM
- **FAIL**: 1+ critical, OR CoVe confidence LOW, OR Lessons Enforcer found Tier 1/2 violation

## Level 2: Squad Summaries (show after Level 1)

One paragraph per squad with key findings only.

```
## Squad Reports

### Logic Squad
- Requirements: X/Y implemented
- Wiring: [CONNECTED / GAPS] -- [brief description of gaps if any]
- Key findings: [2-3 most important with file:line]

### Safety Squad
- Security: [N issues] -- [brief description]
- Schema: [VALID / N mismatches]
- Key findings: [2-3 most important with file:line]

### Performance Squad
- Performance: [N issues] -- [brief description]
- Impact: [N cascading risks]
- Key findings: [2-3 most important with file:line]

### Quality Squad
- DX Grade: [A-F]
- Tests: [ADEQUATE / GAPS]
- Key findings: [2-3 most important with file:line]
```

## Level 3: Deep Dive (show in handoff or on request)

Full details for the coding agent to act on.

```
## Detailed Findings

### Critical [N]
[Numbered list. Each finding includes:]
1. **[Finding title]** -- [Squad that found it]
   - Evidence: [file:line + code quote]
   - CoVe status: [CONFIRMED / not verified]
   - Impact: [What breaks if not fixed]
   - Fix: [Specific action to take]

### Important [N]
[Same format as critical]

### Recommendations [N]
[Same format but lighter -- suggestion + file:line]

## CoVe Verification Details
[Full claim-by-claim results if user wants to see verification evidence]

## Lessons Enforcer Details
[Full anti-pattern check results]

## Handoff to Coding Agent

### Priority fix order:
1. [Highest priority -- file:line + what to do]
2. [Next priority -- file:line + what to do]
3. [Continue in priority order]

### Files to modify:
- [file1]: [what needs changing]
- [file2]: [what needs changing]

### Tests to add/update:
- [test1]: [what needs testing]
- [test2]: [what needs testing]
```

## Cross-Reference Confidence Rules

When the same issue is found by multiple sources, confidence increases:

| Sources | Confidence |
|---------|-----------|
| 3+ squads agree | HIGHEST -- near-certain finding |
| 2 squads agree | HIGH |
| 1 squad + CoVe CONFIRMED | HIGH |
| 1 squad, CoVe not checked | MEDIUM |
| 1 squad + CoVe UNCERTAIN | MEDIUM-LOW |
| CoVe REFUTED | REMOVED (with note explaining the refutation) |
| Lessons Enforcer only (new finding) | HIGH (institutional memory) |
