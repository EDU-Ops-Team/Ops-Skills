# Merged AI-First SIR Skill Spec

## Purpose

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
- fire
- health
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
- state education or childcare administrative rules
- local amendments where applicable

### Phase 4: Zoning and Use Permission

Determine:

- by-right / conditional / special use / prohibited
- parking or queuing constraints
- overlay or district restrictions
- whether planning review is waived, sign-off only, or required

### Phase 5: Permit Path

Determine:

- permit type by name
- submittal platform
- best-case timeline
- worst-case timeline
- fee formula and known fees
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

### Phase 8: Site Operations and Infrastructure

Research where possible:

- traffic study trigger logic
- pickup / drop-off constraints
- access and stacking issues
- road jurisdiction
- utilities and capacity notes
- internet provider availability
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

### 1. Executive Summary

Include:

- address
- existing use / likely occupancy
- overall recommendation
- top 1-3 decision-driving facts
- score / rating
- main timeline risk

### 2. What We Know Remotely

Structured tables for:

- site and building facts
- authority chain
- code framework
- zoning and planning
- permit path and timelines
- environmental and geographic findings
- site operations and infrastructure
- feasibility calculations and cost estimates

### 3. What We Do Not Know Yet

Structured list of unresolved items with:

- why they matter
- what decision they block
- whether the gap is remote-researchable or field-only

### 4. Critical Vendor Tasks

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

### 5. Timeline and Dependency Table

This IS the Flag Page's Permit Review Time Summary table from `references/sir-report.md`. Do not produce a separate timeline table — the Flag Page table with its Concurrent/Blocking columns serves as the dependency map.

### 6. Fee and Cost Table

Separate:

- known jurisdictional fees
- known code-trigger costs
- estimated buildout range
- `TBD` items

### 7. Recommendation

One of:

- `PROCEED`
- `PROCEED WITH CAUTION`
- `REQUIRES JUSTIFICATION`
- `PASS`

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
