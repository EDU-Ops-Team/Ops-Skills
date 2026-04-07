# AI-First SIR Skill

## Mode Routing

Determine which mode to run from the invocation:

| Invocation | Mode |
|---|---|
| `<address>` or address only | Mode 1 — SIR Generation |
| `vendor-packet <sir_file>` | Mode 2 — Vendor Packet |
| `compare <ai_file> <vendor_file>` | Mode 3 — Comparison Agent |
| `score <sir_file> <site_id>` | Mode 4 — Scoring Agent |
| `loop` | Mode 5 — Loop Controller |

Read the invocation and jump directly to the relevant mode. Do not run multiple modes in one invocation unless explicitly asked.

---

## Mode 1: SIR Generation

### Purpose

This skill runs before vendor kickoff and attempts to recover as much decision-critical and vendor-useful information as possible from:

- the site address
- public web research
- assessor, zoning, permit, and code sources
- environmental and geographic databases
- jurisdiction contacts and fee schedules

The output is an AI-first Site Investigation Report that:

1. tells the team whether the site is worth advancing
2. gives vendors a focused scope for what still must be verified on-site
3. reduces wasted vendor effort on information that can be found remotely

## Primary Outcome

Produce a single report that combines:

- SIR-style jurisdiction and permit research
- site-evaluator-style conversion feasibility and code trigger analysis
- vendor-ready task cards for all field-only unknowns

## Required Inputs

- site address
- intended use: Group E school / daycare / educational occupancy
- optional known facts from the user

## Core Principles

### 1. AI First, Vendor Second

The agent must exhaust remote research before assigning field tasks.

### 2. Known vs Unknown Must Be Explicit

Every material fact must be labeled using the single confidence system below. Use these letter codes in all report sections and tables.

- **A — Verified**: From authoritative source (county assessor, FEMA, permit records, staff confirmation)
- **B — High-confidence inferred**: From reliable source (business listing, satellite imagery, published municipal code, fee schedule)
- **C — Inferred/Estimated**: Calculated values, independent online research without staff confirmation, visual observations
- **D — Unknown**: Requires verification (remote-researchable or field-only)

### 3. Existing Occupancy Comes First

Determine current use and likely occupancy early. Existing educational use or prior Group E alignment can collapse the process and shorten the report.

### 4. Authority Chain Is Mandatory

Never assume the city alone controls the process. Research the full authority chain:

- city
- county
- state
- special districts
- road / DOT jurisdiction
- health authority
- state fire marshal
- education / childcare administrative regulators

### 5. Timeline Is a Dependency Graph

Do not list timelines as isolated numbers. Identify:

- concurrent reviews
- blocking approvals
- post-construction steps
- prerequisites to permit issuance
- prerequisites to operations

### 6. Vendor Tasks Must Be Gap-Driven

Every field task must exist because a critical question cannot be answered remotely.

## Research Workflow

### Phase 1: Site Identity and Existing Use

Recover:

- parcel ID
- assessor use code
- gross area
- story count
- year built
- lot size
- current tenant / vacancy status
- existing occupancy classification
- evidence of prior school or childcare use

### Phase 2: Authority Chain

Document all relevant authorities with contact info where available:

- planning / zoning
- building / permits
- fire — research whether the jurisdiction's fire department or state fire marshal accepts pre-submittal inquiries. Some fire authorities (e.g., Minneapolis Fire Department) refuse all pre-submittal engagement and will not provide code guidance, routing, or sprinkler requirements before a formal submittal. When pre-submittal refusal is confirmed or indicated, assign D to fire code framework, fire routing, and all fire protection findings — do not fill with code citations inferred from the state's base code. If the base fire code is publicly known, it may be cited at C confidence with an explicit note that the fire authority has not confirmed it and will not engage pre-submittal.
- health — when the health authority is a county or state health department, (1) look up the published fee schedule before assigning D — most state and county DOH offices publish plan review and inspection fees online; and (2) check for public records access restrictions. Some county health departments restrict FOIA access to state residents only (e.g., Williamson County, TN). When access is structurally restricted, health requirements cannot be obtained through normal remote research — flag all health findings as D-confidence and note the access restriction explicitly. FL FDOH county offices: default timeline estimate is **4–8 weeks** (not 8–12 weeks) — confirmed 5 weeks (1 week if immediate approval) for Palm Beach County.
- public works / engineering
- sanitary sewer / water
- state fire marshal
- county or state education / childcare regulators
- DOT / road authority
- historic or overlay authority

### Phase 3: Code Framework

Identify the controlling framework, not just a generic IBC reference:

- building code edition
- fire code edition
- plumbing code
- health / food service rules
- state education or childcare administrative rules — **for FL K-12 private schools**: always cite FAC 6A-2.0040 (Sanitation Standards in K-12 Private Schools) and FAC 4E-11 (Rules for Food Hygiene). FAC 65C-22 governs childcare licensing — cite it only if the program is licensed as childcare. For K-12 programs, 6A-2.0040 and 4E-11 are operative in addition to or instead of 65C-22.
- local amendments where applicable

### Phase 4: Zoning and Use Permission

Determine:

- base zoning district and classification
- **overlay districts** — explicitly search the jurisdiction's published zoning map and code overlay tables. Do not report "None" without checking the overlay map. Overlays (transit areas, small business zones, design districts, flood overlays) frequently drive separate parking, use, and design requirements independent of the base zone. If the jurisdiction publishes an interactive map, look for overlay layers.
- by-right / conditional / special use / prohibited for the intended educational use
- **SF-based Special Permit or conditional use triggers** — check for floor area thresholds that trigger review regardless of use type (e.g., ">5,000 SF leasable area requires Special Permit for any use"). This is separate from use-type triggers. Report both if both exist.
- parking requirements — include any overlay-driven adjustments to the base zone standard
- queuing or drop-off constraints
- **signage** — check the zoning ordinance signage section. Above-ground-floor restrictions are common and affect school identification and EEC licensing. Report permitted sign types and any floor-based restrictions.
- whether planning review is waived, sign-off only, or required

### Phase 5: Permit Path

Determine:

- permit type by name
- submittal platform
- **Pre-Submittal or Pre-Application Meeting** — research explicitly whether the jurisdiction requires a pre-submittal or pre-application meeting before a Special Permit, conditional use, or change-of-use application can be filed. This is a sequential blocking step (typically 2–6 weeks) that must appear on the critical path. It is commonly listed in the planning department's online submittal instructions. Do not omit it from the timeline if found.
- **Scope-conditional requirements** — when a published rule is conditioned on a trigger (e.g., "required for development orders," "required when footprint changes"), research explicitly what activates that trigger for the proposed scope. Interior-only work with no footprint or parking changes frequently does not trigger planning review, development orders, or pre-application meetings. State the condition explicitly rather than asserting the requirement as absolute.
- **Neighborhood Meeting requirement** — some jurisdictions require an applicant-hosted neighborhood meeting between the pre-submittal meeting and application filing. If found, add it to the critical path with its minimum timeline.
- best-case timeline (include all sequential blocking steps, not just review time)
- worst-case timeline
- fee formula and known fees — **always compute a specific dollar estimate, not just the formula.** For building permit fees: access the jurisdiction's published fee schedule or permit calculator; apply it to a benchmark construction value of $100,000–$200,000 (typical K-8 interior renovation scope); report both the formula and the computed range. For state fire marshal plan review fees: apply the published formula to the same benchmark range. Additionally, if the site is a large building (>10,000 SF) where life-safety upgrades (sprinklers, restroom additions, egress work) are likely based on Phase 7 feasibility analysis, also compute fees at a site-scaled estimate of $300,000–$500,000 to reflect the probable actual construction scope. Report both ranges. Report "TBD (formula: X% of construction cost; estimated $Y–$Z at $100K–$200K light scope; $Y2–$Z2 at $300K–$500K heavy scope)" rather than "TBD" alone.
- certificate of occupancy or business tax / operating license path
- blocking dependencies

### Phase 6: Environmental and Geographic

Research:

- FEMA flood zone
- contamination records
- UST records
- historic district status
- asbestos / lead risk from age
- seismic category if available
- tornado / storm shelter implications where relevant

### Phase 7: School Conversion Feasibility

Calculate or infer:

- occupancy compatibility tier
- fire area
- sprinkler trigger
- below-grade trigger
- occupant load
- 50-person threshold implications
- bathroom requirements
- likely code cost items
- conversion difficulty tier
- E-Occupancy Score and rating (calculate per rubric in `references/site-eval-brainlift.md`)
- construction scope checklist: for each code trigger identified above, state the physical work required and its cost range using the Construction Scope Inference table in `references/site-eval-brainlift.md`. Order by cost, largest first. Flag items that cannot be confirmed remotely as "Unknown until field visit."
- timeline posture: using the timeline range from Phase 5, determine SHORT, MIDRANGE, or LONG. Count the following SHORT indicators minus LONG indicators — net positive = SHORT, net zero (±1) = MIDRANGE, net negative = LONG. SHORT indicators: no planning review required, no pre-submittal meeting required, fire authority accepts pre-submittal engagement, single primary blocking track, 0–1 D-confidence items on critical-path steps, building already Group E. LONG indicators: CUP or SUP required, pre-submittal or neighborhood meeting required, fire authority refuses pre-submittal engagement, 2+ sequential blocking tracks, 3+ D-confidence items on critical-path steps, occupancy change required. State the posture and list 2–3 plain-English reasons.
- cost posture: determine BELOW TYPICAL, TYPICAL, or ABOVE TYPICAL for a K-8 conversion. BELOW TYPICAL indicators: building already Group E, fire area under 12K SF with no basement, post-1980 construction, existing school infrastructure in place. ABOVE TYPICAL indicators: occupancy change required from incompatible group (S, R, I), sprinkler retrofit triggered, pre-1960 building with probable hazmat, egress deficient or entirely unknown, CUP/SUP fees. Count BELOW minus ABOVE indicators using same net scoring. State the posture and list 2–3 plain-English bullet reasons covering the biggest cost drivers or cost reducers.

### Phase 8: Site Operations and Infrastructure

Research where possible:

- traffic study trigger logic
- pickup / drop-off constraints
- access and stacking issues
- road jurisdiction
- **sanitary sewer** — identify the servicing utility, research whether capacity constraints apply, and note any connection fee trigger (e.g., fixture count changes). Assign D if a staff call is required for capacity confirmation.
- **water** — identify the servicing utility and meter/line notes. Note any fee triggers. Assign D if capacity must be confirmed via staff.
- internet provider availability — **confidence discipline required**: area coverage ≠ building connection. Do not report above C confidence without direct provider rep confirmation that this specific building has an active fiber or broadband connection. If building-level service status cannot be confirmed remotely, assign D and flag as requiring provider outreach. Specifically note if fiber is available in the area but connection to the building is unverified.
- food service / kitchen requirements

### Phase 9: Field Verification Plan

Generate vendor task cards only for unknowns that materially affect:

- go / no-go
- permitability
- life safety
- budget
- timeline

## Output Contract

The final report must contain these sections.

### 1. Executive Impact Summary

This section appears first — before all other sections. It is written for executive stakeholders who need straight-to-the-point answers. Use plain language at no higher than an 8th-grade reading level. No sentences over 20 words where avoidable. No code jargon without a plain-English explanation in the same sentence.

Include these six subsections in order:

**Score at a Glance**
One line: score, color band, recommendation. Then one plain-English sentence that says what the score means. Examples:
- GREEN/PROCEED: "This building has no known barriers to opening a school. The work ahead is routine."
- YELLOW/CAUTION: "This building can work, but several unknowns need answers before you commit."
- ORANGE/JUSTIFICATION: "This building has serious problems. Only move forward if the business case is strong enough to justify extra cost and time."
- RED/PASS: "This building is unlikely to work as a school. Walk away unless the situation changes."

**What Must Be Built**
A checkbox checklist of construction and renovation scope items inferred from Phase 7 code triggers and Phase 8 infrastructure gaps. Use the Construction Scope Inference table in `references/site-eval-brainlift.md`. Order by cost, largest first. Each item states WHAT and WHY in one plain-English line. After the list, add one sentence noting any unknowns that could expand scope once the building is inspected.

Format: `- [ ] [Item] — [Plain-English reason: e.g., "required because the building is over 12,000 SF"]`

**Permits and Approvals**
A numbered sequential list of every permit, review, and approval required — in the order they must happen. Each entry names the authority and states in plain English whether it must happen before or at the same time as another step.

**Timeline Outlook**
State the report's best-case and worst-case range, then state the posture (SHORT / MIDRANGE / LONG) and give 2–3 bullet reasons in plain English. Example: "This site skews toward the SHORT end because no planning review is needed, the fire department will talk to you before you submit plans, and the building is already permitted as a school."

**Cost Outlook**
State the permit fee range and estimated buildout range, then state the cost posture (BELOW TYPICAL / TYPICAL / ABOVE TYPICAL) and give 2–3 bullet reasons in plain English covering the biggest cost drivers or reducers.

**Risk Watch**
A table of items that could blow up the timeline or budget. Each row states what could go wrong, what happens if it does, and how likely it is. Use four likelihood levels only: Unknown until field visit, Low, Moderate, High.

| Risk | What happens if it goes wrong | Likelihood |
|---|---|---|

---

### 2. Executive Summary

Include:

- address
- existing use / likely occupancy
- overall recommendation
- top 1-3 decision-driving facts
- score / rating
- main timeline risk

### 3. What We Know Remotely

Structured tables for:

- site and building facts
- authority chain
- code framework
- zoning and planning (must include: base zone, overlay districts, SP triggers, parking, signage)
- permit path and timelines (must include: pre-submittal meeting if required, neighborhood meeting if required, all blocking sequential steps)
- environmental and geographic findings
- site operations and infrastructure (must include: sewer, water, internet)
- feasibility calculations and cost estimates

### 4. What We Do Not Know Yet

Structured list of unresolved items with:

- why they matter
- what decision they block
- whether the gap is remote-researchable or field-only

### 5. Critical Vendor Tasks

Self-contained task cards for deal-killer unknowns such as:

- travel distance
- exits and exit separation
- corridor widths
- dead-end corridors
- construction type
- existing fire protection
- restroom counts
- ADA features
- kitchen and food-service infrastructure
- pickup / drop-off circulation observations

### 6. Timeline and Dependency Table

This IS the Flag Page's Permit Review Time Summary table from `references/sir-report.md`. Do not produce a separate timeline table — the Flag Page table with its Concurrent/Blocking columns serves as the dependency map.

### 7. Fee and Cost Table

Separate:

- known jurisdictional fees
- known code-trigger costs
- estimated buildout range
- `TBD` items

### 8. Recommendation

One of:

- `PROCEED`
- `PROCEED WITH CAUTION`
- `REQUIRES JUSTIFICATION`
- `PASS`

### 9. File Output

Save the completed report to:

```
reports/{address-kebab-case}_{YYYY-MM-DD}.md
```

Example: `reports/123-main-st-boston-ma_2026-04-02.md`

Create the `reports/` directory if it does not exist. Print the saved path on completion.

## Hard Rules

1. Never guess construction type.
2. Never collapse city, county, and state authority into one assumed AHJ.
3. Never present a timeline without dependencies.
4. Never omit confidence labels.
5. Never omit egress-related vendor tasks.
6. Never generalize food service requirements across jurisdictions.
7. If contamination appears active at or adjacent to the site, flag it as a likely fatal issue.
8. If a staff answer is unavailable or contradictory, fall back to published code, ordinances, fee schedules, and public records.
9. Separate remote facts from field-only unknowns.
10. The report must be useful both to decision-makers and to vendors.
11. Every table must include a Confidence column (A/B/C/D). No table row may be written without a confidence label.
12. Never report overlay districts as "None" without explicitly checking the jurisdiction's published overlay map or overlay code table.
13. Never report internet service above C confidence without direct building-level provider confirmation. Area coverage alone is C at best; without rep confirmation of building connection, assign D.
14. Never omit sewer and water as output sections. Assign D if capacity requires a staff call — but the section must always be present.
15. Never omit a Pre-Submittal Meeting from the permit path critical path if the jurisdiction requires one. Research this explicitly for every Special Permit or conditional use path.
16. Never assert a scope-conditional requirement as absolute without researching what triggers the condition for the proposed scope. State the trigger condition, then assess whether the proposed scope meets it.
17. Never assign fire code framework, fire routing, or fire protection requirements above D confidence when the AHJ fire department has a confirmed or indicated policy of refusing pre-submittal engagement. The base state or national fire code may be cited at C confidence only when explicitly noted as unconfirmed by the fire authority. Research this pre-submittal engagement policy explicitly for every jurisdiction.

## Minimum Quality Bar

The report is complete only when:

- every major authority has been researched or explicitly marked unknown
- every major section has at least one concrete finding or explicit gap statement
- all calculations are shown or explained
- vendor tasks are tied to real unresolved risks
- timeline and fee tables are present
- the recommendation is consistent with the evidence

## Not In Scope

- final architectural design
- final code compliance certification
- field measurements presented as facts without verification
- legal advice

---

## Mode 2: Vendor Packet

Transform a completed AI SIR into the vendor-facing version. The vendor receives a focused document — not the raw AI output.

### Input

- Path to an AI-generated SIR (output of Mode 1)

### Transformation Rules

Read the SIR file. Apply these rules to every table row and finding:

**Confidence A:** Keep value as-is. No source field needed. These are authoritative facts.

**Confidence B or C:** Rewrite the value cell as:
> AI found: [original value] — please verify

Add an empty `Source` column to the right of the `Confidence` column. Vendor fills this in.

Embed the original claim ID as an HTML comment immediately after the row so it survives round-trip:
```
<!-- claim-id: R-047 -->
```

**Confidence D (vendor task cards):** Leave unchanged. These are already field tasks.

### Cover Note

Prepend this block to the vendor packet:

---

> **How to use this report**
>
> Section 1 tables are pre-populated from remote research. Rows marked **"AI found: X — please verify"** need your independent confirmation — fill in the Source column with where you verified it (e.g., "FL statute 633.202", "staff call 4/3", "observed on site"). Rows with a plain value in the Confidence A column are from authoritative sources and do not need re-verification unless something looks wrong.
>
> Section 2 task cards are your field scope. Complete each one.
>
> Do not leave Source blank on any row you actively verified — that field is how we know the difference between "confirmed" and "carried forward."

---

### Output

Write the vendor packet to the same directory as the input file, named:
`{original_filename}_vendor-packet.md`

---

## Mode 3: Comparison Agent

Diff an AI pre-report against a vendor-completed return. Output structured corrections.

### Input

- Path to AI pre-report (the original Mode 1 output, not the vendor packet)
- Path to vendor-completed return

### Process

**Step 1 — Match sections**

Both documents follow the same template structure. Match section by section, table by table, row by row using section headers and datapoint names as keys.

**Step 2 — Classify each B/C row**

For every B/C row in the AI report, find the corresponding vendor return entry and classify:

| Vendor entry | Classification | Benchmark signal |
|---|---|---|
| Source filled + same value | `confirmed` | Strong — update expected value |
| Source blank + same value | `confirmed_unsourced` | Weak — do not update benchmark |
| Source filled + different value | `remote_miss` | Strong — AI got it wrong, update benchmark |
| Source blank + different value | `corrected_unsourced` | Ambiguous — flag for human review |
| Left blank (not verified) | `unverified` | Skip |

**Step 3 — Classify D item completions**

For every vendor task card the vendor completed, classify as `field_only`. These are expected unknowns — they validate the task card but do not penalize the AI.

**Step 4 — Identify new findings**

If the vendor return contains a populated field that does not correspond to any AI claim (the AI had no entry for it), classify as:
- `remote_miss` if the vendor noted a public source
- `field_only` if the vendor noted field observation as the source

**Step 5 — Output**

Write corrections to `benchmark/{site_id}_{date}_corrections.json` following the schema in `references/corrections-schema.md`.

Print a summary:

```
Site: {site_id}
Remote misses:        {n}  ← AI should have found these
Field-only new:       {n}  ← legitimate unknowns
Confirmed (sourced):  {n}  ← AI was right, verified
Confirmed (unsourced):{n}  ← unverified, excluded from benchmark
Ambiguous:            {n}  ← needs human review
Benchmark updates:    {n}
```

---

## Mode 4: Scoring Agent

Score an AI-generated SIR against a benchmark record.

### Input

- Path to an AI-generated SIR (Mode 1 output)
- `site_id` — used to load `benchmark/{site_id}.json`

### Process

Load the benchmark record. Score the SIR against it using the six rubric dimensions from `references/prompt-eval-rubric.md` (Remote Fact Coverage, Authority and Process Accuracy, Code and Calculation Accuracy, Unknown Handling, Vendor Task Quality, Output Structure).

Apply penalties per the rubric.

### Output

Print scores in this format:

```
Site: {site_id}
─────────────────────────────────────
Remote Fact Coverage          [n]/25
Authority & Process Accuracy  [n]/20
Code & Calculation Accuracy   [n]/20
Unknown Handling              [n]/10
Vendor Task Quality           [n]/15
Output Structure              [n]/10
─────────────────────────────────────
Subtotal                      [n]/100
Penalties                     -[n]
─────────────────────────────────────
TOTAL SCORE                   [n]/100
Band: [90-100 OPERATIONAL / 75-89 NEEDS REVIEW / 60-74 PARTIAL / <60 NOT OPERATIONAL]
─────────────────────────────────────
Major penalties triggered:
  [list each major penalty with brief reason, or "None"]
Top 3 improvement opportunities:
  [list specific gaps tied to benchmark expected values]
```

Also append a machine-readable line for log parsing:
```
SCORE_RECORD	{site_id}	{total_score}	{band}	{major_penalty_count}
```

---

## Mode 5: Loop Controller

Autonomously iterate on Mode 1 (SIR Generation) to improve benchmark scores. This mode modifies `SKILL.md` — specifically the Mode 1 section. All other sections and all reference files are read-only.

### Setup (first run only)

1. Identify train sites: all `.json` files in `benchmark/` not marked `"holdout": true`
2. Identify holdout sites: all `.json` files marked `"holdout": true`
3. Run Mode 4 on each train site using current SKILL.md to establish baseline scores
4. Log baseline to `benchmark/results.tsv` (tab-separated):
   ```
   commit	site_id	total_score	major_penalties	description
   ```
5. If `results.tsv` already exists with a baseline row, skip to the experiment loop.

### Experiment Loop

LOOP FOREVER:

1. Review the most recent `remote_miss` corrections across all train sites. Identify the most frequent or highest-penalty gap category.
2. Propose one targeted change to the Mode 1 section of `SKILL.md`. One change at a time — a new instruction, a refined phase step, an additional hard rule, or a removed ambiguity.
3. Apply the change to `SKILL.md`.
4. Run Mode 1 on all train sites. Run Mode 4 on each output to score against benchmarks.
5. Calculate mean train score before and after.
6. **Keep** the change if:
   - Mean train score improved, AND
   - No major penalty count increased
7. **Revert** the change (restore prior SKILL.md) if the above conditions are not met.
8. Log the result to `benchmark/results.tsv`.
9. **Promote to holdout check** only if: the change was kept AND mean train score improved by ≥3 points. Run Mode 4 on all holdout sites. Keep the change only if holdout score also improves and no new major penalties appear.
10. Never stop. Never ask for confirmation. If no obvious improvement target exists, re-read all corrections files and look harder.

### What the Loop Must Not Touch

- Any section of `SKILL.md` other than Mode 1
- Any file in `references/`
- Any file in `benchmark/`
- The Mode Routing section

### Simplicity Criterion

A change that adds complexity for a gain under 2 points is not worth keeping. A change that removes text and holds or improves the score is always worth keeping.
