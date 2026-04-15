# Ops-Skills

Shared agent skill library for the EDU Ops team. Each directory contains a SKILL.md and supporting reference files that define how a Claude Managed Agent performs a specific task.

## Skills

### DD Pipeline Skills (7 agents)

These skills power the [Alpha DD Pipeline](https://github.com/EDU-Ops-Team/alpha-dd-pipeline) work units. The pipeline repo references this repo as a submodule.

| Skill | Pipeline WU | Purpose | Status |
|---|---|---|---|
| `ease-of-conversion` | AGENT-02 | AI-first Site Investigation Report — zoning, permitting, code analysis, E-Occupancy scoring | Complete |
| `school-approval` | AGENT-03 | State education regulatory scoring — approval difficulty, timeline, gating requirements | Complete |
| `cds-verification-extraction` | AGENT-06 | Extract structured data from CDS-verified SIR returns | Complete — pending vendor samples |
| `vendor-bi-extraction` | AGENT-07 | Extract structured data from Worksmith inspection checklist returns | Complete — pending vendor samples |
| `isp-extraction` | AGENT-08 | Extract structured data from Program Fit Analysis PDFs | Complete |
| `opening-plan-v2` | AGENT-12 | Two-pass permitting plan — SIR baseline + 5 parallel research agents. Google Doc output | Complete (v2.2) |
| `dd-report-assembly` | AGENT-13 | Assemble DD Report Google Doc from all upstream Sindri data | Complete |

### General Skills

| Skill | Purpose |
|---|---|
| `center-of-gravity` | Find expert consensus on a topic — parallel research, synthesis, watchlist management |
| `check` | Multi-agent code review — 4 parallel detective squads + CoVe verification |
| `config-health` | Audit Claude Code global configuration for structural health |
| `qplan` | Two-track planning intelligence — parallel research + skeleton-to-spec deep planning |

## Shared Schemas

The DD pipeline's dual-column design (AI extraction → vendor verification → delta) depends on AI and vendor extractions using identical schemas:

| Schema | Location | Used By |
|---|---|---|
| `shared-sir-schema.json` | `cds-verification-extraction/references/` | WU-02 (sir_ai), WU-06 (sir_vendor), WU-09 (sir_delta) |
| `shared-inspection-schema.json` | `vendor-bi-extraction/references/` | WU-07 (inspection_vendor), WU-09 (inspection_delta) |
| `isp-extraction-schema.json` | `isp-extraction/references/` | WU-08 (isp_extract) |

## Usage

### As a submodule (DD Pipeline)

```bash
# In the alpha-dd-pipeline repo
git submodule update --remote skills/ops-skills
```

### As Perplexity Computer skills

Each skill can be loaded individually:
```
load_skill(name="ease-of-conversion", scope="user")
load_skill(name="school-approval", scope="user")
load_skill(name="opening-plan-v2", scope="user")
```

## Repo Structure

```
Ops-Skills/
├── ease-of-conversion/           # AGENT-02
│   ├── SKILL.md
│   └── references/
├── school-approval/              # AGENT-03
│   ├── SKILL.md
│   └── references/
├── cds-verification-extraction/  # AGENT-06
│   ├── SKILL.md
│   └── references/
├── vendor-bi-extraction/         # AGENT-07
│   ├── SKILL.md
│   └── references/
├── isp-extraction/               # AGENT-08
│   ├── SKILL.md
│   └── references/
├── opening-plan-v2/              # AGENT-12
│   ├── SKILL.md
│   └── references/
├── dd-report-assembly/           # AGENT-13
│   ├── SKILL.md
│   └── references/
├── center-of-gravity/
├── check/
├── config-health/
└── qplan/
```
