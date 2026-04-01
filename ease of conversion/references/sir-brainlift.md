# SIR Jurisdiction Caller — Jurisdiction Research for Site Investigation Reports

**Problem:** Alpha School's site expansion is bottlenecked by jurisdiction research (15-25 hrs/site, 27 sites in pipeline). Target: <5 hrs/site via 60-80% agent automation, timeline predictions within ±2 weeks, scale to 10+ SIRs/month.

---

## Core Strategic Insights

**Existing Occupancy = 80% of Workload**: A building already Group E or operating as a school collapses the entire process. Boston E-to-E: 11-week Short Form, $10 health. Boca school-to-school: 2-4 week interior alteration. Minneapolis multi-tenant: 19 weeks, $1,117+. Determine existing occupancy FIRST—it dictates a 3-page vs 15-page report.

**Information Extraction is Adversarial, Not Cooperative**: Staff are gatekeepers. Boston Inspection Services: completely unresponsive—CDS independently researched Short Form permit. Brentwood: FOIA restricted to TN citizens. Minneapolis Fire: refused preliminary comments. Assume resistance. Have fallbacks ready: municipal code, online portals, fee PDFs, public records. The real skill is asking the same question 3-4 different ways to verify answers are consistent and the person actually knows what they're talking about.

**Permit Timeline = Dependency Graph, Not Sum**: Brentwood: city+state fire concurrent but city BLOCKED until state approves. Minneapolis: SAC parallel but must pay before issuance. Boca: health concurrent with building but blocked by fire inspection + CO. Map concurrent vs sequential, identify blocking dependencies, calculate best-case (first round) and worst-case (3+ rounds + revision).

**Authority is Never Just the City**: 4/4 SIRs had hidden non-city authorities: TN State Fire Marshal, MA 780 CMR state codes, FL DOH Palm Beach County (not city), MassDOT/FDOT road jurisdiction. Map full chain—city, county, state, special districts—BEFORE department research.

**Food Service Requirements Cannot Be Generalized**: Boston catered = $10 permit, no review. Boca Raton catered = $575, full kitchen required REGARDLESS of model (hand sink <20', mop sink, refrigerator, cleanable surfaces). Minneapolis = 3-comp sink, institutional license, certified manager. The question is "what does THIS jurisdiction require for K-8?" not "what's our food model?"

**Code Framework First, Requirements Second**: 4 states, 4 systems—MN 2020 state-amended IBC, Brentwood 2024 IBC direct, Boston 780 CMR (not IBC at all), Boca 2023 FBC 8th Ed + FL Fire Prevention Code (NFPA, not IFC). State amendments add invisible provisions: MN 429.2 K-2 restrictions, FL 6A-2.0040 sanitation, FL 4E-11 food hygiene. Wrong code edition = unreliable findings.

---

## Key Insights (Action/QC)

**Existing Use First**: Research occupancy via assessor/appraiser/permit portal before any department contact. If school/E: simplified path across all sections. **QC:** Documented within 30 min.

**Map Authority Chain**: City + County (Health?) + State (code authority, State FM, education admin codes) + Special Districts (DOT, historic). **QC:** Every authority confirmed with contact info. No "assumed city."

**Code Framework**: Building code edition + fire code + state education admin codes. Check ICC Tracker + adoption ordinances. **QC:** Cited specifically. Fire code separate. State amendments noted.

**Zoning = Binary Gate**: BY RIGHT / CUP-SUP / NOT PERMITTED. CUP/SUP = longest lead-time. **QC:** Status + conditions. Not permitted = deal-breaker.

**Planning ∝ Use Match**: School-to-school = waived. Use change = layered. **QC:** REQUIRED / NOT REQUIRED / SIGN-OFF ONLY.

**Permit Type > City Size**: Identify by name—determines timeline, fees, CO process. **QC:** Type + link, best/worst, fee formula.

**Fire Routing**: (1) Through building = no timeline. (2) Separate = parallel. (3) State FM = may BLOCK city. **QC:** Routing + blocking dependency + sprinkler status.

**Health = Highest Variability**: Document requirements REGARDLESS of food model. Authority city or county. Restrooms often from health, not code. **QC:** Blocking deps noted.

**Engineering = Often TBD**: Traffic study triggered by USE CHANGE only. Flag TBD vs guess. **QC:** Trigger logic + DOT checked.

**Unresponsive Staff = Data Point**: Alt contacts → independent research → flag confidence → public records → document in Flag Page. **QC:** No blank sections.

---

## Health Requirements by Jurisdiction

| Jurisdiction | Authority Level | Food Model | Requirements | Fees | Timeline |
|---|---|---|---|---|---|
| **Boston** | City (Inspectional Services) | Catered pre-packaged | $10 Children's Feeding permit, no plan review | $10 | During building permit |
| **Minneapolis** | City (Health Dept) | On-site | 3-comp sink, hand sink, institutional license, certified manager in 45 days | $494+ | 8 wks (food plan review) |
| **Brentwood** | County (Williamson Co) | On-site | Full health permit (details unavailable—FOIA restricted) | TBD | 10 wks |
| **Boca Raton** | County (FL DOH Palm Beach) | ANY (regardless) | Kitchen area, hand sink <20', mop sink, refrigerator, cleanable surfaces. TWO reviews required. | $575 | Concurrent w/building; blocked by fire+CO |

**Restrooms (from health, not building code):** Boca: separate staff, grades 2-8 M/F, K-1 unisex ok. Boston: teachers separate every other floor, students every floor (MA Plumbing Code).

---

## Permit Process by Jurisdiction

| Jurisdiction | Permit Type | Submittal | Best/Worst | Fees | Key Dependency |
|---|---|---|---|---|---|
| **Minneapolis** | Building + SAC Determination | Accela | 9/19 wks | $449 + TBD SAC | SAC concurrent but must pay before issuance |
| **Brentwood** | Building + State FM | MyGov | 4/10 wks | $158 + $1,000 FM | State FM concurrent but BLOCKS city release |
| **Boston** | Short Form | Online | 11 wks | $20 + $10/$1K value | Staff unresponsive—type found independently |
| **Boca Raton** | Interior Alteration + BTR | eHUB | 2-4/10 wks | 1% contract + $170 BTR | BTR = 3wk post-construction process |

**CO:** Boston = occupancy-class (existing E = no new CO). Boca = BTR post-construction ($170, 3wks). Most jurisdictions: 3 review rounds, 1wk revision between. Boston: NO fast-track for Group E.

---

## Fire Review Routing

| Jurisdiction | Pattern | Timeline Impact | Key Detail |
|---|---|---|---|
| **Boston** | Through building | None | Sprinkler status confirmed via public records request |
| **Boca Raton** | Through building | None | Courtesy pre-inspection available; NFPA-based fire code |
| **Brentwood** | State FM required | +2-4 wks, +$1K | City permit WILL NOT RELEASE until state approves |
| **Minneapolis** | Unknown | Unknown | Fire Dept refused all engagement without submittal |

---

## Building Code Frameworks

| State | Code System | Key Divergence |
|---|---|---|
| **Minnesota** | 2020 MN Building Codes (state-amended IBC) | Section 429.2 adds K-2 grade restrictions absent from base IBC |
| **Tennessee** | 2024 IBC (direct adoption) | State FM separate authority for educational uses |
| **Massachusetts** | 780 CMR (state-specific, NOT IBC) | IBC references meaningless; separate Plumbing Code for restrooms |
| **Florida** | 2023 FBC 8th Ed (state-amended IBC) + FL Fire Prevention Code (NFPA 1&101) | Three parallel frameworks: building + fire + admin code (6A-2.0040, 4E-11) |

**Pattern:** Always check for state education-specific administrative codes BEYOND the building code.

---

## Staff Communication Patterns

| Pattern | Example | Fallback |
|---|---|---|
| Cooperative | Boca Raton | Standard workflow |
| Dept-specific | Minneapolis (Fire no, others yes) | Adapt per-department |
| Structurally restricted | Brentwood (FOIA = TN residents only) | Published codes + fees only |
| Unresponsive | Boston Inspection Services | Independent research; flag "believed required" |

**Always available:** Zoning class, use permission, permit type, submittal method, fee schedules, adoption ordinances.
**Rarely available:** Timeline commitments, preliminary review, requirements without plans.
**Deflections:** "Consult a design professional" / "Can't confirm without plans" / "Submit plans and we'll review."
**Coastal jurisdictions are notably harder**—more politics, complex intertwined processes.
**Key skill:** Ask the same question 3-4 ways to verify consistency and competence.

---

## SIR Research Workflow

1. **Pre-Research (30m):** Existing occupancy → AHJ → code framework → zoning → authority levels
2. **Planning (1-2h):** Use permission → review reqs → parking/queuing → overlays
3. **Building (2-3h):** Permit type → submittal → timeline/fees → CO process
4. **Fire (1-2h):** Routing → fire code → sprinkler status
5. **Health (2-3h):** Authority level → food reqs REGARDLESS of model → restrooms → concurrency
6. **Engineering (1-2h):** Traffic trigger (use change?) → road jurisdictions
7. **Utilities (1h):** Provider/capacity → impact fees (always ask change-of-use)
8. **Internet (30m):** Provider → speeds → installation
9. **Flag Page (1h):** Issues → dependency graph → best/worst timelines → fees
10. **QC (1h):** Sections complete, contacts documented, TBDs flagged, timeline math verified

**Done when:** All 10 sections populated. Every dept has name/title/phone/email. Flag Page has issues + time table + fee table. Dependencies mapped. TBDs have next steps. Confidence noted for independent research. Timeline: best = longest concurrent (first round); worst = longest × rounds + revision. Fees: sum known + list TBD = "$X,XXX + TBD."

---

## Expert Perspectives

**Monica Swannie (CDS):** Adversarial framing validated across 27+ SIRs. Zoning always first. Staff rapport matters—no legal obligation to answer. Coastal jurisdictions harder. State-to-state nuances learned only through experience.

**ICC:** Code Adoption Tracker indispensable, but base IBC often insufficient due to state amendments. Always verify state-specific provisions.

**CDS:** Reports define output standard. Thoroughness > speed. AI should weight web research heavier than human workflow does.

**Municode:** Municipal codes more reliable than staff for zoning/fees/permissions. Don't capture process—staff essential for timelines/routing.