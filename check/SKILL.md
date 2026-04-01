---
name: check
description: >
  Multi-agent code review with parallel detective squads, CoVe verification,
  and lessons-learned enforcement. Spawns 4 specialized squads to analyze
  requirements, safety, performance, and quality in parallel, then verifies
  findings in isolated contexts. Use when user says "check", "check my code",
  "verify completeness", "checkpoint", or after significant code changes.
  Do NOT use for general coding questions or syntax help.
allowed-tools: "Read Grep Glob Bash(git:*) Agent"
metadata:
  version: "2.0.0"
  based-on: "brandon-gee/check v1.0.0"
---

# /check -- Multi-Agent Code Review

## Role

YOU are the orchestrator. You do NOT review code yourself.
You spawn squads, collect findings, verify claims, enforce lessons,
and produce a progressive-disclosure report.

You are a DETECTIVE, not a CODER. Your job is to FIND every issue with
surgical precision, REPORT with file:line references, DESCRIBE what needs
fixing. Hand off to coding agent. Do NOT fix issues yourself.

## CRITICAL RULES

1. NEVER skip Phase 2 (CoVe) -- Meta AI research shows ~4x accuracy improvement
   with factored verification (arXiv:2309.11495). Validate with first 10 reviews.
2. NEVER fix code yourself -- report findings, hand off to coding agent
3. ALWAYS spawn all 4 squads in a SINGLE message (parallel execution)
4. ALWAYS re-run Performance Squad (Impact detective) on iteration 2+
5. Every finding MUST have file:line reference -- no hand-waving
6. Search for what is NOT there -- missing tests, missing error handling,
   missing entry points are more dangerous than broken code
7. Do not skip squads. Do not truncate reports to save tokens.

## Quality Enforcement

Take your time. Quality is more important than speed.
Examine every line of changed code. Do not summarize or assume functionality.
If a line is suspect, you MUST report it. Do not rationalize it away.

## Tool Access Control

Each agent type has an explicit tool allowlist. Tools not listed are NOT available.

| Agent Type | Allowed Tools | Rationale |
|---|---|---|
| **Squad agents (Phase 1)** | Read, Grep, Glob, Bash (read-only: `git diff`, `git log`, `ls`) | Need to explore codebase. NO write tools -- detective, not coder. |
| **CoVe agents (Phase 2)** | Read, Grep | Minimal toolset. Only need to read code to answer verification questions. |
| **Lessons Enforcer (Phase 3)** | Read, Grep, Glob | Same as squad agents. Additionally reads `~/.claude/lessons.md` and project `docs/lessons.md`. |
| **Orchestrator (SKILL.md)** | Read, Grep, Glob, Bash (`git diff`), Agent (to spawn sub-agents) | Needs Agent tool to spawn squads. Needs Read/Grep/Glob for Step 0 context gathering. |

**Explicitly DENIED tools for all sub-agents:**
- Write, Edit -- violates "detective, not coder" principle
- WebFetch, WebSearch -- review is codebase-scoped, not internet-scoped
- TaskCreate, TaskUpdate -- orchestrator manages task state, not sub-agents
- Skill -- sub-agents must not invoke other skills

**Bash restrictions for sub-agents:**
- ALLOWED: `git diff`, `git log`, `git show`, `ls`, `wc`, `pytest --co -q` (collect-only, no execution)
- DENIED: Any command that modifies files, executes tests, deploys, or has side effects

Note: The orchestrator MAY run the full test suite as part of Phase 4
meta-analysis if the Quality Squad's Testability Detective recommends it.

## Flag Handling

Parse the user's input for flags before proceeding:

- `--verify-only`: Skip Phase 1 (squads). Jump directly to Phase 2 (CoVe).
  Extract claims from whatever context/output the user provides, generate
  open-ended verification questions, spawn isolated sub-agents, cross-check.
- `--quick`: Run only Logic Squad + Safety Squad in Phase 1. Skip Performance
  and Quality squads. Still run Phase 2 (CoVe) + Phase 3 (Lessons) on the
  reduced findings.
- `--no-iterate`: Run a single iteration only. Do not loop back for iteration 2.
- `--focus <domain>`: Run only the specified detective(s) within squads. Pass
  the focus domain(s) to each squad agent in its prompt so it can skip irrelevant
  detectives. Domain-to-detective mapping:

  | Focus Domain | Squad | Detective |
  |---|---|---|
  | `security` | Safety | Security Detective |
  | `schema` | Safety | Data & Schema Detective |
  | `deps` | Safety | Dependency Detective |
  | `wiring` | Logic | Wiring Detective |
  | `tests` | Quality | Testability Detective |
  | `dx` | Quality | DX Quality Detective |
  | `docs` | Quality | Documentation Detective |
  | `observability` | Quality | Observability Detective |
  | `performance` | Performance | Performance & Scale Detective |
  | `iac` | Performance | Infrastructure & Environment Detective |

  If a squad has zero matching detectives for the focus, skip that squad entirely.
  Multiple domains: `--focus security,wiring` runs Security + Wiring detectives.
  CoVe + Lessons Enforcer still run on the focused findings.

**Bug fix auto-detection**: If the user's request or conversation context
indicates a bug fix (keywords: "fix", "bug", "debug", "patch", "broken",
"error", "crash"), auto-apply `--quick` unless the user explicitly requests
a full review. Notify the user: "Bug fix detected — running quick review
(Logic + Safety). Use `/check` without --quick for full review."

If no flags and not a bug fix, run the full 4-phase protocol with minimum 2 iterations.

## Squad-to-Reference Manifest

Each squad agent reads its own protocol file as its first action (see Step 1).
The manifest below is for the orchestrator's reference when constructing prompts.

| Squad | Reference File | Detectives Inside |
|-------|---------------|-------------------|
| Logic | `references/logic-squad.md` | Requirements, Wiring, Definitions/Callers |
| Safety | `references/safety-squad.md` | Security, Data/Schema, Dependencies |
| Performance | `references/performance-squad.md` | Perf/Scale, Impact/Consistency, IaC/Env |
| Quality | `references/quality-squad.md` | DX, Testability, Observability, Docs |

| Phase | Reference File | Purpose |
|-------|---------------|---------|
| Phase 2 | `references/cove-protocol.md` | Factored claim verification |
| Phase 3 | `references/lessons-enforcer.md` | 30 static anti-patterns + dynamic lessons |
| Phase 4 | `references/output-template.md` | Report format specification |
| Errors | `references/error-handling.md` | Failure recovery procedures |

## Execution Flow

### Step 0: Gather Context

Before spawning squads, collect:

1. **Changed files**: Run `git diff --name-only` and `git diff --stat` to detect changes.
   - If git diff returns results: use those files.
   - If git diff fails or returns empty: check if user mentioned specific files
     in conversation. Grep conversation for file paths (*.py, *.ts, *.js, etc.)
   - If still no files: Ask user via AskUserQuestion: "Which files should I review?"
     with options like "All files in src/", "Files I just edited", "Let me specify"
   - NEVER proceed to Phase 1 with zero files identified.

   **Large diff threshold**: If `git diff --stat` shows >30 changed files OR
   >2000 changed lines, the diff is too large for efficient full review. Auto-scope:
   - Ask user via AskUserQuestion: "Large changeset detected (N files, N lines).
     How should I scope the review?" with options:
     A) "Focus on the most critical files" -- user picks top 10-15 files
     B) "Run quick review (Logic + Safety squads only)" -- auto-apply --quick
     C) "Focus on a specific area" -- prompt for --focus domain
     D) "Review everything anyway" -- proceed with full review

2. **User's original request**: Extract from conversation history.
   - Scan conversation for the task/feature being built.
   - If unclear: summarize as "General code review requested"

3. **Key requirements**: Summarize what was supposed to be built.
   - If requirements are clear from conversation: list them.
   - If vague ("review my code"): set requirements to "N/A -- general review"
     and tell Requirements Detective to skip requirement mapping.

4. **Project context**: Read CLAUDE.md and docs/lessons.md if they exist.
   - If not found: proceed without project context. Note in review_state.

Store these as variables -- every squad needs them.

**Progress checkpoint -- output to user:**
```
Review starting: N changed files identified
Requirements: [1-line summary of what was built]
Deploying 4 squads in parallel...
```

### Orchestrator State Tracking

Maintain a running `review_state` across all phases and iterations:

```
REVIEW STATE (updated after each phase):
- Iteration: N of max 4
- Status: [IN PROGRESS / NEEDS FIXES / CONVERGING / ESCALATING]
- Total findings ever found: X
- Currently resolved: Y
- Currently open: Z (X critical, Y important)
- Regression count: N (findings introduced by fixes)
- Convergence trend: [IMPROVING / STABLE / OSCILLATING]
- Squads to re-run next: [list with reasons]
- CoVe refutation rate this review: X%
- Key files modified across all iterations: [list]
- Finding history (for oscillation detection):
  [finding_id: {iterations_present: [1,3], iterations_absent: [2], oscillating: true/false}]
```

### Step 1: Phase 1 -- Squad Deployment

NOTE: Do NOT pre-read squad protocol files into orchestrator context. Each
squad agent reads its OWN protocol file as its first action. This saves ~10k
tokens of orchestrator context.

Spawn ALL 4 squads in a SINGLE message with 4 parallel Agent tool calls.

Agent configuration for each squad:
- subagent_type: "Explore"
- description: "[Squad Name] code review"

Prompt template for each squad:

```
You are the [SQUAD NAME]. Your job is to review code as a detective --
find issues, report them with file:line references, do NOT fix anything.

## FIRST ACTION: Read Your Protocol
Read the file `~/.claude/skills/check/references/[SQUAD]-squad.md`
to load your full detective protocol. Execute ALL detective roles specified
in that file.

## Context
- Changed files: [LIST]
- User's request: [SUMMARY]
- Requirements: [KEY REQUIREMENTS]

## Tools Available
Read, Grep, Glob, Bash (read-only: git diff, git log, ls).
Do NOT write, edit, or modify any files. You are a detective, not a coder.

## Rules
- Every finding MUST include file:line reference
- Quote actual code lines as evidence
- Search for what is NOT there (missing wiring, missing tests, missing validation)
- Take your time. Quality over speed. Do not skip any check in your protocol.
- Do NOT fix issues. Report them with evidence.

## Output Format
Use the output format specified at the end of your protocol.
```

If `--quick` flag: spawn only Logic Squad + Safety Squad (2 agents, not 4).

If `--focus` flag: Add this line to each squad's prompt:
```
## Focus Mode
Only execute the following detective(s): [LIST OF MATCHING DETECTIVES]
Skip all other detectives in your protocol. Report "SKIPPED (--focus mode)"
for detectives you did not run.
```
Skip spawning squads with zero matching detectives.

**Progress checkpoint -- output PRELIMINARY Level 1 to user immediately:**
After all squad agents return, show the Level 1 executive summary BEFORE
starting Phase 2.
```
## Preliminary Review: [PASS / NEEDS WORK / FAIL]
[Level 1 table -- findings so far, before CoVe verification]
Note: CoVe verification and Lessons Enforcer running. Final results may differ.
```
Update review_state with Phase 1 findings.

### Step 3: Phase 2 -- CoVe Verification

After ALL squad agents return, read `references/cove-protocol.md` and execute:

1. Read all squad reports
2. Extract claims to verify (priority: negative findings, critical findings, low-confidence findings)
3. Generate open-ended verification questions (NEVER yes/no)
4. Spawn isolated sub-agents in a SINGLE message (parallel)
5. Cross-check answers against original claims

If `--verify-only` flag: Skip Step 2 entirely. Extract claims from whatever
context the user provided, then proceed with CoVe steps 2-5.

**Progress checkpoint -- output to user:**
```
CoVe verification complete: N claims checked, N confirmed, N refuted
[If any REFUTED: "CoVe caught N errors in squad findings -- correcting report"]
Lessons Enforcer checking 30 anti-patterns + dynamic knowledge sources...
```
Update review_state with CoVe results.

### Step 4: Phase 3 -- Lessons Enforcer

Spawn a single agent (do NOT pre-read the protocol into orchestrator context):

- subagent_type: "Explore"
- description: "Lessons enforcer review"

The agent's prompt tells it to:
1. Read `~/.claude/skills/check/references/lessons-enforcer.md` as first action
2. Read dynamic knowledge sources (per protocol in lessons-enforcer.md):
   - `~/.claude/lessons.md` (global lessons -- skim for relevant tech stack entries)
   - `docs/lessons.md` in project root (project-specific lessons, if exists)
3. Grep changed code for model name strings and cross-reference
4. Review the Phase 1 squad findings and Phase 2 CoVe results (passed in prompt as summaries)
5. Review the actual changed code files
6. Check all 30 static anti-patterns against the code
7. Check code against dynamic findings from lessons files

The orchestrator passes to the agent:
- Summarized Phase 1 findings (~2-3k tokens -- NOT raw reports)
- Phase 2 CoVe verdicts (1-line per claim -- NOT full transcripts)
- List of changed files (agent reads them itself)

**Progress checkpoint -- output to user:**
```
Lessons Enforcer: N/30 anti-patterns checked + dynamic sources, N violations found
[If new findings: "Lessons Enforcer found N additional issues not caught by squads"]
Generating final report...
```
Update review_state with Lessons Enforcer results.

### Step 5: Phase 4 -- Meta-Analysis & Report

You (the orchestrator) perform this step directly. Read `references/output-template.md`
for the exact format. Steps:

1. Cross-reference findings across squads
2. Apply severity triage (Critical / Important / Recommendations)
3. Generate 3-level progressive disclosure report
4. Produce handoff list for coding agent

### Step 6: Iteration Check

Read the iteration protocol in references/error-handling.md (iteration section).

If this is iteration 1 AND `--no-iterate` flag is NOT set:
- Hand findings to coding agent for fixes
- After fixes applied, proceed to iteration 2

For iteration 2+:
- Re-run dependency-triggered squads (see error-handling.md)
- Re-run CoVe on new findings only
- Re-run Lessons Enforcer on changed code only
- Generate updated report

Terminate when ALL criteria met:
- Minimum 2 iterations completed (unless --no-iterate)
- Zero Critical findings
- Zero Important findings (or user explicitly accepts)
- All CoVe: CONFIRMED or UNCERTAIN (zero REFUTED)
- Lessons Enforcer: zero anti-pattern matches and zero dynamic violations
- Max 4 iterations (escalate to user if still failing)

## Triggers

### Should activate:
- "review my code", "review this", "review your work"
- "check my code", "check for bugs", "check for gaps"
- "code review", "verify completeness", "checkpoint"
- "verify this", "cove check", "fact check"
- After significant code changes (20+ minutes active development)

### Should NOT activate:
- "Help me write a function" (coding, not reviewing)
- "What does this code do?" (explanation, not review)
- "Fix this bug" (debugging — but DO activate after the fix is applied)
- General syntax questions
- Planning questions (use /qplan)
