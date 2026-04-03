# Benchmark Record Schema

Each file in `benchmark/` is a JSON record for one site. The record defines the ground truth that Mode 4 scores against and Mode 3 updates.

---

## Schema

```json
{
  "site_id": "string — kebab-case identifier, e.g. boca-raton-01",
  "address": "string — full street address",
  "state": "string — two-letter code",
  "holdout": "boolean — true = held out from loop training, scored only at promotion",

  "source_sir_path": "string — relative path to source SIR document used to build this record",
  "source_inspection_path": "string | null — relative path to inspection report if available",

  "expected_existing_use": "string — plain description, e.g. 'Single-story commercial office, Group B'",
  "expected_occupancy_class": "string — IBC group, e.g. 'Group B'",

  "expected_authorities": [
    "string — one entry per authority, e.g. 'City of Boca Raton Building Department'",
    "string — e.g. 'FL DOH Palm Beach County (Health)'",
    "string — include non-city authorities explicitly"
  ],

  "expected_code_framework": {
    "building_code": "string — edition and name, e.g. '2023 Florida Building Code, 8th Edition'",
    "fire_code": "string — e.g. 'Florida Fire Prevention Code — NFPA 1 & 101 with FL Amendments'",
    "state_admin_codes": ["string — e.g. 'FAC 6A-2.0040 (Sanitation Standards)'"],
    "local_amendments": ["string — or empty array if none"]
  },

  "expected_zoning_status": "BY_RIGHT | CUP | SUP | NOT_PERMITTED",

  "expected_permit_path": {
    "permit_type": "string — exact name used by jurisdiction, e.g. 'Interior Alteration Permit'",
    "submittal_platform": "string — e.g. 'eHUB online portal'",
    "best_case_weeks": "number",
    "worst_case_weeks": "number",
    "blocking_dependencies": ["string — e.g. 'City permit will not release until State FM approves'"]
  },

  "expected_key_fees": {
    "building_permit": "string — amount or formula, e.g. '1% of contract value'",
    "state_fire_marshal": "string | null",
    "health": "string — itemized if multiple reviews",
    "total_known": "string — e.g. '$745 + TBD SAC'"
  },

  "expected_timeline_notes": "string — critical path explanation, e.g. 'BTR is post-construction, adds 3 weeks after CO'",

  "expected_environmental_findings": {
    "flood_zone": "string — FEMA zone designation, e.g. 'Zone X'",
    "contamination": "CLEAR | MINOR | ACTIVE",
    "historic_district": "boolean",
    "asbestos_lead_risk": "NONE | POSSIBLE | LIKELY"
  },

  "expected_conversion_risks": [
    "string — one per identified risk, e.g. 'Full kitchen infrastructure required regardless of catering model'"
  ],

  "expected_vendor_tasks": [
    "string — datapoint names, e.g. 'max_travel_distance', 'construction_type', 'corridor_width_min'"
  ],

  "fatal_flags": [
    "string — known deal-killers for this site, or empty array"
  ],

  "e_occupancy_score": "number | null — known score if available from source SIR",
  "e_occupancy_rating": "GREEN | YELLOW | ORANGE | RED | null",

  "notes": "string — anything unusual about this site or its benchmark record"
}
```

---

## Populating a Record

1. Start from the source SIR document for the site.
2. Extract each field from the SIR findings — use only what the SIR explicitly states.
3. If the SIR is ambiguous on a field, set the value to `null` and note it in `notes`. Do not infer.
4. After vendor return, run Mode 3. The comparison agent will update fields marked as `remote_miss` — do not manually edit those fields after the first comparison run.

---

## Example: Boca Raton (partial)

```json
{
  "site_id": "boca-raton-01",
  "address": "TBD — populate from source SIR",
  "state": "FL",
  "holdout": false,

  "expected_authorities": [
    "City of Boca Raton Building Department",
    "City of Boca Raton Planning & Zoning",
    "FL DOH Palm Beach County (Health)",
    "Florida State Fire Marshal (if triggered)",
    "FDOT (road jurisdiction check required)"
  ],

  "expected_code_framework": {
    "building_code": "2023 Florida Building Code, 8th Edition",
    "fire_code": "Florida Fire Prevention Code — NFPA 1 & 101 with FL Amendments",
    "state_admin_codes": ["FAC 6A-2.0040 (Sanitation Standards)", "FAC 4E-11 (Food Hygiene)"],
    "local_amendments": []
  },

  "expected_permit_path": {
    "permit_type": "Interior Alteration Permit + Business Tax Receipt",
    "submittal_platform": "eHUB online portal",
    "best_case_weeks": 2,
    "worst_case_weeks": 10,
    "blocking_dependencies": [
      "BTR requires fire inspection + CO before final health approval",
      "BTR is post-construction — 3 weeks after CO"
    ]
  },

  "expected_key_fees": {
    "building_permit": "1% of contract value",
    "state_fire_marshal": null,
    "health": "$575 (two reviews required regardless of catering model)",
    "total_known": "1% contract + $575 + $170 BTR"
  },

  "expected_conversion_risks": [
    "Full kitchen infrastructure required by FL DOH Palm Beach regardless of catering model",
    "Two separate health reviews required (school plan review + food sanitation)"
  ],

  "notes": "Health requirements apply regardless of food model — this is a common AI miss. Verify this is captured in code framework and health sections."
}
```
