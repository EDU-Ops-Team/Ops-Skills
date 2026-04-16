---
name: brainlift-capacity
description: >-
  Calculate microschool student capacity using the Brainlift rules (JC Fischer methodology).
  Accepts multiple input types: spreadsheets with room/SF data, floor plan images (PDF, JPG, PNG),
  or manual room lists. Applies all Brainlift constraints (gross SF ceiling, NLA at 42 SF/student,
  80% NLA cap, classroom-sharing multiplier, workshop requirement, dining, play area, 1:1 meeting
  space, sunlight, bathrooms) and outputs a step-by-step capacity calculation with the final number.
  Use when asked to calculate capacity, determine max enrollment, run a capacity calc, apply
  Brainlift rules, or evaluate how many students a building can hold. Also use when reviewing a
  lease flyer, floor plan, or room schedule for capacity purposes.
metadata:
  author: greg-foote
  version: '1.1'
---

# Brainlift Capacity Calculator

## When to Use This Skill

Use when the user asks to:
- Calculate capacity for a school building
- Determine max enrollment using the Brainlift rules
- Review a floor plan, lease flyer, or room schedule for capacity
- Compare capacity numbers across systems (RHODES, Row 6.1, school calc sheets)
- Evaluate whether a building's capacity number is correct
- Run a capacity calc on a new or existing site

## Input Types

This skill handles three input modes. Determine which applies based on what the user provides.

### Mode 1: Spreadsheet
The user shares a spreadsheet (Google Sheets link, Excel file, or CSV) containing room names, square footages, and NLA classification flags.

**What to extract:**
1. Open the spreadsheet and identify all worksheet tabs — if multiple tabs exist, calculate capacity for each tab separately
2. Find the columns for: Room Name, Square Footage, and NLA flag (Yes/No or equivalent)
3. Look for summary rows that may contain pre-calculated totals for NFA and NLA
4. Record every room with its name, square footage, and NLA classification
5. Sum all room SF to get the NFA (Net Floor Area); sum NLA-flagged rooms to get total NLA
6. If the spreadsheet contains a pre-calculated capacity number, note it for comparison

### Mode 2: Floor Plan Image (PDF, JPG, PNG)
The user shares a floor plan drawing — from a lease flyer, architectural plan, or similar document.

**What to extract:**
1. Examine the floor plan image carefully and identify every labeled room
2. For each room, determine:
   - Room name/type (classroom, office, bathroom, kitchen, storage, hallway, etc.)
   - Estimated SF based on proportional analysis against the known total building SF
   - Whether it qualifies as NLA (has windows/natural light, is a learning space, not utility/admin)
3. Get the total building SF from the flyer, listing, or user — this is the anchor for proportional estimates
4. Floor plans are often marked "not to scale" — use the total building SF as the constraint and allocate room sizes proportionally based on visual area
5. Flag rooms where NLA classification is uncertain (e.g., interior rooms that may lack windows)
6. Always run a sensitivity analysis showing conservative, mid, and generous NLA estimates

**Room Classification Guide for Floor Plans:**

| Typically NLA | Typically NOT NLA |
|---|---|
| Rooms labeled "Classroom" | Bathrooms, Restrooms |
| Rooms labeled "Learning Area" | Kitchen, Kitchenette |
| Large open spaces with windows | Storage, Closets |
| Studios (if windowed) | Office, Admin |
| Commons (if used for learning) | Hallway, Corridor, Circulation |
| | Lobby, Entry, Reception |
| | Mechanical, Electrical, IT Closet |
| | Janitorial |
| | Breezeway (outdoor) |

**NLA Disqualifiers:**
- No windows / no natural light → cannot be NLA (use as workshop instead)
- Bathroom → never NLA
- Pure circulation (hallway) → never NLA
- Admin/office → not NLA unless explicitly repurposed for learning

### Mode 3: Manual Room List
The user provides room names and sizes in text (chat message, pasted table, etc.).

**What to extract:**
1. Parse the room list from the user's message
2. Ask for total NFA if not provided
3. Classify each room as NLA or non-NLA based on room type
4. Proceed to calculation

## Calculation Procedure

Read `references/brainlift-rules.md` for the complete rule set. Then execute these steps in order:

### Step 1: Establish Building Totals
- Total NFA (Net Floor Area / total usable interior space)
- Total NLA (Net Learning Area — rooms where students get dedicated learning seats)
- List all rooms with SF and NLA classification

### Step 2: Gross SF Ceiling
```
gross_ceiling = floor(NFA / 100)
```
This is the hard cap. No building can exceed this regardless of NLA.

### Step 3: 80% NLA Cap
```
max_nla = NFA × 0.80
usable_nla = min(actual_nla, max_nla)
```
If actual NLA exceeds 80% of NFA, cap it.

### Step 4: Classroom Configuration
Count the number of rooms that qualify as dedicated classrooms (NLA rooms ≥200 SF that could seat a group of students).

Apply the sharing multiplier:

| Usable Classrooms | Multiplier | Effective SF/Student |
|---|---|---|
| 4+ | 1.00x | 42 SF |
| 3 | 1.10x | 46 SF |
| 2 | 1.10x | 46 SF |
| 1 | 1.50x | 63 SF |

### Step 5: NLA Capacity
```
nla_capacity = floor(usable_nla / effective_sf_per_student)
```

### Step 6: Preliminary Capacity
```
preliminary = min(gross_ceiling, nla_capacity)
binding_constraint = "Gross SF ceiling" if gross_ceiling <= nla_capacity else "NLA capacity"
```

### Step 7: Supporting Requirements Check

Run each check against the preliminary capacity number:

**Workshop:** Does the building have a hybrid room (NLA room with ≥20% surplus beyond what the students in that room need) or a dedicated workshop room (35 SF × students per guide)? Windowless rooms should be prioritized for this. If no solution exists, reduce capacity until one does.

**1:1 Meeting Space:** Is there at least one room ≥100 SF suitable for private meetings?

**Play Area:** Is there on-site outdoor space (≥100 SF per student) or a qualifying off-site park (5-min walk for a 7-year-old, max 1 crosswalk, safe/maintained)?

**Dining:** Calculate: `ceil(capacity × 0.50 × 1.10) × 15 SF`. Is there enough non-NLA space for dining? Note: dining space can double as workshop but NOT as NLA.

**Bathrooms:** Are there bathrooms on every floor? Within 200 ft of NLA? Child-height fixtures for LL/L1?

**Sunlight:** Do all NLA rooms have natural light? If not, reclassify windowless rooms as workshop/support and recalculate NLA.

### Step 8: Grade Span Viability
If preliminary capacity < 20, test removing the oldest level (MS first, then L2, etc.) and recalculating with fewer levels until ≥20 students can be supported.

### Step 9: Final Capacity
The final number is the highest count that passes ALL constraints simultaneously. State the number, the binding constraint, and any conditional passes that need field verification.

## Output Format

Present results in this structure:

### Summary Box
State the final capacity number prominently, the binding constraint, and the building basics (address, NFA, NLA).

### Room Inventory Table
| Room | SF | NLA? | Notes |
|---|---|---|---|

### Step-by-Step Calculation
Show every step with the math. Use the format from the calculation procedure above. The user and their boss need to see exactly how the number was derived.

### Supporting Requirements
| Requirement | Status | Notes |
|---|---|---|
| Workshop | Pass/Fail/Conditional | Details |
| 1:1 Meeting | Pass/Fail | Details |
| Play Area | Pass/Fail/Conditional | Details |
| Dining | Pass/Fail/Conditional | Details |
| Bathrooms | Pass/Fail/Conditional | Details |
| Sunlight | Pass/Fail/Conditional | Details |

### Flags & Caveats
List anything that needs field verification, any assumptions made (especially for floor plan estimates), and any rooms where NLA classification is uncertain.

### Comparison (if applicable)
If the user provides numbers from other systems (RHODES, Row 6.1, school calc sheets), include a comparison showing where numbers match and where they differ, with an explanation of why.

## Multi-Building Sites

If a site has multiple buildings (like Alpha Anywhere NYC North/South or Riverside 10300/10350):
1. Calculate each building separately
2. Show individual capacities
3. Show combined capacity (sum of individual buildings)
4. Note that the combined gross ceiling (total NFA / 100) may differ slightly from the sum of individual ceilings due to rounding

## Sensitivity Analysis (Floor Plans Only)

When working from floor plan images (Mode 2), always include a sensitivity table since room sizes are estimated:

| Scenario | NLA Estimate | Capacity | Binding Constraint |
|---|---|---|---|
| Conservative | Low NLA | X students | Which rule caps it |
| Mid estimate | Mid NLA | Y students | Which rule caps it |
| Generous | High NLA | Z students | Which rule caps it |

This helps the user understand how much the estimate could shift once exact measurements are taken.
