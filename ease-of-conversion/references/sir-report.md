# SIR Report Template

Use this structure for the jurisdiction research sections of the AI-First SIR. This template
covers the governmental research half of the report — authority chain, department-by-department
findings, permit path, timelines, and fees. It pairs with the vendor-ready report template
which covers the building feasibility and vendor task sections.

---

## Report Structure

```
# Site Investigation Report
## [Property Address], [City], [State] [ZIP]
### Job #[number] — [Date]
### Prepared by: AI-First SIR Agent | Domain Expert: [if applicable]

---

## EXECUTIVE IMPACT SUMMARY

**Score: [XX] / 100 — [COLOR]  |  Recommendation: [PROCEED / PROCEED WITH CAUTION / REQUIRES JUSTIFICATION / PASS]**

[One plain-English sentence stating what this means. No jargon. Max 20 words.
GREEN/PROCEED example: "This building has no known barriers to opening a school. The work ahead is routine."
YELLOW/CAUTION example: "This building can work, but several unknowns need answers before you commit."
ORANGE/JUSTIFICATION example: "This building has serious problems. Only move forward if the business case justifies extra cost and time."
RED/PASS example: "This building is unlikely to work as a school. Walk away unless the situation changes."]

---

### What Must Be Built

[Checkbox checklist of construction and renovation scope inferred from code triggers and infrastructure gaps.
Order by cost, largest first. Each item: WHAT + WHY in one plain-English line. Use the Construction Scope
Inference table in references/site-eval-brainlift.md to populate cost ranges.]

- [ ] [Item] — [Plain-English reason, e.g., "required because the building is over 12,000 square feet"]
- [ ] [Item] — [Plain-English reason]
- [ ] ...

**What we don't know yet:** [One sentence naming field-only unknowns that could add to this list and what it would mean for budget if they go wrong. The building inspection will confirm or clear these.]

---

### Permits and Approvals

[Numbered sequential list of every permit, review, and approval required — in the order they must happen.
State in plain English whether each step runs at the same time as another or must wait for one to finish.]

1. **[Permit/Review Name]** — [Authority Name] — [e.g., "Start this on Day 1, same time as the building permit" or "Cannot file until Step 3 is complete"]
2. **[Permit/Review Name]** — [Authority Name] — [Concurrency/blocking note]
3. ...

---

### Timeline Outlook

**Range from this report: [X] weeks (best case) to [Y] weeks (worst case)**

**This site skews toward the [SHORT / MIDRANGE / LONG] end because:**
- [Plain-English reason, e.g., "No planning review is needed — that step is off the critical path entirely."]
- [Plain-English reason, e.g., "The fire department will talk to you before you submit plans, so there are no surprise requirements waiting at review."]
- [Plain-English reason — include a LONG reason if applicable, e.g., "The city requires a public hearing before the permit can be filed. That adds 8–16 weeks before construction can start."]

---

### Cost Outlook

**Permit fees: ~$[X] to $[Y]**
**Estimated buildout: ~$[X] to $[Y]**

**This site's costs run [BELOW TYPICAL / TYPICAL / ABOVE TYPICAL] for a K-8 conversion because:**
- [Plain-English reason, e.g., "The building is already permitted as a school — the biggest conversion cost is already off the table."]
- [Plain-English reason covering biggest cost driver or reducer]
- [Plain-English reason if applicable]

**Big-ticket items to watch:**
- [Item and cost range, e.g., "Sprinkler system: $60K–$150K if required (field inspection will confirm)"]
- [Item and cost range if applicable]

---

### Risk Watch

[Items that could blow up the timeline or budget. Use four likelihood levels only:
Unknown until field visit, Low, Moderate, High.]

| Risk | What happens if it goes wrong | Likelihood |
|---|---|---|
| [Risk item] | [Plain-English consequence — e.g., "Adds $60K–$150K and 4–6 weeks to install a sprinkler system"] | [Unknown until field visit / Low / Moderate / High] |
| [Risk item] | [Plain-English consequence] | [Likelihood] |

---

## FLAG PAGE

This is the most critical section. Decision-makers read this first.

### Summary of Important Issues

[Bulleted list of the 3-7 most critical findings. Each bullet should be one sentence
that captures a decision-driving fact. Lead with the most important.]

- [Most critical issue — e.g., "Educational use is BY RIGHT in the C-2 zoning district — no CUP required."]
- [Second issue — e.g., "State Fire Marshal review required — adds 2-4 weeks and $1,000 to timeline."]
- [Third issue — e.g., "Health department requires full kitchen infrastructure regardless of catering model — $575 in fees."]
- [Additional issues as warranted]

### Permit Review Time Summary

| Department / Process | Best Case | Worst Case | Concurrent? | Blocking? |
|---|---|---|---|---|
| Planning / Zoning | [time or N/A] | [time or N/A] | [Yes/No] | [What it blocks] |
| Building Permit | [time] | [time] | [Yes/No] | [What it blocks] |
| Fire Review | [time or "Included with building"] | [time] | [Yes/No] | [What it blocks] |
| State Fire Marshal | [time or N/A] | [time or N/A] | [Yes/No] | [What it blocks — e.g., "City permit won't release until state approves"] |
| Health Review(s) | [time] | [time] | [Yes/No] | [What it blocks — e.g., "Final health requires fire inspection + CO first"] |
| Engineering | [time or TBD] | [time or TBD] | [Yes/No] | [What it blocks] |
| Certificate of Occupancy / BTR | [time] | [time] | Sequential | Post-construction |
| **TOTAL (Critical Path)** | **[best]** | **[worst]** | — | — |

**Timeline Calculation Notes:**
- Best case = [explain: e.g., "longest concurrent track (building + state FM) approved first round = X weeks"]
- Worst case = [explain: e.g., "3 review rounds with 1 week revision between each = X weeks"]
- [Note any post-construction processes that add to total]

### Permit Fee Estimate Summary

| Department / Item | Fee | Status | Notes |
|---|---|---|---|
| Building Permit | $[amount] | [Known/Estimated/TBD] | [Formula if applicable — e.g., "1% of contract value, min $100"] |
| State Fire Marshal | $[amount or N/A] | [Known/TBD] | |
| Health Review(s) | $[amount] | [Known/TBD] | [Itemize if multiple — e.g., "School Plan Review $325 + Food Sanitation $250"] |
| Planning / Zoning | $[amount or $0] | [Known/TBD] | |
| Impact / Utility Fees | $[amount or TBD] | [Known/TBD] | [e.g., "SAC Determination — separate process, fees TBD"] |
| Business Tax Receipt / CO | $[amount] | [Known/TBD] | |
| **TOTAL** | **$[total] + TBD** | — | [List what's TBD] |

---

## CONTACT INFORMATION

[Every department that has been researched must have a contact entry, even if the contact
is "General line — no named contact obtained."]

### [Department Name] — [Authority Level: City / County / State]
| Field | Value |
|---|---|
| Authority | [Full name — e.g., "Florida Department of Health in Palm Beach County"] |
| Address | [Physical address] |
| Contact Person | [Name or "General line"] |
| Title | [Title or "—"] |
| Phone | [Phone number] |
| Email | [Email or "Not obtained"] |
| Notes | [e.g., "Staff unresponsive — information obtained via independent research"] |

[Repeat for every department: Planning, Building, Fire, Health, Engineering, Sewer, Water, Internet,
State Fire Marshal (if applicable), State DOT (if applicable), Education/Childcare Regulator (if applicable)]

---

## PLANNING & ZONING

### Summary / Flag Items
- **Review Time:** [time or "Not required"]
- **Fees:** [fees or "$0"]
- **Key Finding:** [One-sentence summary — e.g., "Educational use permitted by right in DT1 zoning district"]

### Zoning Classification
- District: [e.g., "C-2 Community Commercial"]
- Educational / School Use: [BY RIGHT / CUP REQUIRED / SUP REQUIRED / NOT PERMITTED]
- Conditions: [Any conditions — e.g., "Playground and parking must meet setback requirements"]
- Source: [Municipal code section or staff confirmation]
- Confidence: [A/B/C/D]

### Planning Review
- Separate Planning Review Required: [YES / NO / SIGN-OFF ONLY]
- Pre-Application Meeting: [Available / Required / Not applicable / Rejected]
- Parking Requirements: [Requirements with code citation, or "Existing parking deemed adequate for same-use"]
- Loading / Queuing Requirements: [Requirements or "None identified"]
- Overlay Districts: [Historic, flood, restricted parking, etc. — or "None"]

---

## BUILDING DEPARTMENT

### Summary / Flag Items
- **Permit Type:** [Exact name — e.g., "Interior Only Alteration Permit"]
- **Review Time:** [Best case / Worst case]
- **Fees:** [Amount or formula]
- **Key Finding:** [e.g., "Short Form Permit available — no structural changes means streamlined process"]

### Codes Adopted
- Building Code: [Edition — e.g., "2023 Florida Building Code, 8th Edition"]
- Fire Code: [Edition — e.g., "Florida Fire Prevention Code — NFPA 1 & 101 with FL Amendments"]
- State Education Admin Codes: [e.g., "FAC 6A-2.0040 (Sanitation Standards), FAC 4E-11 (Food Hygiene)"]
- Confidence: [A/B/C/D]

### Special Design Criteria
- [Any jurisdiction-specific requirements — e.g., "MN Section 429.2 K-2 grade restrictions"]
- [Or "Per State Building Codes — consult design professional for specifics"]

### Restroom Requirements
- Source: [Building code / Health department / State plumbing code]
- Requirements: [Specific requirements — e.g., "Separate staff restroom, grades 2-8 M/F, K-1 may use unisex"]
- Confidence: [A/B/C/D]

### Plan Submittal Procedure
- Platform: [e.g., "Online via Boca Raton eHUB portal"]
- Process: [Steps — e.g., "Submit application + plans → initial review 2-4 weeks → subsequent rounds 1-2 weeks"]
- Review Rounds: [Expected number — e.g., "Most jurisdictions assume 3 rounds, 1 week revision between"]
- Expedited Review: [Available / Not available / TBD]
- Requirements: [List of submittal requirements — plans, affidavits, insurance certificates, etc.]

### Certificate of Occupancy
- Type: [Standard CO / Occupancy-class CO / Business Tax Receipt / Other]
- Process: [e.g., "CO issued to occupancy class — floor already E occupancy, no new CO needed"]
- Timeline: [Separate timeline if applicable — e.g., "BTR: 3 weeks post-construction"]
- Fees: [If separate from permit fees]

---

## FIRE DEPARTMENT

### Summary / Flag Items
- **Review Routing:** [Through building / Separate local / State Fire Marshal required]
- **Review Time:** [Time or "Included with building permit"]
- **Fees:** [Fees or "Included"]
- **Key Finding:** [e.g., "State FM review required — BLOCKS city permit release until approved"]

### Fire Design Criteria
- Fire Code: [Edition, if different from building section]
- Education-Specific Provisions: [e.g., "FL Fire Prevention Code Chapter 14"]
- Sprinkler Status: [Known from records / Unknown — vendor task / Confirmed by staff]
- Source of Sprinkler Info: [e.g., "Public records request" or "Staff confirmation" or "UNKNOWN"]

### Plan Submittal Procedure
- [If separate from building: describe process, timeline, fees]
- [If routed through building: "Fire review included with building permit submittal"]
- [If state FM: describe separate state process, blocking dependency, timeline, fees]

### Courtesy Inspection
- Available: [YES / NO / Not asked]
- Contact: [If available — e.g., "Email Assistant Fire Marshal to coordinate"]

---

## HEALTH DEPARTMENT

### Summary / Flag Items
- **Authority Level:** [City / County / State — with specific department name]
- **Reviews Required:** [List — e.g., "School Plan Review + Food Sanitation Certificate"]
- **Review Time:** [Time for each review]
- **Fees:** [Itemized fees]
- **Key Finding:** [e.g., "Full kitchen infrastructure required REGARDLESS of catering model"]

### Health Design Criteria
- Food Service Requirements: [SPECIFIC to this jurisdiction — e.g., "Hand sink within 20', mop sink, refrigerator, smooth cleanable surfaces"]
- Food Model Impact: [e.g., "Requirements apply regardless of whether Alpha School or caterer provides meals"]
- Restroom Requirements (from Health): [If health dept imposes requirements beyond building code]
- Illumination / Drinking Fountain Requirements: [If applicable — e.g., "40 foot-candles, 1 fountain per 100 occupants per FAC 6A-2.0040"]

### Plan Submittal Procedure
- Concurrency: [Concurrent with building? Blocking dependencies?]
- Process: [Steps, timeline, fees for each required review]
- Post-Construction Requirements: [e.g., "Final health approval requires fire inspection + building CO first"]

---

## ENGINEERING DEPARTMENT

### Summary / Flag Items
- **Traffic Study Required:** [YES / NO / TBD — with trigger logic]
- **Review Time:** [Time or TBD]
- **Key Finding:** [e.g., "No traffic study triggered — existing use is already a school"]

### State DOT
- Adjacent Roads: [City-maintained / State DOT jurisdiction]
- DOT Authority: [e.g., "FDOT District 4" or "MassDOT"]
- Requirements: [Any DOT requirements or "None identified"]

### Local Transportation
- Stacking / Queuing Requirements: [Requirements or "None identified"]
- Drop-off / Pick-up: [Constraints or observations]

### Plan Submittal Procedure
- [Process if separate from building, or "Routed through building permit"]

---

## SANITARY SEWER

### Summary / Flag Items
- **Provider:** [Name — city utility, regional authority, private]
- **Capacity:** [Sufficient / Concerns / TBD]
- **Fees:** [Impact fees, connection charges, or "None identified"]

### Plan Submittal Procedure
- [Separate process or "Routed through building permit"]
- Impact Fees: [Amount or TBD — e.g., "SAC Determination: separate 11-week process, fees TBD"]

---

## WATER

### Summary / Flag Items
- **Provider:** [Name]
- **Capacity:** [Sufficient / Concerns / TBD]
- **Fees:** [Amount or TBD]

### Plan Submittal Procedure
- [Separate process or "Routed through building permit"]

---

## INTERNET

### Summary / Flag Items
- **Provider:** [Name]
- **Available Speeds:** [Speed and connection type — e.g., "1 Gbps fiber"]
- **Installation:** [Timeline and fees]

---

## OPEN ITEMS AND CONFIDENCE NOTES

[List all items where information was obtained through independent research rather than
staff confirmation, or where staff was unresponsive.]

| Item | Status | Confidence | Next Step |
|---|---|---|---|
| [e.g., "Building permit type"] | Based on online research | B | Confirm with staff at submittal |
| [e.g., "Fire review process"] | Staff unresponsive | D | Continue attempting contact |
| [e.g., "Engineering review"] | Awaiting response | TBD | Follow up [date] |

---

## DISCLAIMER

This report is a compilation of information gathered at the time of preparation from public
sources, jurisdiction staff, and online research. Requirements may change based on final
project scope and plan review. Environmental site assessments are not addressed in this report.
All findings labeled with confidence levels below A should be verified during the permit
process. Timeline and fee estimates are based on current jurisdiction practices and may vary.
```

---

## Formatting Notes

- Use tables for all structured data — they're scannable
- Every department section must have content, even if "N/A — not required for this scope" or "TBD — awaiting response"
- Bold the most critical finding in each department's Summary / Flag Items
- Flag Page should be readable in 2 minutes and contain enough information for a go/no-go decision
- Contact information must be present for every researched department
- Open items table at the end ensures nothing falls through the cracks
- The SIR sections pair with the vendor-ready report sections to form the complete AI-First SIR output
- **Executive Impact Summary reading level**: No higher than 8th grade. No jargon without a plain-English explanation in the same sentence. Sentences under 20 words where possible. Write like you're explaining to a smart business owner who has never read a building code.
- **"What Must Be Built" format**: Use markdown checkboxes (`- [ ]`) so stakeholders can use the list as a tracking document during the project.
- **Risk Watch likelihood levels**: Use exactly four levels — "Unknown until field visit", "Low", "Moderate", "High". No other options.