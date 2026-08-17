# Use-Case Assessment Phases

Each phase has a **review gate** — present your work, wait for the user to confirm or redirect, then proceed. Never batch phases.

## Phase 1: Intake & Clarify

1. If a brief was provided, read it. Otherwise capture the user's verbal description verbatim.
2. Read the repo root `CLAUDE.md` (and `AGENTS.md` if present) so you know the project's conventions, tech stack, and hard constraints before reasoning about the use-case.
3. Produce a **plain-English summary** (3-5 paragraphs) of the use-case:
   - Who the user / actor is
   - The triggering situation
   - The outcome they want
   - Success signal — how we'd know it works
   - Any constraints or non-goals the brief mentions
4. List **clarifying questions** (3-7, no more). Target the ambiguities that would materially change the design — not trivia. Examples of good questions:
   - Which user role triggers this? (admin, end-user, internal agent, external system)
   - Does this replace existing behavior or run alongside it?
   - Is this multi-tenant-aware from day one?
   - Is there a deadline or compliance reason driving it?
5. Present summary + questions. Wait for answers.

**Gate:** proceed only after the user confirms the summary and answers the material questions. It's fine to loop here — better to clarify now than in Phase 5.

## Phase 2: Codebase Reconnaissance

Delegate broad exploration to an `Explore` subagent to keep the main context clean.

Brief for the Explore agent:
- The confirmed use-case summary (from Phase 1)
- Ask it to identify: which directories / modules / services / data models / API routes / workers / config keys this use-case would touch
- Ask it to surface the dominant patterns in those subsystems (e.g., "services extend `BaseService` and take `ServiceDependencies`", "all queries filter by `orgId`")
- Ask it to flag any "Don't" rules, frozen surfaces, or architectural decisions from `CLAUDE.md` that the use-case might bump into
- Ask for concise output — an annotated map, not raw file dumps

Produce an **Impact Map**:

```markdown
## Impact Map

### Touched subsystems
- **<area>** — `path/to/dir` — existing responsibility, what this use-case would need from it
- ...

### Dominant patterns in these areas
- ...

### Hard constraints to respect
- (quote the constraint + source, e.g., "CLAUDE.md line 87: expert MCP tool surface is frozen")

### Possibly relevant but uncertain
- (things the Explore agent flagged but wasn't sure about)
```

Present the Impact Map. Ask: *"Does this cover the right surface area? Anything missing, or anything here that shouldn't be?"*

**Gate:** wait for confirmation. If the user redirects attention, re-run the Explore agent with a refined brief.

## Phase 3: Fit Analysis

For each touched subsystem in the Impact Map, write a fit/gap entry:

```markdown
### <Subsystem>

**What exists today:** <grounded in actual code, cite file paths>
**What the use-case needs:** <specific to the use-case>
**Alignment:** <where existing patterns / abstractions fit naturally>
**Conflicts:** <where existing rules, schemas, or assumptions would be violated>
**Reusable pieces:** <specific services, helpers, or models that can be leveraged>
**New abstractions required:** <if any — be concrete, e.g., "a new repository method", "a new worker", "a new table">
```

Then produce a **Constraint Check** — for each hard constraint from the Impact Map, state whether the use-case honors it, bends it, or breaks it.

Present Fit Analysis + Constraint Check. Ask: *"Is anything here wrong or oversimplified? Any subsystem I should go deeper on?"*

**Gate:** wait for confirmation before options.

## Phase 4: Options & Tradeoffs

Present **2-3 approaches** with honest tradeoffs. Typical shape:

- **Option A — Minimal:** smallest change that delivers the outcome, even if narrow. What it doesn't address.
- **Option B — Proper:** the version that fits the architecture well. More code, more integration.
- **Option C — Ambitious:** bigger scope if the use-case implies broader capability worth building. Flag if this is scope-creep.

For each option:

```markdown
### Option <X>: <name>

**Scope:** <what's in, what's out>
**Code changes:** <bullets — files to modify, files to create>
**New abstractions:** <or reused ones>
**Data model deltas:** <schema, index, graph changes>
**Risks:** <what could bite>
**Downsides:** <what this option gives up>
```

End with your **Recommendation** — which option you'd pick and why, grounded in the codebase's existing direction.

Also: if no option is a good idea (use-case conflicts with architecture, already solved, not worth the cost), say so plainly and recommend **Option N — Don't build this**, with the reasoning.

Present. Ask: *"Which direction do you want to take — or is there a hybrid, or should we not build this?"*

**Gate:** wait for the user to pick an approach (or reject all). If rejected, stop here — no downstream artifacts. If picked, the rest of the skill elaborates that choice, not the others.

## Phase 5: Requirements Extraction

From the chosen approach, extract requirements using the same format as `/create-spec` so `/create-blueprint` can consume them.

**Business requirements (BR-N)** — the WHY. Technology-agnostic:
- ID, Requirement, Rationale, Source (which line of the use-case or user statement)

**Functional requirements (FR-X.Y, grouped by domain)** — the WHAT:
- ID, Requirement, Acceptance Criteria, Source

**Non-functional requirements (NFR-N)** — the HOW WELL:
- ID, Category (Performance | Security | Compliance | Scalability | Availability | Observability | Maintainability), Requirement, Rationale
- Inherit relevant NFRs from the existing codebase — if the project already has hard security / multi-tenant rules, restate them as NFRs here so the downstream plan respects them.

Keep this scoped to the feature — don't re-derive the whole system's requirements.

Present all three lists. Ask: *"Any requirements missing, miscategorized, or wrong?"*

**Gate:** wait for confirmation.

## Phase 6: Architecture Delta & Decisions

Produce three artifacts:

**architecture/overview.md** — how the feature sits in the existing system:
- Diagram (ASCII is fine) showing the feature's components alongside existing ones
- Data flow for the primary use-case path
- What's new, what's reused, what's modified

**architecture/components.md** — component-level detail:
- For each new or modified component: responsibility, collaborators, where it lives in the codebase
- Reference the subsystems from the Phase 2 Impact Map

**architecture/decisions/adr-NNN-<topic>.md** — one ADR per non-trivial decision introduced by this use-case. Use `adr-template.md`.

Important: do **not** re-decide settled decisions. If the existing codebase already has an ADR or documented choice (e.g., "we use Elasticsearch for search"), reference it rather than re-litigating. Create new ADRs only for decisions this use-case *newly* introduces.

Present each ADR one at a time. For each: *"Accept this recommendation, pick a different option, discuss, or defer?"* Update status accordingly.

**Gate:** wait for all ADRs to be addressed.

## Phase 7: Open Questions & Risks

Compile everything that remains unresolved:
- Gaps in the use-case brief
- Assumptions you made during analysis
- Items that need stakeholder or domain-expert input
- Risks specific to this change (data migration, breaking changes, performance, rollout)

Categorize:
- **Must resolve before `/create-blueprint`** — blockers for planning
- **Must resolve during early implementation** — important but won't block planning
- **Can resolve as we go** — watch items

Present. Ask: *"Any other questions we should capture? Anything to reprioritize?"*

Once confirmed, write all final documents per `output-structure.md` and tell the user:
- Where the files landed
- That the output is compatible with `/create-blueprint <path>`
- A one-line summary of the recommended approach
