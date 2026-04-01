# Vendor-Ready Report Template

Use this exact structure for every Site Evaluator report. The report serves two audiences
simultaneously: decision-makers who need a go/no-go signal, and vendors who need a
specific scope of work when they visit the site.

---

## Report Structure

```
# Building Conversion Feasibility Report
## [Address]
### Assessment Date: [Date] | E-Occ Score: [Score]/100 | Rating: [GREEN/YELLOW/ORANGE/RED]

---

## EXECUTIVE SUMMARY

[2-3 sentences max. State the address, current use, conversion difficulty tier, E-Occ score,
and the single most important finding. End with a clear recommendation: PROCEED / PROCEED WITH
CAUTION / REQUIRES JUSTIFICATION / PASS.]

Example: "1750 Rufe Snow Dr, Keller, TX 76248 is a single-story commercial office (Group B)
built in 1998. With an E-Occ Score of 92/100 (YELLOW), this is a low-difficulty conversion
candidate with no sprinkler requirement and no environmental concerns. Recommend PROCEED
to detailed assessment."

---

## SECTION 1: WHAT WE KNOW (Automated Findings)

Present findings grouped by category. Each finding should include:
- The data point and value
- Source (where this came from)
- Confidence level (A/B/C/D)

### Building Characteristics
| Data Point | Value | Source | Confidence |
|---|---|---|---|
| Gross Building Area | [value] SF | [County] Assessor | A |
| Number of Stories | [value] | [County] Assessor | A |
| Year Built | [value] | [County] Assessor | A |
| Assessor Use Code | [raw code] | [County] Assessor | A |
| IBC Occupancy Classification | Group [X] | Mapped from use code | B |
| Exterior Material | [value] | Street View / Assessor | B/C |
| Lot Size | [value] SF ([value] acres) | [County] Assessor | A |
| Vacancy Status | [occupied/vacant] | Business Search | B |
| Current Tenant/Use | [description] | Google Business | B |

### IBC Code Analysis
| Threshold | Value | Trigger? | Implication |
|---|---|---|---|
| Fire Area vs 12,000 SF | [value] SF | [YES/NO] | [Sprinklers required / Not required] |
| Below Grade Space | [YES/NO/UNKNOWN] | [YES/NO/UNKNOWN] | [Sprinklers required regardless of size / Clear / Needs verification] |
| Calculated Occupant Load | [value] persons | — | Based on [gross SF] × 0.70 ÷ 20 |
| OL vs 50-Person Threshold | [value] ≥ 50? | [YES/NO] | [Triggers fire alarm, panic hardware, 2 exits, storm shelter / Below threshold] |
| Bathrooms Required | [count] | — | For OL of [value] per IBC plumbing tables |

### Environmental & Geographic
| Check | Result | Source | Confidence |
|---|---|---|---|
| FEMA Flood Zone | Zone [X] | FEMA FMSC | A |
| Flood Insurance Required | [YES/NO] | FEMA FMSC | A |
| Environmental Contamination | [clear/concern/active] | EPA / [State agency] | B |
| UST Status | [none/present/removed] | [State] UST database | B |
| Historic District | [YES/NO] | City planning records | B |
| Asbestos/Lead Risk | [none/possible/likely] | Inferred from year built | A |
| Tornado Zone (250 mph) | [YES/NO] | ICC 500 wind map | B |
| Storm Shelter Required | [YES/NO] | 250mph zone AND OL>50 | A |
| Seismic Design Category | [A-F] | USGS data | B |

### Cost Estimates (Known Items)
| Item | Estimated Cost | Confidence | Notes |
|---|---|---|---|
| Sprinkler System | $[value] | C | [SF × $4.50/SF or $0 if not required] |
| Fire Alarm System | $[value] | C | [SF × $4/SF or $0 if OL<50] |
| Panic Hardware | $[value] | C | [Est. door count × $400] |
| Bathroom Additions | $[value] | C | [Additional bathrooms needed × $20,000] |
| **Identified Code Costs** | **$[total]** | **C** | **Sum of above — does NOT include egress, buildout, or tenant improvements** |

---

## SECTION 2: WHAT WE DON'T KNOW (Vendor Tasks)

This section is the vendor's scope of work. Each task is self-contained — a vendor should
be able to read a single task card and know exactly what to do, why, and what "good" looks like.

### CRITICAL TASKS (Must Complete Before Proceeding)

These items can kill the deal. Prioritize these on any site visit.

#### Task [N]: [Task Title]
- **What to do:** [Plain-language instruction. Be specific about what to measure, photograph, or verify.]
- **Why it matters:** [Explain the IBC threshold or requirement in plain language. No jargon without explanation.]
- **What good looks like:** [Specific pass/fail criteria. "Travel distance must be ≤200 feet (no sprinklers) or ≤250 feet (with sprinklers) from the most remote classroom location to the nearest exit."]
- **Tools needed:** [Tape measure, camera, laser distance meter, etc.]
- **Datapoint name:** [The exact field name for the database, e.g., `max_travel_distance`]

---

Standard critical tasks that should ALWAYS be included (egress is never knowable from address alone):

#### Task: Measure Maximum Travel Distance
- **What to do:** Starting from the point farthest from any exit inside the building, measure the walking path (not straight line) to the nearest exit door. Follow corridors and go around walls — don't measure through them. Take a photo at your starting point and at each exit door.
- **Why it matters:** The IBC limits how far students would have to travel to escape in an emergency. If this distance exceeds the limit and we can't add a closer exit, this building cannot be converted to a school.
- **What good looks like:** ≤200 feet without sprinklers, ≤250 feet with sprinklers. Under 150 feet is ideal.
- **Tools needed:** Laser distance meter or long measuring tape, camera
- **Datapoint name:** `max_travel_distance`

#### Task: Count and Document All Exits
- **What to do:** Walk the entire building perimeter and interior. Count every door that leads directly outside or to a public way. For each exit: photograph it, note if it has panic hardware (a push bar), and measure the door width. Note if any exits are blocked, locked, or obstructed.
- **Why it matters:** A school with more than 50 students needs at least 2 exits, and they must be separated by a minimum distance (at least 1/3 of the building's maximum diagonal dimension). If the building only has one exit, adding a second may require cutting through an exterior wall — a major expense.
- **What good looks like:** Minimum 2 exits, separated by at least 1/3 of the building diagonal. Each exit door at least 32" clear width.
- **Tools needed:** Tape measure, camera
- **Datapoint names:** `number_of_exits`, `exit_separation_distance`

#### Task: Measure Corridor Widths
- **What to do:** Measure the width of every corridor/hallway in the building at its narrowest point. Also note any points where the corridor narrows (columns, built-in furniture, etc.). Photograph each measurement location.
- **Why it matters:** School corridors must be at least 44 inches wide to allow safe evacuation. Corridors narrower than this would require demolition and reconstruction — often a deal-killer.
- **What good looks like:** All corridors ≥44 inches wide. Under 44 inches at any point is a serious problem.
- **Tools needed:** Tape measure, camera
- **Datapoint name:** `corridor_width_min`

#### Task: Check for Dead-End Corridors
- **What to do:** Walk every corridor. If any corridor ends without reaching an exit (a "dead end"), measure the distance from the dead end to the point where you can turn toward an exit. Photograph dead-end corridors.
- **Why it matters:** Dead-end corridors trap students during evacuations. The IBC limits dead-end length to 20 feet (no sprinklers) or 50 feet (with sprinklers). Exceeding this requires adding exits or reconfiguring walls.
- **What good looks like:** No dead-end corridors exceeding 20 feet (or 50 feet if building has sprinklers).
- **Tools needed:** Laser distance meter, camera
- **Datapoint name:** `dead_end_corridor_length`

#### Task: Verify Construction Type
- **What to do:** Determine the building's structural system: Is the frame steel, concrete, or wood? Are the exterior walls masonry (brick/block), metal panel, or wood siding? Can you see the roof structure (steel joists, wood trusses, concrete deck)? Look in accessible ceiling spaces, mechanical rooms, or loading areas where structure is exposed. Photograph all visible structural elements.
- **Why it matters:** The IBC limits building size (height and area) based on construction type. If the building's construction type doesn't support Group E at its current size, the conversion may not be possible without major structural upgrades.
- **What good looks like:** Any construction type can work — we just need to know what it IS so we can check the IBC tables. Steel or concrete (Types I or II) is most flexible. Wood frame (Type V) is most restrictive on size.
- **Tools needed:** Camera, flashlight for ceiling/mechanical spaces
- **Datapoint name:** `construction_type`

---

### STANDARD TASKS (Collect During Same Visit)

These items inform the detailed assessment and cost modeling but aren't deal-killers.

[Include relevant tasks based on what's unknown. Common ones:]

#### Task: Count Existing Bathrooms
- **What to do:** Count all restrooms in the building. For each, note: number of fixtures (toilets + urinals), whether it's ADA accessible (grab bars, wider stall, accessible sink height), and general condition.
- **Why it matters:** Schools require a minimum number of bathrooms based on student count. Adding bathrooms costs approximately $20,000 each, so knowing the current count directly affects the budget.
- **What good looks like:** [N] bathrooms are needed for this building's occupant load of [OL]. Any existing bathrooms reduce the conversion cost.
- **Tools needed:** Camera, notepad
- **Datapoint name:** `bathroom_count_existing`

#### Task: Check for Existing Fire Protection
- **What to do:** Look for: (1) A Fire Department Connection (FDC) on the building exterior — a metal plate with two or more hose connections, usually near the main entrance. (2) Sprinkler heads visible on ceilings inside the building. (3) A fire alarm pull station near exit doors. (4) Fire alarm panel — usually in a main corridor or entry vestibule. Photograph everything you find.
- **Why it matters:** Existing fire protection systems reduce conversion cost significantly. A building that already has sprinklers saves $[estimated sprinkler cost]. An existing fire alarm panel may only need reprogramming rather than full replacement.
- **What good looks like:** Sprinkler heads visible throughout = major cost savings. Fire alarm panel present = moderate savings. Neither = budget for full installation.
- **Tools needed:** Camera
- **Datapoint names:** `sprinkler_system_existing`, `fire_alarm_existing`, `fdc_observed`

#### Task: Document ADA Accessibility Features
- **What to do:** Check and photograph: (1) Is there a level (no-step) entrance from the parking lot? (2) Are doorways at least 32" clear width? (3) Are there ramps where there are level changes? (4) If multi-story, is there an elevator? (5) Are bathrooms ADA compliant (grab bars, wide stalls)?
- **Why it matters:** Educational facilities must meet ADA accessibility standards. Buildings constructed after 1990 (post-ADA) were likely built compliant, but modifications over time may have introduced barriers.
- **What good looks like:** Level entrance, 32"+ doorways, accessible restrooms. Post-1990 buildings generally need minimal ADA work.
- **Tools needed:** Tape measure, camera
- **Datapoint names:** `accessible_entrance_observed`, `ada_compliance_notes`

#### Task: Photograph Building Exterior (All Sides)
- **What to do:** Walk the full building perimeter. Photograph each side of the building, the roof (from ground level), the parking lot, any signage, and the street frontage. Note visible conditions: cracking, water damage, roof sagging, HVAC equipment on roof.
- **Why it matters:** Exterior photos help assess overall building condition, confirm assessor data (stories, materials), and identify issues not visible in satellite imagery.
- **What good looks like:** Building in good structural condition with no visible damage. Clean roofline. Adequate parking visible.
- **Tools needed:** Camera/phone
- **Datapoint name:** `exterior_condition_photos`

---

## SECTION 3: CONVERSION DIFFICULTY ASSESSMENT

### Difficulty Tier: [LOW / MODERATE / MODERATE-HIGH / HIGH / VERY HIGH]

**Original Occupancy:** Group [X] ([Description])
**Target Occupancy:** Group E (Educational)

**Why this tier:** [2-3 sentences explaining what makes this conversion easier or harder
based on the original building design. Reference specific building characteristics.]

### Key Advantages for This Conversion
- [List 2-4 specific advantages, e.g., "Single story eliminates elevator and multi-floor egress requirements"]

### Key Challenges for This Conversion
- [List 2-4 specific challenges, e.g., "Open retail floor plan requires subdivision into classrooms — significant framing and drywall cost"]

### Known Code Costs: $[total]
### Estimated Additional Costs: [Range based on difficulty tier]
- LOW tier: $10-30/SF additional buildout
- MODERATE tier: $30-60/SF additional buildout
- MODERATE-HIGH tier: $60-100/SF additional buildout
- HIGH tier: $100-150+/SF additional buildout

### Estimated Total Conversion Range: $[low] — $[high]
Based on [gross SF] at $[low/SF] — $[high/SF]

---

## SECTION 4: NEXT STEPS

[Based on the rating, provide specific next steps:]

**If GREEN (80-100):**
1. Dispatch vendor with this report to collect Critical Tasks
2. Upon vendor data return, update assessment and calculate detailed score
3. If detailed score remains GREEN, engage architect for preliminary design
4. Budget: Known code costs + difficulty tier buildout range

**If YELLOW (60-79):**
1. Review the specific challenges identified above — are they acceptable?
2. If yes, dispatch vendor with this report focusing on Critical Tasks
3. Critical tasks may reveal deal-breakers — wait for data before committing
4. Budget contingency: add 20% to estimated range

**If ORANGE (40-59):**
1. This site has significant barriers — document business justification for proceeding
2. Before dispatching vendor, confirm that alternative sites have been evaluated
3. If proceeding, vendor visit should focus exclusively on Critical Tasks
4. Budget contingency: add 30-50% to estimated range

**If RED (0-39):**
1. Recommend PASS on this site
2. Fatal flaw identified: [state the specific deal-breaker]
3. No vendor dispatch recommended unless the fatal flaw assessment is disputed
```

---

## Formatting Notes

- Use tables wherever possible — they're scannable
- Bold the most important number in each section
- Keep vendor task descriptions at a 10th-grade reading level — no IBC jargon without explanation
- Every cost estimate must be labeled with confidence level
- The report should be 3-5 pages when printed — concise enough to read in 10 minutes