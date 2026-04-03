# Prompt Evaluation Rubric — AI-First SIR

## Purpose

Used by Mode 4 (Scoring Agent) to evaluate an AI-generated SIR against a benchmark record. The benchmark record (`benchmark/{site_id}.json`) is the ground truth source. Each dimension is scored independently, then penalties are applied on top.

**When a corrections record exists** (`benchmark/{site_id}_*_corrections.json`): use it as the primary signal for Remote Fact Coverage and Unknown Handling — corrections are ground truth for what was findable vs field-only.

**When no corrections record exists**: score against benchmark `expected_*` fields only. Treat the result as an estimate.

---

## Dimensions

### 1. Remote Fact Coverage — 25 points

**What it measures:** Did the AI find publicly available facts through remote research?

**Scoring:** Start at 25. For each `remote_miss` in the corrections record, deduct:

| Severity | Trigger | Deduction |
|---|---|---|
| High | Fact was in published ordinance or code; directly affects permit path, cost, or go/no-go (e.g., overlay districts, SP trigger mechanism, required process steps) | -5 |
| Moderate | Fact was in published fee schedule or published guidance (e.g., health fee, signage rules, pre-submittal requirements) | -3 |
| Low | Fact required interpretation of multiple published sources | -2 |

If no corrections record exists, evaluate each `expected_*` field in the benchmark: deduct -3 for each field that is absent or materially wrong in the SIR.

**Floor:** 0. Cannot go negative from this dimension alone.

---

### 2. Authority & Process Accuracy — 20 points

**What it measures:** Correctness of the authority chain and permit path modeling.

#### Authority chain — 10 pts

Score proportionally across all authorities in `expected_authorities`:
- Each required authority correctly identified with correct role and level: proportional credit
- Missing a non-city blocking authority (State Fire Marshal, County Health, Metropolitan Council SAC, or equivalent): **-5 per missing authority**
- Incorrectly asserting an authority has jurisdiction when it does not (e.g., MA State FM on Group E): **-3**
- City-only authority chain when state or county authority is required: **-5**

#### Permit path — 10 pts

| Item | Points |
|---|---|
| Correct permit type by name and submittal platform | 2 |
| Pre-Submittal Meeting identified as a blocking sequential step (if `pre_submittal_meeting_required: true` in benchmark) | 3 |
| Neighborhood Meeting identified if required by benchmark | 2 |
| All `blocking_dependencies` from benchmark represented in the report | 3 |

Deduct:
- **-3** for each blocking dependency in the benchmark that is absent from the report
- **-3** for listing a sequential blocking dependency as concurrent
- **-2** for correct dependencies present but not modeled in the timeline (mentioned in text only)

---

### 3. Code & Calculation Accuracy — 20 points

**What it measures:** Correctness of code framework citations and all quantitative calculations.

#### Code framework — 10 pts

| Item | Points |
|---|---|
| Correct operative building code edition (780 CMR for MA; FBC for FL; state-amended IBC with correct edition and state for MN, TN, etc.) | 4 |
| Correct fire code | 2 |
| Correct plumbing code | 1 |
| Correct state administrative codes (EEC 606 CMR, FAC, MN 429.2, etc.) | 2 |
| Local amendments correctly noted or correctly noted as absent | 1 |

**Note:** Citing "IBC" without identifying the state-amended version is a partial answer, not a correct one, for states that amend IBC. It earns 1 of the 4 building code points.

#### Calculations — 10 pts

| Item | Points |
|---|---|
| Occupant load calculated correctly for suite area at Group E rate | 3 |
| Sprinkler trigger correctly identified (12,000 SF fire area or building-level threshold, whichever applies) | 2 |
| Egress requirements (exit count, stair width minimum, travel distance limit) correctly derived from occupant load | 2 |
| Bathroom fixture count correctly estimated from occupant load and applicable code | 1 |
| E-Occupancy Score and rating consistent with benchmark value (within ±5 points) | 2 |

---

### 4. Unknown Handling — 10 points

**What it measures:** Did the AI correctly distinguish what it knows from what it doesn't? Overconfidence is the primary failure mode.

**Scoring:** Start at 10. Deduct:

| Failure | Deduction |
|---|---|
| Assigned B or A to a fact that corrections record shows was materially wrong (overconfidence) | -3 per instance |
| Additional deduction when the wrong answer was presented as actionable (e.g., internet availability stated as multi-provider confirmed when building has no fiber connection) | -2 additional per instance |
| Assigned D to a fact that was clearly findable from published sources (under-research) | -1 per instance |

**Correct behavior:** Assigning D to items that benchmark confirms as `field_only` earns no deduction. This is expected behavior.

**The tension:** An AI that assigns D to everything scores 10/10 here but near 0 on Remote Fact Coverage. The rubric penalizes both extremes by design.

---

### 5. Vendor Task Quality — 15 points

**What it measures:** Are field tasks complete, correctly targeted, and actionable?

#### Coverage — 9 pts

Score proportionally across all tasks in `expected_vendor_tasks` in the benchmark:
- Each expected task present in the report: proportional credit toward 9 pts
- Missing a critical task (stair width, travel distance, exit count, sprinkler status when site is above ground floor): **-3 per missing**
- Missing a standard task: **-1 per missing**

#### Quality — 6 pts

For each vendor task card present (up to 6 cards scored):
- Card includes: what to do, why it matters, what good looks like, specific datapoint name(s): **1 pt**
- Generic or vague instruction without specific measurement guidance: **0 pts** for that card

---

### 6. Output Structure — 10 points

**What it measures:** Are all required sections present and correctly formatted?

| Item | Points |
|---|---|
| Executive Summary with recommendation, E-Occ score, and top timeline risk | 2 |
| All required "What We Know Remotely" subsections present — including sewer, water, signage, and overlay districts as separate line items | 3 |
| "What We Do Not Know Yet" section present with gap-to-decision mapping | 1 |
| Confidence labels (A/B/C/D) on all table rows | 2 |
| Fee and cost table with TBD items separated from known items | 1 |
| Timeline dependency table present | 1 |

Deduct **-1** for each required subsection absent: sewer, water, signage, and overlay districts each count separately.

---

## Penalties

Applied after dimension scoring. Can reduce total below zero.

| Penalty | Trigger | Deduction |
|---|---|---|
| Wrong operative code | Citing IBC as the sole controlling code for MA, FL, or MN (states that use state-amended codes with different numbering or additional sections) | -10 |
| Missing blocking non-city authority | Omitting a non-city authority that blocks permit issuance: TN State FM, MN Metropolitan Council SAC, or equivalent identified in benchmark | -10 |
| Missing egress tasks on upper-floor site | No stair width, travel distance, or exit count tasks when site is above ground floor | -10 |
| Critical overconfidence | Assigning B or A to a fact that corrections record shows was materially wrong (stacked on top of Unknown Handling deduction) | -5 per instance |
| Wrong SP trigger mechanism | Identifying SP as use-type-driven when benchmark shows SF-threshold-driven, or vice versa | -5 |
| Missing sewer/water sections | Both sewer and water absent as output sections | -5 |

A **major penalty** is any single penalty ≥5 points. Count and list these separately in the SCORE_RECORD output.

---

## Band Definitions

| Score | Band | Meaning |
|---|---|---|
| 90–100 | OPERATIONAL | Ready for production use with minimal human review |
| 75–89 | NEEDS REVIEW | Usable with human spot-check on flagged gaps |
| 60–74 | PARTIAL | Significant gaps; requires substantial human augmentation |
| <60 | NOT OPERATIONAL | Do not use without full human redo |

---

## Scoring Procedure

1. Load `benchmark/{site_id}.json`
2. Load corrections record if present: `benchmark/{site_id}_*_corrections.json`
3. Score each dimension against benchmark `expected_*` fields and corrections classifications
4. Sum dimension scores (max 100)
5. Subtract penalties
6. Identify major penalties (≥5 pts each)
7. Output scorecard and `SCORE_RECORD` line per Mode 4 format
