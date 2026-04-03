# Corrections Record Schema

Each file in `benchmark/` named `{site_id}_{date}_corrections.json` is the output of one Mode 3 run. Multiple correction runs for the same site are allowed — append them, do not overwrite.

---

## Schema

```json
{
  "site_id": "string",
  "ai_report_path": "string — relative path to AI pre-report used",
  "vendor_report_path": "string — relative path to vendor completed return",
  "comparison_date": "string — ISO 8601, e.g. '2026-04-02'",

  "corrections": [
    {
      "id": "string — sequential, e.g. 'C-001'",
      "ai_claim_id": "string | null — HTML comment ID from vendor packet, e.g. 'R-047', or null if new finding",
      "section": "string — section path, e.g. 'Fire Department / Fire Design Criteria'",
      "field": "string — datapoint name or label, e.g. 'fire_code'",

      "ai_value": "string | null — what the AI said, or null if AI had no entry",
      "ai_confidence": "A | B | C | D | null",

      "vendor_value": "string — what the vendor found or confirmed",
      "vendor_source": "string | null — source the vendor noted, or null if left blank",

      "classification": "remote_miss | field_only | confirmed | confirmed_unsourced | corrected_unsourced | extended | unverified",

      "benchmark_update": "boolean — true if this correction should update the benchmark expected_* value",
      "benchmark_field": "string | null — the expected_* key to update, e.g. 'expected_code_framework.fire_code'",
      "benchmark_new_value": "string | null — the corrected value to write",

      "rubric_dimension": "string | null — which rubric dimension this affects, e.g. 'code_framework_correct'",
      "notes": "string"
    }
  ],

  "summary": {
    "total_reviewed": "number",
    "remote_misses": "number",
    "field_only": "number",
    "confirmed": "number",
    "confirmed_unsourced": "number",
    "corrected_unsourced": "number",
    "extended": "number",
    "unverified": "number",
    "benchmark_updates": "number"
  }
}
```

---

## Classification Definitions

| Classification | Meaning | Benchmark update? |
|---|---|---|
| `remote_miss` | AI missed or got wrong something the vendor found via public source | Yes |
| `field_only` | Vendor found this on site — genuinely required physical presence | No (validates task card) |
| `confirmed` | Vendor independently verified AI value with a named source | Yes (reinforces expected value) |
| `confirmed_unsourced` | Vendor did not re-verify — accepted AI value without independent check | No |
| `corrected_unsourced` | Vendor gave a different value but cited no source | Flag for human review |
| `extended` | Vendor added detail that doesn't contradict AI (AI was incomplete, not wrong) | Yes (adds detail to expected value) |
| `unverified` | Vendor left the row blank — no finding either way | Skip |

---

## Human Review Queue

Corrections with `classification: "corrected_unsourced"` must be reviewed by a human before `benchmark_update` is set to `true`. The comparison agent flags these but does not auto-update the benchmark. A human must:

1. Determine if the vendor value is plausible
2. If yes: manually set `benchmark_update: true` and add a `notes` entry explaining the judgment
3. If no: set `benchmark_update: false` and note why

---

## Example

```json
{
  "site_id": "boca-raton-01",
  "comparison_date": "2026-04-02",
  "corrections": [
    {
      "id": "C-001",
      "ai_claim_id": "R-047",
      "section": "Fire Department / Fire Design Criteria",
      "field": "fire_code",
      "ai_value": "Florida Fire Prevention Code — NFPA 1",
      "ai_confidence": "B",
      "vendor_value": "Florida Fire Prevention Code — NFPA 1 & 101 with FL Amendments",
      "vendor_source": "FL statute 633.202 — confirmed online",
      "classification": "remote_miss",
      "benchmark_update": true,
      "benchmark_field": "expected_code_framework.fire_code",
      "benchmark_new_value": "Florida Fire Prevention Code — NFPA 1 & 101 with FL Amendments",
      "rubric_dimension": "code_framework_correct",
      "notes": "AI omitted NFPA 101 and the FL Amendments. Both are publicly accessible. Classic code framework miss."
    },
    {
      "id": "C-002",
      "ai_claim_id": null,
      "section": "Building Department / Codes Adopted",
      "field": "state_admin_codes",
      "ai_value": null,
      "ai_confidence": null,
      "vendor_value": "FAC 6A-2.0040 (Sanitation Standards), FAC 4E-11 (Food Hygiene)",
      "vendor_source": "FL Department of Education website — publicly accessible",
      "classification": "remote_miss",
      "benchmark_update": true,
      "benchmark_field": "expected_code_framework.state_admin_codes",
      "benchmark_new_value": ["FAC 6A-2.0040 (Sanitation Standards)", "FAC 4E-11 (Food Hygiene)"],
      "rubric_dimension": "authority_chain_complete",
      "notes": "AI omitted state education admin codes entirely. Phase 3 instruction does list these but AI did not research them."
    },
    {
      "id": "C-003",
      "ai_claim_id": "R-062",
      "section": "Vendor Tasks / Verify Construction Type",
      "field": "construction_type",
      "ai_value": "D — Unknown, field verification required",
      "ai_confidence": "D",
      "vendor_value": "Type II-B steel frame with metal panel exterior",
      "vendor_source": "Field observation — exposed steel visible in mechanical room",
      "classification": "field_only",
      "benchmark_update": false,
      "benchmark_field": null,
      "benchmark_new_value": null,
      "rubric_dimension": null,
      "notes": "Correctly flagged as field-only. Task card worked as intended."
    }
  ],
  "summary": {
    "total_reviewed": 3,
    "remote_misses": 2,
    "field_only": 1,
    "confirmed": 0,
    "confirmed_unsourced": 0,
    "corrected_unsourced": 0,
    "extended": 0,
    "unverified": 0,
    "benchmark_updates": 2
  }
}
```
