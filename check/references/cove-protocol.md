# CoVe (Chain-of-Verification) Protocol

Based on Meta AI research (Dhuliawala et al., 2023, arXiv:2309.11495).
Core finding: accuracy jumps from ~17% to ~70% when verification happens
in isolated contexts instead of joint contexts.

## When This Runs

- After Phase 1 squad reports are collected (standard flow)
- Directly on user-provided context (when --verify-only flag is used)

## The 4-Step Protocol

### Step 1: Claim Extraction

Read the input (squad reports or user-provided context) and extract every
verifiable claim. Be exhaustive -- more claims is better than fewer.

Tag each claim:
- `[FACTUAL]` -- verifiable by reading code/docs
- `[BEHAVIORAL]` -- verifiable by tracing execution paths
- `[STRUCTURAL]` -- verifiable by checking architecture/wiring
- `[NEGATIVE]` -- absence claims ("no X found") -- PRIORITIZE THESE
- `[SUBJECTIVE]` -- opinions/scores -- verify the reasoning

Priority order for verification:
1. All NEGATIVE findings ("no security issues", "no dead code") -- these are
   most likely to be wrong (absence is hard to verify)
2. All CRITICAL findings -- highest impact if wrong
3. All LOW-CONFIDENCE squad findings
4. HIGH-CONFIDENCE findings (may be overconfident)

Output:
```
CLAIMS EXTRACTED: N total
C1: [claim text] [TAG] -- Source: [squad name + finding number]
C2: [claim text] [TAG] -- Source: [squad name + finding number]
...
```

### Step 2: Question Generation

For EACH claim, generate an open-ended verification question.

CRITICAL RULE: Questions MUST be open-ended. NEVER yes/no.

Why: Models agree with yes/no questions ~80% of the time regardless of
truth value. Open questions force genuine analysis.

BAD (yes/no -- triggers sycophantic agreement):
- "Does authenticate() return a tuple?" -> model likely says "yes"
- "Is there SQL injection risk?" -> model confirms original finding
- "Does this handle null inputs?" -> model agrees with whatever was claimed

GOOD (open-ended -- forces genuine analysis):
- "What does authenticate() return? Describe the return type and all
  possible return values."
- "What user inputs reach SQL queries in this code? Trace each input to
  its query construction."
- "What happens when null/None is passed to this function? Trace the
  execution path line by line."

Rules:
1. Each question targets exactly ONE claim
2. Must be answerable by reading code/docs alone
3. Must NOT contain the expected answer
4. Must NOT reference the original finding being verified
5. Frame as genuine inquiry, not leading question

Output:
```
VERIFICATION QUESTIONS:
Q1 (for C1): [open-ended question]
Q2 (for C2): [open-ended question]
...
```

### Step 3: Factored Execution (Isolated Sub-Agents)

Spawn sub-agents to answer verification questions in CLEAN, ISOLATED contexts.

CRITICAL: Context isolation is what makes CoVe work. Each sub-agent:
- Receives ONLY: its assigned question(s) + relevant source files
- CANNOT see: original squad findings, other claims, overall analysis
- CAN see: the actual code files needed to answer the question

Batching strategy (priority-based isolation):
- CRITICAL and NEGATIVE claims: ALWAYS 1 agent per claim (maximum isolation --
  these are the claims most likely to be wrong and highest-impact if wrong)
- IMPORTANT claims: batches of 2-3
- RECOMMENDATION claims: batches of 5

ALL agents MUST be spawned in a SINGLE message (parallel execution).

Agent configuration:
- subagent_type: "general-purpose"
- model: (inherits session model -- Opus/Sonnet)

KNOWN LIMITATION: The Claude Code Agent tool does NOT support a `model` parameter
override. CoVe agents run on the session model (typically Opus or Sonnet), not
Haiku as the Meta AI research used. This does NOT reduce verification quality --
context isolation is what makes CoVe work, not model capability. The only impact
is higher token usage and slightly longer wall time vs. Haiku.

Prompt template for each agent:
```
You are a verification agent. Answer the following question(s) by reading
the relevant code/files.

IMPORTANT RULES:
- Answer based ONLY on what you read in the actual code/files
- Do NOT speculate or assume -- if you can't verify, say "UNABLE TO VERIFY"
- Provide file:line references for every assertion you make
- Quote actual code when relevant
- Be precise and specific

QUESTION(S):
[question(s)]

RELEVANT FILES TO READ:
[file list]

Answer each question thoroughly with evidence from the code.
```

Collect all answers:
```
VERIFICATION ANSWERS:
A1 (for Q1/C1): [agent's answer with file:line references]
A2 (for Q2/C2): [agent's answer with file:line references]
...
```

### Step 4: Cross-Check

Compare each verification answer against the original claim:

**CONFIRMED** -- Agent's independent finding matches the original claim.
Evidence provided aligns with the claim.
```
C1: CONFIRMED -- [brief evidence summary from agent]
```

**REFUTED** -- Agent found something DIFFERENT from what was claimed.
This is the most important finding -- this is an error caught by CoVe.
```
C1: REFUTED -- Original: "[claim]" -> Actual: "[what agent found]"
```

**UNCERTAIN** -- Agent couldn't find enough evidence to confirm or deny.
```
C1: UNCERTAIN -- [reason for uncertainty]
```

**PARTIAL** -- Some aspects confirmed, others not.
```
C1: PARTIAL -- Confirmed: [what's right] | Refuted: [what's wrong]
```

Rules:
- Default to UNCERTAIN, not CONFIRMED -- err on caution
- NEGATIVE claims require extra scrutiny -- agent must have actually
  searched for the thing claimed absent
- A claim is REFUTED only if agent provides SPECIFIC evidence contradicting it

### Confidence Assessment

WHEN: Compute this AFTER Step 4 (Cross-Check) completes, BEFORE generating
the Output Format below. Count all claims and their verdicts, then apply:

**HIGH**: >80% confirmed, 0 refuted
**MEDIUM**: >60% confirmed, <10% refuted
**LOW**: <60% confirmed OR >10% refuted

Include the computed confidence level in the "Verification Confidence" field
of the output format summary table.

### Output Format

```
## CoVe Verification Results

### Summary
- Claims verified: N
- Confirmed: N (X%)
- Refuted: N (X%)
- Partial: N (X%)
- Uncertain: N (X%)
- Verification Confidence: [HIGH / MEDIUM / LOW]

### Refuted Claims (ERRORS CAUGHT BY COVE)
| # | Original Claim | What Verification Found | Evidence |
|---|---------------|------------------------|----------|
| CN | [claim] | [what agent found] | file:line |

### Corrections to Squad Reports
[For each REFUTED claim, specify which squad report needs updating
and what the corrected finding should be]

### Refutation Root Cause Analysis
For each REFUTED claim, classify the root cause:

| Root Cause | Meaning | Action |
|-----------|---------|--------|
| **Protocol gap** | Squad protocol doesn't check for this correctly | Update the squad's reference file with improved detection logic |
| **Execution error** | Squad ran the protocol but made a mistake this time | Log for pattern tracking. No protocol change needed unless recurring. |
| **False positive** | Squad flagged something that isn't actually an issue | Add to lessons-enforcer as "known false positive pattern" to suppress in future |
| **Scope miss** | Claim was about something outside the squad's domain | Consider reassigning this check to a different squad |

Track refutation root causes across reviews. If the same squad has >3
protocol gaps in 10 reviews, its reference file needs a major revision.

### Uncertain Claims (NEEDS INVESTIGATION)
| # | Claim | Why Uncertain | Recommended Action |
|---|-------|--------------|-------------------|
| CN | [claim] | [reason] | [what to do] |
```

## --verify-only Mode

When invoked with --verify-only flag (replaces standalone /cove):

1. Skip Phase 1 entirely
2. Read whatever context the user provided (analysis output, previous
   review findings, any text with claims to verify)
3. Execute Steps 1-4 of this protocol on that context
4. Output the verification results
5. Hand off refuted claims to coding agent or /qplan
