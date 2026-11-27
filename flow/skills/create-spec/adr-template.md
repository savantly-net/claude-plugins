# ADR Template

Use this format for every technology decision record created in Phase 6.

```markdown
# ADR-NNN: [Decision Title]

## Status
PROPOSED | ACCEPTED | DEFERRED

## Context
[Why this decision needs to be made — the problem or need]

## Options Considered

### Option A: [Name]
- **Pros:** ...
- **Cons:** ...
- **Cost/Effort:** ...

### Option B: [Name]
- **Pros:** ...
- **Cons:** ...
- **Cost/Effort:** ...

### Option C: [Name] (if applicable)
- **Pros:** ...
- **Cons:** ...
- **Cost/Effort:** ...

## Recommendation
[Your suggested option and why, but explicitly noted as a recommendation, not a decision. When no requirement forces a one-way choice, prefer the option that is cheaper to undo.]

## Reversibility
**Two-way** (cheap to undo) | **One-way** (costly to undo). [What a reversal would touch, and which component boundary contains it. For a one-way choice, say what makes it one-way.]

## Decision
[PENDING — to be finalized by human]

## Consequences
[What follows from this decision — trade-offs, follow-on work, constraints it creates]
```
