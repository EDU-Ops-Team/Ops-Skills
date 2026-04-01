---
name: center-of-gravity
description: "This skill should be used when the user asks to find expert consensus on a topic, identify where experts agree or disagree, research best practices from multiple authorities, find the 'center of gravity' on a subject, map expert opinions, do deep research on what thought leaders think, synthesize expert views into recommendations, manage a research watchlist, or stay current on topics over time. Use this skill whenever the user wants to understand what leading voices in a field think about a specific question, even if they phrase it as 'what do experts say about X', 'what's the current thinking on X', 'research best practices for X', 'add topic to watchlist', or 'run my watchlist'."
version: 0.1.0
---

# Center of Gravity

Perform deep web research to identify subject matter experts on a given topic, map their positions, find convergence and divergence patterns, and synthesize findings into an actionable report with confidence scoring and temporal awareness.

## Skill Directory

This skill's directory is located at `~/.claude/plugins/local/center-of-gravity/skills/center-of-gravity/`. All relative paths below (templates, references, data) are relative to the plugin root at `~/.claude/plugins/local/center-of-gravity/`.

## Modes

Parse the user's input for flags:

- **Default (autonomous)**: Execute all four phases without pausing. Deliver the complete report at the end.
- **`--interactive`**: After Phase 1, present the expert list and pause for user input. After Phase 2, present position summaries and pause again. Proceed to synthesis only after user confirms.
- **`--watchlist`**: Process all due topics from `data/topics-watchlist.json`. For each topic where `last_run` + `frequency` ≤ today, run the full pipeline and generate a diff report. Skip topics not yet due.
- **`--add-topic "topic name"`**: Add a topic to the watchlist. Prompt for frequency (daily, weekly, biweekly, monthly) and optional scope notes. Write to `data/topics-watchlist.json`. Do not run research.
- **`--remove-topic "topic name"`**: Remove a topic from the watchlist.
- **`--list-topics`**: Display the current watchlist as a table with name, frequency, last run date, and scope notes.

## Phase 1: Expert Discovery

Goal: identify 5-10 subject matter experts on the topic.

### Check the Expert Registry

Read `data/experts-registry.json` if it exists. Search for experts already tagged with this topic or adjacent topics. Carry forward any matches as pre-vetted candidates — they still go through Phase 2 for updated positions, but skip re-vetting.

### Multi-Source Search

Execute 5+ WebSearch queries using varied formulations across all source categories:

**Academic sources:**
- `"[topic] expert" site:arxiv.org OR site:scholar.google.com`
- `"[topic] systematic review" OR "[topic] meta-analysis"`
- `"[topic]" leading researcher OR professor`

**Social and community sources:**
- `"[topic]" site:news.ycombinator.com`
- `"[topic]" expert opinion site:reddit.com`
- `"[topic]" thread site:x.com`

**Newsletters and podcasts:**
- `"[topic]" site:substack.com`
- `"[topic]" podcast transcript OR "interview with"`
- `"[topic]" conference talk OR keynote`

**Contrarian sources (critical for capturing divergence):**
- `"[topic] criticism" OR "[topic] overrated"`
- `"against [topic]" OR "[topic] problems"`

### Expert Identification

For each search result page, scan titles and snippets for recurring names. For each candidate, record:
- Full name and primary affiliation or role
- 1-2 URLs where their views on this topic appear
- Preliminary credibility tier assessment

### Credibility Tiers

Assign one or more tiers to each expert:

| Tier | Definition | Weight |
|------|-----------|--------|
| Academic Researcher | Faculty, research scientist, or PhD-holder who publishes peer-reviewed work in the field | High for theory and evidence |
| Industry Practitioner | 5+ years of hands-on professional experience with the topic | High for practical applicability |
| Thought Leader | Recognized community voice, conference speaker, large audience | Moderate; strong for trend sensing |
| Author | Published a book-length or major long-form treatment of the topic | Moderate-to-High depending on recency |

If in `--interactive` mode, present the expert list in a table and ask: "Which experts should I prioritize? Any names missing?" Wait for user response before proceeding.

## Phase 2: Expert Vetting & Deep Dive

Goal: vet each candidate and understand their specific positions.

### Vetting Criteria

Each candidate must meet **at least 2** of these 5 criteria to qualify:

1. **Publication record**: Has published substantive content (not just social media posts) on the topic
2. **Peer recognition**: Cited, referenced, or interviewed by other credible sources in the field
3. **Demonstrated expertise**: Professional role or research program directly involves the topic
4. **Consistency**: Has engaged with the topic across multiple pieces over time, not a one-off take
5. **Recency**: Has published on the topic within the last 3 years

### Disqualification Signals

Remove candidates who exhibit these patterns:
- Primarily promote a product or service related to their advice (conflict of interest)
- Positions lack supporting reasoning or evidence (pure opinion without substance)
- Only appear in self-published or promotional contexts, never cited by others
- Contradict well-established evidence without acknowledging counterarguments

Record vetting results: criteria met count (score out of 5) and which specific criteria were satisfied.

### Deep Dive

For each vetted expert (or user-selected subset in interactive mode), use WebFetch to read 1-3 of their most relevant pieces. Prioritize:
- Primary sources (their own writing) over secondary mentions
- Recent content over older content
- Long-form substantive pieces over brief mentions

Extract from each source:
- **Core position**: What does this expert believe or recommend? (2-3 sentences)
- **Key arguments**: What reasoning or evidence do they provide? (bullet points)
- **Specific recommendations**: Concrete, actionable advice
- **Caveats or conditions**: When does the expert say their advice does or does not apply?
- **Publication date**: Note the year for temporal analysis
- **Confidence language**: Does the expert express certainty or hedging?

If a source cannot be fetched (paywall, 404), note the failure and attempt an alternative URL. Do not silently drop experts due to fetch failures.

### Update Expert Registry

Write or update entries in `data/experts-registry.json` for every vetted expert. Schema:

```json
{
  "name": "Expert Name",
  "slug": "expert-name",
  "tiers": ["academic-researcher"],
  "topics": ["topic-slug"],
  "affiliation": "Organization",
  "vetted_date": "YYYY-MM-DD",
  "last_seen_active": "YYYY-MM-DD",
  "vetting_score": 4,
  "vetting_criteria_met": ["publication-record", "peer-recognition", "demonstrated-expertise", "recency"],
  "key_sources": [{"url": "...", "title": "...", "date": "YYYY-MM-DD", "type": "article"}],
  "notes": "Brief note on their focus area"
}
```

If in `--interactive` mode, present position summaries (3-4 sentences each) and ask: "Any positions to dig deeper into, or proceed to cross-referencing?" Wait for user response.

## Phase 3: Cross-Reference & Pattern Detection

Goal: identify convergence and divergence patterns with confidence scores and temporal awareness.

### Convergence Analysis

Compare all expert positions. A "convergence point" requires at least 2 experts independently supporting the same claim or recommendation. For each convergence point:

- Assign a **confidence score** as `N/M` where N = supporting experts, M = total experts who addressed this subtopic (not all experts address every subtopic)
- Rate strength: **Strong consensus** (70%+), **Moderate consensus** (40-69%), **Weak signal** (<40%)
- Use semantic matching: different wording expressing the same position counts as convergence

### Divergence Analysis

Identify areas where experts explicitly disagree. For each divergence point:
- Name the competing positions clearly
- List which experts hold which position
- Categorize the nature of disagreement:
  - **Factual**: Disagree on what is true
  - **Values**: Agree on facts but prioritize differently
  - **Methodological**: Agree on goal but disagree on approach
  - **Scope-dependent**: Different advice for different contexts (most common and most useful)
- Assess whether conditions exist under which each position is correct

### Temporal Assessment

Tag each convergence and divergence point:
- **[STABLE]**: Consensus held for 3+ years with no significant challenges
- **[SHIFTING]**: New evidence or arguments within last 1-2 years are changing the landscape. Explain what is changing and in which direction.
- **[EMERGING]**: Topic is new enough that positions are still forming (<2 years of coverage). Note which direction early signals point.

## Phase 4: Synthesis & Report

Goal: produce the final structured report.

### Generate Report

Read the report template from `templates/report-template.md` in this skill's directory. Follow its structure exactly, populating every section.

Every factual claim must have an inline citation formatted as `[Expert Name](URL)`. Do not make uncited claims about expert positions.

### Save Report

1. Write to `data/reports/{topic-slug}/{YYYY-MM-DD}.md`
2. Copy to `data/reports/{topic-slug}/latest.md`
3. Write to the user's current working directory as `center-of-gravity-report-{topic-slug}.md`

If the user specified a different output path, use that instead for step 3.

### Diff Detection

If `data/reports/{topic-slug}/latest.md` already existed before this run (a prior report), compare the current findings against it and populate the "What Changed" section:
- New experts not in the prior report
- Experts whose core position shifted
- Convergence points where the confidence score changed
- New divergence points not previously identified
- Temporal tags that changed (e.g., STABLE → SHIFTING)

If processing via `--watchlist`, update the topic's `last_run` date in `data/topics-watchlist.json`.

## Error Handling

- If fewer than 3 experts are found in Phase 1, inform the user the topic may be too niche. Offer to broaden search terms or proceed with limited data.
- If WebFetch fails for more than half the sources, note the limitation in the report and rely on search snippet data where necessary.
- If all experts agree (no divergence), note this explicitly — unanimous consensus is a meaningful finding.
- If the topic is too broad (e.g., "technology"), ask the user to narrow it before proceeding.

## Quality Checks

Before delivering the final report, verify:
- Every convergence point has a confidence score
- Every claim cites at least one expert by name with a URL
- Temporal tags are present on every convergence and divergence point
- The expert table is complete with tiers and vetting scores
- Recommendations trace back to specific convergence points
- The report follows the template structure

For detailed methodology documentation, read `references/research-methodology.md`.
