# Example qplan Output

This is a trimmed example of a real qplan analysis. Use it to calibrate output format, depth, and tone.

**Task**: "How should Jarvis handle novel queries that don't match hardcoded patterns?"

---

# qplan 3.0: Scalable Query System

## Complexity: Tier 3 | Track A

## Intelligence Report
- **Habitat**: 5 hardcoded BigQuery methods + 4 regex patterns. Novel queries fall through to semantic search, which can't do aggregations or temporal reasoning. 7-column flat schema (no JOINs needed).
- **APIs**: BigQuery has no NL-to-SQL API. LLM direct SQL generation is the industry approach. `dry_run=True` gives free syntax validation. `maximum_bytes_billed` caps costs.
- **UX Standards**: Best assistants (Copilot, Gemini, Perplexity) use 5-level fallback: exact answer, approximate, related data, suggest reframe, explain gap. Never say "can't."
- **Known Risks**: Text-to-SQL accuracy: 60-80% for well-scoped domains. SQL injection via prompt injection is real. Wrong answers destroy trust faster than no answers. Current regex fast path: less than 0.01ms vs dynamic generation 3-6 seconds.

---

## Approach 1: The Fortress — Expanded Function Library

**The Vision**: Scale the existing pattern from 5 methods to 30-50, covering every anticipated query type.

### The Architect: Structural Logic
Keep current architecture unchanged. Add methods for GROUP BY aggregations, temporal comparisons, cross-entity analysis. LLM extracts parameters only (never generates SQL). Each method is a tested, parameterized query.

### The User: Practical Value
Sub-millisecond response for all matched patterns. Predictable, consistent output. Users learn what the system can do through reliable answers.

### The Auditor: Chaos Test Strategy
Zero SQL injection risk (parameterized only). Zero cost explosion (queries pre-bounded). Failure mode: novel query doesn't match any pattern, returns "I can't answer that."

**Excellence Score: 8/15** (Raw) | **7/15** (Verified)
- Integrity: 4/5 | Resilience: 3/5 | Delight: 1/5
- CoVe: 6 claims | 4 confirmed | 1 refuted | 1 unverifiable

---

## Approach 2: The Avant-Garde — LLM-as-SQL-Generator

**The Vision**: Let the LLM generate SQL directly from natural language, sandboxed with safety rails.

### The Architect: Structural Logic
LLM receives schema + question, generates SELECT-only SQL. Validated via `dry_run=True` before execution. Parameterized cost cap at $0.10 per query. Results formatted by a second LLM pass.

### The User: Practical Value
Can answer ANY question about the data. Natural language in, formatted answer out. Feels like talking to a data analyst.

### The Auditor: Chaos Test Strategy
SQL injection mitigated by SELECT-only enforcement + parameterized cost cap. 20-40% error rate on complex queries. Wrong answers are worse than no answers for trust.

**Excellence Score: 9/15** (Raw) | **7/15** (Verified)
- Integrity: 3/5 | Resilience: 2/5 | Delight: 4/5
- CoVe: 8 claims | 3 confirmed | 3 refuted | 2 unverifiable

---

## Approach 3: The Synthesizer — Tiered Intelligence with Graceful Degradation

**The Vision**: Don't choose between safe-and-limited vs powerful-and-risky. Use BOTH — fast path for known patterns, sandboxed LLM for novel queries, graceful degradation for everything else.

### The Architect: Structural Logic
Three tiers: (1) Regex fast path for known patterns (sub-ms), (2) LLM-generated SQL in sandbox for novel queries (3-6s), (3) Five-level fallback when SQL fails (approximate answer, related data, suggest reframe, explain gap). Each tier has independent error handling.

### The User: Practical Value
Never says "I can't." Known queries are instant. Novel queries get best-effort with confidence scores. Failed queries get helpful guidance ("Try asking about a specific person or date range"). Builds trust incrementally.

### The Auditor: Chaos Test Strategy
Tier 1 failures fall to Tier 2 (no user impact). Tier 2 failures fall to Tier 3 (degraded but helpful). Total system failure requires all 3 tiers to fail simultaneously. Cost capped at every tier. Confidence scores let users calibrate trust.

**Excellence Score: 13/15** (Raw) | **12/15** (Verified)
- Integrity: 5/5 | Resilience: 4/5 | Delight: 4/5
- CoVe: 9 claims | 7 confirmed | 1 refuted | 1 unverifiable

---

## Recommendation: The Synthesizer

**Rationale**: Only approach that scores above 10/15 verified. Combines the safety of The Fortress (Tier 1) with the power of The Avant-Garde (Tier 2) and adds graceful degradation (Tier 3) that neither standalone approach provides. The "never say can't" UX pattern from research directly influenced this design.

**Unbreakable Strategy**: Each tier is independently testable. Tier 1 has existing regression tests. Tier 2 gets a test suite of 50 known-good queries with expected SQL. Tier 3 gets golden-file tests for each fallback level.

**Varnish Checklist**: Confidence scores on every answer. "How I found this" expandable section. Response time indicator (instant vs "thinking..."). Suggest related questions after each answer.

## Interrogation Questions
1. Should Tier 2 (LLM SQL) have a user-visible confidence threshold below which it falls to Tier 3 instead of showing a possibly-wrong answer?
2. Do you want the 5-level fallback to be visible to you (educational) or invisible (just shows best available answer)?
3. Should the system learn from your corrections (e.g., if you rephrase a failed query and it works, store the mapping)?

## Token Savings
- Intelligence: ~7k tokens (4 parallel agents)
- Main session: ~3k tokens (orchestration)
- Net savings vs inline: ~4-7k tokens

---
---

# Track B Example (Skeleton-to-Spec)

Continuing from the Synthesizer recommendation above. User approved The Synthesizer and requested a full spec.

--- Phase 5 Complete ---

## Skeleton: Tiered Intelligence Query System

### Architecture
```
User Query → QueryClassifier (Haiku) → Tier Router
  ├── Tier 1: RegexMatcher → ParameterizedBQ → Response
  ├── Tier 2: SchemaInjector → LLM SQL Gen → dry_run → Execute → Response
  └── Tier 3: FallbackChain → [approximate | related | reframe | explain_gap]
```

### Components
| Component | Purpose | Input | Output | Dependencies |
|---|---|---|---|---|
| QueryClassifier | Route to correct tier | raw query string | {tier: 1\|2\|3, confidence, extracted_params} | Haiku API |
| RegexMatcher | Tier 1 fast path | query + params | parameterized BQ method name | existing 5 methods + new methods |
| SQLGenerator | Tier 2 LLM path | query + schema | SELECT-only SQL string | Sonnet API, BQ schema |
| SQLValidator | Validate before execute | SQL string | {valid: bool, error: string} | BQ dry_run API |
| FallbackChain | Tier 3 degradation | failed query + context | best-effort response at highest available level | Vector search, BQ metadata |

### Integration Points
- [ASSUMPTION: Haiku is fast enough for classification at <200ms]
- [DECISION: Should SQLGenerator use Sonnet or Haiku? Sonnet for accuracy vs Haiku for speed]

--- Phase 6 Complete (Reviewers) ---
--- Phase 7 Complete (Intent Filter) ---

Accepted: "Add circuit breaker — if Tier 2 fails 3x consecutively, route directly to Tier 3 for 5 minutes."
Rejected: "Add Kafka for event streaming between tiers." (Enterprise pattern for 3 in-process function calls.)
Flagged: "Should Tier 2 have a confidence threshold?" (Forwarded to user — matches Interrogation Question #1.)

--- Phase 8 Complete (Refinement) ---
--- Phase 9 Complete ---

## Definition of Done
- [ ] Tier 1: All 5 existing methods still pass + 10 new methods with tests
- [ ] Tier 2: 50-query test suite with expected SQL, >80% match rate
- [ ] Tier 3: Golden-file tests for all 5 fallback levels
- [ ] Circuit breaker: Integration test for 3x failure → Tier 3 routing
- [ ] QueryClassifier: Benchmark confirms <200ms p95 latency
- [ ] End-to-end: 10 representative queries produce correct tier routing
