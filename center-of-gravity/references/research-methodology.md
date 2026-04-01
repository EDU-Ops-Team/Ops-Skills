# Research Methodology: Center of Gravity Analysis

This document describes the research approach used by the center-of-gravity skill in detail, for transparency and reproducibility.

## Overview

The center-of-gravity analysis is a structured expert opinion synthesis method. It does not perform original empirical research. Instead, it aggregates and cross-references the published positions of recognized subject matter experts to identify areas of consensus and disagreement. The value lies in saving the user dozens of hours of reading by systematically mapping the landscape of expert thought.

## Phase 1: Expert Discovery

### Search Strategy

Execute multiple web searches with varied query formulations to avoid single-source bias. Query categories include:

**1. Direct expert searches:**
- `"[topic] expert"`, `"[topic] leading researcher"`, `"[topic] authority"`
- `"[topic]" professor OR "research group"`

**2. Academic sources:**
- `site:arxiv.org "[topic]"` for pre-prints
- `site:scholar.google.com "[topic]"` for peer-reviewed work
- `"[topic] systematic review"` and `"[topic] meta-analysis"` for synthesized evidence
- `"[topic]" "literature review" OR "survey paper"`

**3. Social and community sources:**
- `site:news.ycombinator.com "[topic]"` for technical community discourse
- `site:reddit.com "[topic]" expert OR "years of experience"` for practitioner perspectives
- `site:x.com "[topic]"` for real-time expert commentary and threads

**4. Newsletters and long-form content:**
- `site:substack.com "[topic]"` for expert newsletters
- `"[topic]" podcast transcript` for spoken expert content
- `"[topic]" conference keynote OR "conference talk"` for presentations
- `"[topic]" interview` for Q&A-format expert content

**5. Contrarian and critical sources (essential for capturing divergence):**
- `"[topic] criticism"`, `"[topic] overrated"`, `"[topic] problems"`
- `"against [topic]"`, `"[topic] is wrong"`, `"rethinking [topic]"`

Contrarian searches are not optional. Without them, the analysis skews toward consensus confirmation and misses important dissenting voices.

### Expert Identification Criteria

A candidate is considered a potential expert if they meet at least one of these conditions:

- Published peer-reviewed research on the topic
- Hold a professional role where they work with the topic daily (5+ years)
- Authored a widely-read book or major publication on the topic
- Regularly invited to speak at conferences or advise organizations on the topic
- Recognized by other experts as a key voice (cited, referenced, interviewed)

### Credibility Tier Definitions

| Tier | Definition | Signal Weight | Typical Sources |
|------|-----------|---------------|-----------------|
| Academic Researcher | Faculty, research scientist, or PhD-holding researcher who publishes in the field | High for theory and evidence | University pages, Google Scholar, ResearchGate, arXiv |
| Industry Practitioner | Professional who builds, manages, or operates systems related to the topic (5+ years) | High for practical applicability | LinkedIn, company blogs, conference talks, case studies |
| Thought Leader | Influential voice with large audience but may lack formal credentials in the specific field | Moderate; strong for trends | X/Twitter, newsletters, podcasts, YouTube |
| Author | Published a book-length or major long-form treatment of the topic | Moderate-to-High depending on recency | Amazon, publisher sites, book reviews, author interviews |

Experts may hold multiple tiers. A professor who also consults for industry and wrote a popular book would be tagged as Academic Researcher + Industry Practitioner + Author.

### Expert Registry Integration

Before running new searches, check `data/experts-registry.json` for experts already tagged with this topic or closely adjacent topics. Pre-vetted experts carry forward with their existing vetting score but still undergo Phase 2 deep dive to capture any updated positions. This avoids redundant vetting and builds institutional knowledge over time.

## Phase 2: Expert Vetting

### Vetting Criteria

Each candidate must satisfy **at least 2 of 5** criteria to be included in the analysis:

| # | Criterion | What to Look For |
|---|-----------|-----------------|
| 1 | Publication record | Substantive written content on the topic — articles, papers, books, detailed blog posts. Social media posts alone do not qualify. |
| 2 | Peer recognition | Other credible sources cite, reference, interview, or recommend this person. Look for mentions in other experts' work, conference invitations, advisory roles. |
| 3 | Demonstrated expertise | Their professional role or research program directly involves the topic. Not just opinions from an adjacent field. |
| 4 | Consistency | Has engaged with the topic across multiple pieces over an extended period. A single viral post is insufficient. |
| 5 | Recency | Has published on the topic within the last 3 years. For fast-moving fields, within the last year is preferred. |

The 2-of-5 threshold balances inclusivity with quality. Requiring only 1 criterion would admit too many casual commentators. Requiring 3+ would exclude emerging experts who may have strong recency and publication record but haven't yet accumulated peer recognition.

### Disqualification Signals

Remove candidates who exhibit any of these patterns:

- **Conflict of interest**: Primarily promote a product, service, or company related to their advice. Their "expert opinion" is functionally marketing.
- **Unsupported claims**: Positions lack reasoning or evidence. Assertions without argumentation do not qualify as expert opinion.
- **Echo chamber presence**: Only appear in self-published or promotional contexts. Never cited, referenced, or interviewed by independent sources.
- **Intellectual dishonesty**: Contradict well-established evidence without acknowledging the counterarguments. Ignoring opposing evidence is a disqualification signal; engaging with it and arguing against it is not.

Disqualification is noted but the candidate is not deleted from records — they are simply excluded from the current analysis with a reason noted.

## Phase 2: Deep Source Reading

### Source Selection Priorities

For each vetted expert, sources are ranked by preference:

1. **Primary sources**: Content written or spoken by the expert themselves (blog posts, articles, books, talk transcripts, interviews where they are the subject).
2. **Recent over old**: Content from the last 3 years is preferred. Older content is included only if it represents a foundational or widely-cited work.
3. **Long-form over short-form**: A 3,000-word article is preferred over a tweet thread. Depth of reasoning matters.
4. **Free over paywalled**: If a paywalled source cannot be fetched, search for an equivalent free source (preprint, talk recording, interview summary, cached version).

### Extraction Framework

From each source, extract the following:

- **Position statement**: What does this expert believe about the topic? (2-3 sentences)
- **Supporting arguments**: What reasons or evidence do they provide? (bullet points)
- **Specific advice**: Any concrete, implementable recommendations
- **Boundary conditions**: When does the expert say their advice does NOT apply?
- **Confidence language**: Does the expert express certainty ("always do X") or hedging ("in most cases, consider X")? This informs final confidence scoring.
- **Date of publication**: Critical for temporal analysis.

### Registry Updates

After vetting and deep dive, write or update entries in `data/experts-registry.json`. Include: name, slug, tiers, topics, affiliation, vetted date, last seen active date, vetting score, criteria met, key sources with URLs and dates, and brief notes on their focus area. If an expert already exists in the registry, update their last_seen_active date and add any new topics or sources.

## Phase 3: Cross-Referencing

### Convergence Detection

Two experts "converge" on a point when they independently arrive at the same conclusion, recommendation, or factual claim. Key rules:

- **Independence matters**: Two experts citing the same single study and drawing the same conclusion counts, but is noted as potentially originating from a single source.
- **Semantic matching, not keyword matching**: "Use feature flags for gradual rollouts" and "Deploy behind toggles to reduce blast radius" are the same convergence point expressed differently. Match on meaning, not wording.
- **Granularity**: Prefer specific convergence points over vague ones. "Use caching" is too broad. "Use read-through caching at the application layer for database-heavy reads" is appropriately specific.

### Divergence Detection

Divergence is tracked when experts explicitly contradict each other or recommend incompatible approaches. Types of divergence:

| Type | Definition | Example |
|------|-----------|---------|
| Factual | Experts disagree on what is true | "X causes Y" vs. "X does not cause Y" |
| Values | Agree on facts but prioritize differently | "Optimize for speed" vs. "optimize for safety" |
| Methodological | Agree on goal but disagree on how | "Use microservices" vs. "use a modular monolith" |
| Scope-dependent | Different advice for different contexts | "For startups, do X" vs. "For enterprises, do Y" |

Scope-dependent disagreements are the most common and most useful. Explicitly look for conditions that reconcile apparently opposing positions. When a disagreement is scope-dependent rather than absolute, that reconciliation IS the insight.

### Confidence Scoring

The confidence score `N/M` counts independent expert voices:

- **N** = number of experts whose position aligns with the point
- **M** = total experts who addressed this specific subtopic (not all experts will address every subtopic)
- If only 3 of 7 experts discussed caching, and 2 agreed, the score is `2/3`, not `2/7`

Strength categories:
- **Strong consensus** (70%+ of addressees agree): High confidence. Flag as reliable for decision-making.
- **Moderate consensus** (40-69%): Emerging or partial consensus. Worth noting but verify in your specific context.
- **Weak signal** (2-3 experts, <40%): Minority but potentially important position. May represent a leading indicator.

### Temporal Assessment

Each finding is tagged based on publication dates and detected trend signals:

- **[STABLE]**: The oldest supporting source is 3+ years old, the newest source agrees with the oldest, and no expert has published a retraction, update, or pivot. This consensus has stood the test of time.
- **[SHIFTING]**: At least one expert has changed their position in the last 2 years, OR a new development (technology release, research paper, major incident) has triggered re-evaluation across the community. Explain what is changing and in which direction.
- **[EMERGING]**: The majority of sources are less than 2 years old and the topic did not have significant expert coverage before that. Positions are still forming. Note which direction early signals point.

If the topic has a clear historical arc (e.g., "best practices changed dramatically after event X"), include this context in the report.

## Diff-Based Change Detection

When a previous report exists for the same topic (stored in `data/reports/{topic-slug}/latest.md`), compare the new findings against it to populate the "What Changed" section. Track:

1. **New experts**: Experts discovered in this run that were not in the prior report
2. **Shifted positions**: Experts whose core position has changed from the prior report
3. **Confidence changes**: Convergence points where the N/M score increased or decreased
4. **New divergence**: Disagreements not present in the prior report
5. **Temporal shifts**: Findings whose tag changed (e.g., STABLE → SHIFTING, EMERGING → STABLE)

This diff section is the primary value for recurring watchlist runs — the user can quickly scan what changed without re-reading the full report.

## Limitations

This methodology has inherent limitations:

1. **Web accessibility bias**: Experts who publish openly on the web are overrepresented. Experts who primarily publish in paywalled journals, speak at closed events, or advise privately may be underrepresented.
2. **English-language bias**: Search queries are in English, biasing toward English-speaking experts. Important perspectives from non-English experts may be missed.
3. **Recency bias**: Recent, SEO-optimized content may surface more readily than older foundational work.
4. **Search engine bias**: Results are filtered through the search engine's ranking algorithm, which may not surface the most credible sources first.
5. **Snapshot in time**: The report reflects what was findable on the web at the time of research. Expert positions evolve.
6. **Source availability**: Paywalled content, removed pages, and rate-limited sites may prevent access to important sources.

These limitations are mitigated by:
- Using diverse query formulations across multiple source categories
- Explicitly searching for contrarian and critical views
- Flagging temporal status on every finding
- Maintaining a persistent expert registry that accumulates knowledge across runs
- Diff-based reporting that catches shifts over time
