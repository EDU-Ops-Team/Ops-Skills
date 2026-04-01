# Excellence Scoring Criteria

## Scoring Scale: 0-15 Total

Balance quality with pragmatic constraints. For a solo developer, time and complexity
matter — but don't let them override a genuinely better architectural choice.

### Architectural Integrity (0-5)

**5/5 - Exceptional**
- Clean separation of concerns (SRP, DRY)
- Self-healing architecture (graceful degradation)
- Comprehensive type safety
- Zero side effects in pure functions
- Modular, composable design

**4/5 - Strong**
- Good modularity with minor coupling
- Most code is type-safe
- Generally follows SOLID principles
- Few side effects, mostly contained

**3/5 - Adequate**
- Basic structure present
- Some coupling or duplication
- Partial type coverage
- Side effects not fully isolated

**2/5 - Weak**
- Poor separation of concerns
- Significant technical debt
- Minimal type safety
- Tight coupling throughout

**1/5 - Critical Issues**
- Monolithic, tightly coupled
- No clear architecture
- Type safety absent
- Side effects everywhere

**0/5 - Broken**
- Architectural anti-patterns
- Impossible to maintain or extend

### Resilience & QA (0-5)

**5/5 - Exceptional**
- Handles all failure modes by design
- Edge cases are first-class citizens
- Comprehensive error recovery
- Chaos testing built-in
- Human-intent validation

**4/5 - Strong**
- Most failure modes handled
- Key edge cases covered
- Good error handling
- Testable design

**3/5 - Adequate**
- Happy path works reliably
- Basic error handling
- Some edge cases missed
- Tests exist but incomplete

**2/5 - Weak**
- Fragile under stress
- Minimal error handling
- Edge cases ignored
- Few or no tests

**1/5 - Critical Issues**
- Fails on invalid input
- No error handling
- Untested

**0/5 - Broken**
- Crashes on normal use
- No resilience whatsoever

### User Delight (0-5)

For backend features, interpret "delight" as developer/operator experience:
- API ergonomics, clear error messages, operational simplicity
- For frontend features, use the standard UX criteria below

**5/5 - Exceptional**
- Sleek, professional interface (UI) or elegant API design (backend)
- Responsive / mobile-friendly (if applicable)
- Instant feedback, clear status communication
- Low cognitive load
- Accessible

**4/5 - Strong**
- Clean, intuitive design
- Good feedback loops
- Professional feel

**3/5 - Adequate**
- Functional
- Works but unremarkable
- Basic usability

**2/5 - Weak**
- Clunky or confusing
- Minimal feedback
- High friction

**1/5 - Critical Issues**
- Confusing, frustrating
- No feedback mechanisms

**0/5 - Broken**
- Unusable

## Scoring Guidelines

### How to Score Each Approach

1. **Read the approach description carefully**
2. **Evaluate each dimension independently** (don't let one score influence others)
3. **Compare to scoring criteria** (be honest, not generous)
4. **Document the rationale** (why this score and not higher/lower)
5. **Calculate total**: Sum of 3 scores (0-15)

### Common Scoring Mistakes

- **Anchoring to middle scores** (everything gets 3/5) — FIX: Force rank, which is truly best?
- **Halo effect** (good architecture → assume good UX) — FIX: Score each dimension independently
- **Inflating scores to justify a recommendation** — FIX: Let scores drive recommendation, not the reverse
- **Ignoring implementation complexity entirely** — FIX: For a solo dev, an approach that takes 10x longer with marginal quality gain should be noted in rationale

### Excellence Thresholds

- **13-15**: World-class, gold standard
- **10-12**: Production-ready, solid choice
- **7-9**: Acceptable but has gaps
- **4-6**: Needs significant improvement
- **0-3**: Back to the drawing board

## Example Scoring

### Approach: Add Redis Caching

**Architectural Integrity: 4/5**
- Modular cache layer
- Type-safe cache keys
- Minor coupling to business logic
- RATIONALE: Clean abstraction but cache keys scattered across codebase

**Resilience & QA: 5/5**
- Fallback to DB if Redis down
- Cache warming on startup
- TTL handling for stale data
- Comprehensive tests
- RATIONALE: Handles all failure modes gracefully

**User Delight: 3/5**
- Faster page loads (good)
- No visual feedback for cache hits
- Mobile experience unchanged
- RATIONALE: Performance improvement but invisible to users

**Total: 12/15**

---

Use this criteria consistently across all 3 approaches in every qplan analysis.
