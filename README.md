# Ops-Skills

Shared agent skill library for the EDU Ops team. Each directory contains a SKILL.md and supporting reference files that define how a Claude Managed Agent performs a specific task.

These are **standalone skills** — they can run independently on any address, state, or topic without the DD pipeline. The [DD Pipeline repo](https://github.com/EDU-Ops-Team/alpha-dd-pipeline) references this repo as a submodule for the two skills it uses (ease-of-conversion and school-approval), and keeps its pipeline-specific skills in its own `skills/` directory.

## Skills

### DD Pipeline Standalone Skills

These skills can run on their own and are also consumed by the DD pipeline via submodule:

| Skill | Pipeline WU | Purpose |
|---|---|---|
| `ease-of-conversion` | AGENT-02 | AI-first Site Investigation Report — zoning, permitting, code analysis, E-Occupancy scoring. Run on any address. |
| `school-approval` | AGENT-03 | State education regulatory scoring — approval difficulty, timeline, gating requirements. Run on any state. |

### General Skills

| Skill | Purpose |
|---|---|
| `center-of-gravity` | Find expert consensus on a topic — parallel research, synthesis, watchlist management |
| `check` | Multi-agent code review — 4 parallel detective squads + CoVe verification |
| `config-health` | Audit Claude Code global configuration for structural health |
| `qplan` | Two-track planning intelligence — parallel research + skeleton-to-spec deep planning |

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
```

## Repo Structure

```
Ops-Skills/
├── ease-of-conversion/       # Standalone SIR generation (also used by DD pipeline as AGENT-02)
│   ├── SKILL.md
│   └── references/
├── school-approval/          # Standalone school approval scoring (also used as AGENT-03)
│   ├── SKILL.md
│   └── references/
├── center-of-gravity/
├── check/
├── config-health/
└── qplan/
```
