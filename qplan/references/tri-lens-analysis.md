# Tri-Lens Analysis Guide

## The Trifecta of Senior Leadership

Every qplan analysis must filter through three senior perspectives:

### 1. The Architect (Senior Dev)
**Mindset**: "I've built systems that serve millions. I know what scales and what breaks."

**Questions to Ask**:
- What's the structural integrity of this approach?
- Does it follow SOLID principles?
- Is it modular and composable?
- Can we swap components without major refactoring?
- Where are the potential memory leaks or performance bottlenecks?
- What happens at 10x, 100x scale?

**Focus Areas**:
- Design patterns and architecture
- Dependency management
- Type safety and contracts
- Side effect containment
- Code reusability

**Output**: "From an architectural perspective, this approach..."

### 2. The Auditor (Senior QA)
**Mindset**: "I've seen every implementation break in production. Show me how THIS one won't."

**Questions to Ask**:
- What's the human intent? Does this solve the actual problem?
- What are the unhappy paths?
- What edge cases will developers miss?
- How does this fail when external services are down?
- What validation is missing?
- What happens with malformed data, null inputs, or extreme values?

**Focus Areas**:
- Failure modes and chaos scenarios
- Edge case handling
- Input validation
- Error recovery
- Test coverage strategy
- Human-intent validation (does it solve the RIGHT problem?)

**Output**: "From a QA perspective, this approach..."

### 3. The User (Practical Perspective)
**Mindset**: "Does this solve my problem simply and effectively? Will I enjoy using and maintaining this?"

For **backend features** (most Alfred work), focus on:
- Is the API ergonomic? Are tool interfaces intuitive?
- Are error messages clear and actionable?
- Is the operational overhead low? (monitoring, debugging, restarting)
- Can I understand this code in 6 months?
- Is cognitive load minimal?

For **frontend features**, additionally focus on:
- Is it mobile-friendly?
- Are loading states and feedback present?
- Is it accessible?
- Does it feel professional and polished?

**Questions to Ask**:
- Does this solve the actual problem simply?
- Would I enjoy using/maintaining this?
- Is the cognitive load minimal?
- Are error states clear and recoverable?
- Is this the simplest approach that still works well?

**Focus Areas**:
- Practical usability (UI or API)
- Operational simplicity
- Error communication
- Maintainability by a solo developer
- "Does this spark joy or dread?"

**Output**: "From a practical perspective, this approach..."

---

## How to Apply Tri-Lens Analysis

### For Each Approach (3 total)

1. **Read the approach** through the Architect lens
   - Evaluate structural soundness
   - Identify architectural strengths/weaknesses

2. **Read the approach** through the Auditor lens
   - Find failure modes
   - Identify testing gaps

3. **Read the approach** through the User lens
   - Assess practical usability and maintainability
   - Evaluate operational simplicity

4. **Synthesize insights** into a cohesive evaluation
   - Don't just list 3 separate perspectives
   - Show how they interact (e.g., "The Architect's modular design enables the Auditor's test strategy")

---

## Common Tri-Lens Patterns

### Pattern: When Perspectives Align

**Signal**: All three lenses agree on strength/weakness
- **Example**: "Caching improves architecture (modularity), QA (resilience), and UX (speed)"
- **Action**: High confidence recommendation

### Pattern: When Perspectives Conflict

**Signal**: Trade-offs between lenses
- **Example**: "Microservices (Architect: excellent) vs Monolith (Auditor: simpler to test, User: faster development)"
- **Action**: Acknowledge trade-off explicitly, recommend based on context

### Pattern: When One Lens Dominates

**Signal**: Critical issue in one lens outweighs others
- **Example**: "Approach has elegant architecture but catastrophic security flaw (Auditor)"
- **Action**: Flag as blocker, score appropriately

---

## Tri-Lens Integration with Scoring

Use tri-lens insights to justify scores:

- **Architectural Integrity** → Primarily Architect lens
- **Resilience & QA** → Primarily Auditor lens
- **User Delight** → Primarily User lens

But let all three lenses inform all three scores:
- Architect notes performance issues → Affects Delight score
- Auditor finds edge cases → Affects Integrity score
- User sees friction → Affects Resilience score (user error states)

---

## Anti-Patterns to Avoid

- **Checkbox thinking**: "Architect says good, Auditor says good, User says good" → Shallow. Show how perspectives interact.
- **Ignoring conflicts**: Pretending all lenses align when they don't → Be honest about trade-offs.
- **Bias toward complexity**: Architect loves over-engineering → Simple solutions can score 15/15 too.
- **Ignoring the solo dev context**: Enterprise patterns for one person → Always consider who maintains this.

---

Use this tri-lens framework consistently for every approach in every qplan analysis.
