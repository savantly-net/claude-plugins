# ADR Template

Use this format for every *new* technology or design decision introduced by the use-case. If a decision is already settled by an existing ADR or documented choice in the codebase, **reference it rather than re-deciding**.

```markdown
# ADR-NNN: [Decision Title]

## Status
PROPOSED | ACCEPTED | DEFERRED

## Context
[Why this decision needs to be made, in the context of this use-case and the existing codebase. Link to the relevant part of the use-case brief and the subsystem(s) it touches.]

## Options Considered

### Option A: [Name]
- **Pros:** ...
- **Cons:** ...
- **Cost/Effort:** ...
- **Fit with existing codebase:** [how it aligns with or diverges from current patterns]

### Option B: [Name]
- **Pros:** ...
- **Cons:** ...
- **Cost/Effort:** ...
- **Fit with existing codebase:** ...

### Option C: [Name] (if applicable)
- **Pros:** ...
- **Cons:** ...
- **Cost/Effort:** ...
- **Fit with existing codebase:** ...

## Recommendation
[Your suggested option and why, explicitly noted as a recommendation, not a decision. Lean toward options that match the codebase's existing direction unless there's a strong reason to diverge.]

## Decision
[PENDING — to be finalized by human]

## Consequences
[What follows from this decision — trade-offs, follow-on work, constraints it creates, any existing code that would need to change.]

## References
- Use-case: `use-case.md#<section>`
- Related existing ADRs: [link or "none"]
- Touched subsystems: [from codebase/impact-map.md]
```
