# Constructing Three Approaches

## The Three Archetypes

Every qplan analysis generates exactly 3 distinct approaches representing different strategic philosophies:

### 1. The Fortress (Battle-Tested Standard)
**Philosophy**: Use proven, industry-standard patterns

**Characteristics**:
- Well-documented, mature technologies
- Patterns used by industry leaders
- Low risk, high confidence
- Strong community support
- Extensive tooling and libraries

**When to choose**:
- Mission-critical features
- Team lacks expertise in this area
- Timeline is tight
- Stakeholder risk-aversion

**Example**: "REST API with Express, PostgreSQL, Redis caching"

### 2. The Avant-Garde (Cutting-Edge Innovation)
**Philosophy**: Use modern, innovative approaches

**Characteristics**:
- Latest technologies and patterns
- Improved developer experience
- Better performance or features
- Smaller community (but growing)
- Fewer mature tools

**When to choose**:
- Competitive advantage needed
- Team enjoys learning new tech
- Long-term investment in tooling
- Performance is critical

**Example**: "GraphQL with Hasura, Prisma ORM, tRPC for type safety"

### 3. The Synthesizer (Outside-the-Box Rethink)
**Philosophy**: Question assumptions, simplify radically

**Characteristics**:
- Challenges the problem statement
- Minimalist, unconventional approach
- Often combines ideas from different domains
- May eliminate perceived requirements
- Paradigm-shifting perspective

**When to choose**:
- Current approaches feel too complex
- Recurring problems with standard solutions
- Opportunity for 10x improvement
- Team open to experimentation

**Example**: "Skip backend API entirely—use edge functions + client-side state machine"

---

## Construction Process

### Phase 1: Synthesize Intelligence Reports

Before constructing approaches, integrate the 4 agent reports:

1. **Habitat Report** (Explore agent)
   - What exists in the codebase?
   - What patterns should we follow?
   - What technical debt to avoid?

2. **API Research** (Plan agent)
   - What external services are available?
   - What are best practices?
   - What are known gotchas?

3. **UX Research** (Plan agent)
   - How do industry leaders implement this?
   - What delights users?
   - What are accessibility requirements?

4. **Risk Research** (Plan agent)
   - What commonly breaks?
   - What security issues exist?
   - What edge cases are critical?

**Synthesis Question**: "Given ALL this intelligence, what are fundamentally different ways to solve this problem?"

### Phase 2: Generate Approach 1 (The Fortress)

**Step 1**: Identify the industry-standard solution
- From API Research: What do docs recommend?
- From UX Research: What do leaders use?
- From Risk Research: What avoids known issues?

**Step 2**: Adapt to habitat
- From Habitat Report: How does this fit existing patterns?
- What components can we reuse?
- What do we need to add?

**Step 3**: Write the approach
```markdown
[36m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m
[36m APPROACH 1: The Fortress - [Name][0m
[36m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m

**The Vision**: [One sentence philosophy]

### The Architect: Structural Logic
[Design patterns, modularity, type safety]

### The Visionary: UX "Wow" Factors
[User experience, delight factors]

### The Auditor: Chaos Test Strategy
[Failure modes, edge cases, testing]

**Excellence Score: X/15**
- Integrity: X/5 | Resilience: X/5 | Delight: X/5
**Decision Metrics:** α: X.X | Ω: X.X | δ: X.X

**Rationale for scores**: [Justify based on tri-lens analysis]
```

### Phase 3: Generate Approach 2 (The Avant-Garde)

**Step 1**: Identify cutting-edge alternatives
- From API Research: Newer, better APIs?
- From UX Research: Modern design patterns?
- What's possible now that wasn't 2 years ago?

**Step 2**: Consider "New Box" thinking
- What if we used [modern framework]?
- What if we prioritized [developer experience]?
- What if we built for [10x scale] from day 1?

**Step 3**: Balance innovation with pragmatism
- Is this too bleeding-edge?
- Does the team have capacity to learn?
- Are there production-ready libraries?

**Step 4**: Write the approach (same template as Approach 1)

### Phase 4: Generate Approach 3 (The Synthesizer)

**Step 1**: Challenge assumptions
- Do we really need [component]?
- What if we [opposite of standard approach]?
- Can we eliminate entire categories of complexity?

**Step 2**: Cross-pollinate ideas
- What would [different industry] do?
- Can we borrow patterns from [different domain]?
- What's the minimalist version?

**Step 3**: Validate it's not just "weird for weird's sake"
- Does it actually solve the problem better?
- Is it defensible with real advantages?
- Would a senior engineer respect this approach?

**Step 4**: Write the approach (same template)

---

## Ensuring Approaches Are Truly Distinct

### Good Divergence:
```
Approach 1: REST API with Express + PostgreSQL
Approach 2: GraphQL with Prisma + Hasura
Approach 3: Edge functions + distributed state (no backend)
```
✅ Fundamentally different architectures

### Bad Divergence:
```
Approach 1: React with Redux
Approach 2: React with MobX
Approach 3: React with Context API
```
❌ Same framework, only state management differs

**Test for distinctness**: "If I pick Approach X, can I easily switch to Approach Y later?"
- If YES → Not distinct enough
- If NO (requires major refactoring) → Truly distinct ✅

---

## Common Approach Patterns by Domain

### API Development
- **Fortress**: REST with Express/FastAPI
- **Avant-Garde**: GraphQL with strong typing
- **Synthesizer**: Edge functions + event-driven

### Frontend Features
- **Fortress**: React with established libraries
- **Avant-Garde**: Modern framework (Svelte, SolidJS)
- **Synthesizer**: Web components, no framework

### Data Storage
- **Fortress**: Relational DB (PostgreSQL)
- **Avant-Garde**: Multi-model (PostgreSQL + Redis + Search)
- **Synthesizer**: Event sourcing + CQRS

### Authentication
- **Fortress**: JWT with refresh tokens
- **Avant-Garde**: OAuth 2.0 + OIDC with Auth0/Clerk
- **Synthesizer**: Passkeys (WebAuthn) + biometrics

---

## Integration with Scoring

After writing all 3 approaches:

1. **Apply tri-lens analysis to each**
2. **Score using 0-15 criteria**
3. **Calculate decision metrics** (α, Ω, δ)
4. **Compare scores honestly** (don't inflate to make a favorite win)
5. **Recommend the highest-scoring approach** (or justify deviation)

---

## Final Validation Checklist

Before presenting approaches to user:

- [ ] All 3 approaches are truly distinct (not minor variations)
- [ ] Each approach has tri-lens analysis (Architect/Auditor/Visionary)
- [ ] Each approach has excellence scores (Integrity/Resilience/Delight)
- [ ] Each approach has decision metrics (α/Ω/δ)
- [ ] Approaches integrate intelligence from all 4 agent reports
- [ ] Recommendation is clear and justified
- [ ] Interrogation questions capture key uncertainties

---

Use this framework to construct 3 approaches for every qplan analysis.
