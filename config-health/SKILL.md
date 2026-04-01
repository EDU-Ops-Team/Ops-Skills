---
name: config-health
description: "Audit the global Claude Code configuration for structural health. Use when the user says 'config health', 'check my config', 'audit my setup', 'config-health', or wants to verify their global Claude Code standards are consistent with the RAPTOR/GraphRAG-informed hierarchy."
disable-model-invocation: false
---

# Config Health Check

Audit the global `~/.claude/` configuration against the RAPTOR/GraphRAG-informed hierarchy:
- **Layer 0** (always-present): `~/.claude/CLAUDE.md` — universal rules + compressed domain summaries with routing pointers
- **Layer 1** (trigger-based): `~/.claude/skills/` — action workflows only, never knowledge-only skills
- **Layer 2** (on-demand): `~/.claude/docs/` — full domain standards, loaded when Layer 0 routes to them

## Procedure

Run ALL of the following checks. For each, report PASS or FAIL with a specific explanation. At the end, provide a summary table and any fix recommendations.

### Check 1: CLAUDE.md Line Count

Read `~/.claude/CLAUDE.md`. Count lines.
- **PASS**: ≤ 150 lines
- **WARN**: 151–200 lines (getting bloated)
- **FAIL**: > 200 lines (context degradation likely)

Report the current count.

### Check 2: Compressed Domain Headers

For every file in `~/.claude/docs/`, verify that `~/.claude/CLAUDE.md` contains BOTH:
1. A **compressed summary header** for that domain (a line with pipe-delimited key terms, e.g., `## Python | uv | ruff | pytest | ...`)
2. A **routing pointer** to the full doc (a line containing `→` and the file path)

- **PASS**: Every doc has both a compressed header and a pointer in CLAUDE.md
- **FAIL**: List any docs that are missing a compressed header (decision knowledge gap — agent won't know to descend) or missing a pointer (can't find the full doc)

### Check 3: Orphaned Docs

For every routing pointer in CLAUDE.md (lines containing `→` and a file path), verify the target file actually exists.

- **PASS**: All pointed-to files exist
- **FAIL**: List any broken pointers (Layer 0 routes to nowhere)

### Check 4: Duplication Check

Compare `~/.claude/lessons.md` against `~/.claude/CLAUDE.md`. Flag any rules that appear in both files verbatim or near-verbatim.

- **PASS**: No significant duplication
- **WARN**: List duplicated rules. Recommend keeping the compressed version in CLAUDE.md and the incident-specific version in lessons.md only if it adds context.

### Check 5: Skill Classification

For every skill in `~/.claude/skills/`, read its `SKILL.md` frontmatter and first 20 lines of content. Classify it as:
- **Action skill**: Has a clear trigger, performs a task, produces an output (CORRECT for Layer 1)
- **Knowledge skill**: Primarily provides reference information or conventions without a specific task (SHOULD BE in Layer 0 or Layer 2 instead)

- **PASS**: All skills are action-oriented
- **FAIL**: List any knowledge skills and recommend migrating their content to either a compressed header in CLAUDE.md (if high-frequency) or a doc in `~/.claude/docs/` (if low-frequency)

### Check 6: Settings Security

Read `~/.claude/settings.json`. Check for deny rules covering:
- `.env` files
- Credential files
- SSH keys
- Destructive commands (`rm -rf`)

- **PASS**: All four categories have deny rules
- **WARN**: List any missing categories

### Check 7: Layer Balance

Report token estimates for each layer:
- **Layer 0**: Approximate token count of CLAUDE.md (lines × ~4 tokens/line as rough estimate)
- **Layer 1**: Count of skills and their description lengths
- **Layer 2**: Total lines across all docs/

Assess whether Layer 0 is appropriately sized relative to the other layers. Layer 0 should be the smallest layer by far — it's the root summary, not the whole tree.

- **PASS**: Layer 0 < 30% of total configuration tokens
- **WARN**: Layer 0 is 30-50% of total
- **FAIL**: Layer 0 > 50% (CLAUDE.md has absorbed too much — decompose into docs/skills)

## Output Format

After running all checks, produce a summary:

```
CONFIG HEALTH REPORT
════════════════════
Check 1 — CLAUDE.md size:        [PASS/WARN/FAIL] (N lines)
Check 2 — Domain headers:        [PASS/FAIL] (N/M docs covered)
Check 3 — Orphaned docs:         [PASS/FAIL] (N broken pointers)
Check 4 — Duplication:           [PASS/WARN] (N duplicates)
Check 5 — Skill classification:  [PASS/FAIL] (N action / M knowledge)
Check 6 — Security rules:        [PASS/WARN] (N/4 categories)
Check 7 — Layer balance:         [PASS/WARN/FAIL] (L0: N% / L1: N% / L2: N%)

Overall: [HEALTHY / NEEDS ATTENTION / DEGRADED]
```

If any check is FAIL or WARN, provide specific fix recommendations with exact file paths and what to change.
