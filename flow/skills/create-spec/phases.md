# Decomposition Phases

## Phase 1: Ingest & Confirm Understanding

1. Read the source document or material provided.
2. Produce a **plain-English summary** (3-5 paragraphs) of what the project is about — goals, scope, who it's for, and the high-level approach described.
3. Present this summary and ask: "Did I capture this correctly? Anything to add, correct, or reframe?"
4. Wait for confirmation before proceeding.

## Phase 2: Business Requirements

Extract the **WHY** — the business-level goals, outcomes, and constraints. These should be technology-agnostic.

For each requirement, provide:
- **ID** (BR-1, BR-2, ...)
- **Requirement** — clear, testable statement
- **Rationale** — why this matters to the business
- **Source** — where in the source material this came from

Present as a table. Ask: "Any business requirements missing, or any of these off-base? Should we adjust priority or wording?"

Wait for confirmation before proceeding.

## Phase 3: Functional Requirements

Extract the **WHAT** — the specific capabilities the system must have. Group by domain/component.

For each requirement:
- **ID** (FR-X.Y format, grouped by domain)
- **Requirement** — specific, verifiable capability
- **Acceptance Criteria** — how we'd know this is done
- **Source** — charter reference

Present grouped by domain. Ask: "Are these groupings logical? Any capabilities missing or over-specified?"

Wait for confirmation before proceeding.

## Phase 4: Non-Functional Requirements

Extract the **HOW WELL** — security, compliance, availability, maintainability, etc.

### Performance, Scalability and Observability are opt-in

**These three categories enter the spec only when the human asks for them.** An invented
latency target, capacity number or metrics list becomes a constraint that every later design
must honor, and nobody asked for it.

Before you extract, ask one multi-select question: "Should the spec carry requirements for
any of these: Performance, Scalability, Observability?" The recommended answer is **none**,
unless the source states a target. When the source states one, quote it in the question, so
the human sees what they keep or drop.

For each category the human selects, choose with them:
- **Only what the source states** — capture the stated targets word for word. Invent none.
- **Propose targets** — draft targets, each marked PROPOSED, for the human to confirm.

For each category the human does not select, write no rows. Write this line under the
`non-functional.md` table: `Not specified (opted out <date>): <categories>.` Do not move
them to Open Questions: the blueprint turns open questions into spikes.

⚠️ **Audit logging that a regulation requires is Compliance or Security, not Observability.**
The opt-out never removes it.

For each:
- **ID** (NFR-1, NFR-2, ...)
- **Category** (Security | Compliance | Availability | Maintainability, plus Performance | Scalability | Observability when opted in)
- **Requirement** — measurable where possible
- **Rationale**

Pay special attention to security and compliance requirements implied by the client's industry or domain (e.g., government/defense work implies FedRAMP, ITAR, data sovereignty concerns).

Present and ask: "Any non-functional requirements I missed?" Add "Are the targets realistic?" when a target-bearing category was opted in.

Wait for confirmation before proceeding.

## Phase 5: Implementation Details

Separate out the **HOW** — architecture, components, technologies, and patterns described in the source material. This is NOT requirements — this is the proposed technical approach.

Produce:
- **Architecture Overview** — describe the system's major components and how they interact
- **Component Breakdown** — list each component with its responsibility
- **Data Flow** — how data/work moves through the system
- **Integration Points** — external systems the solution connects to

Mark everything here as the charter author's **proposed** approach, not decided. Present and ask: "Anything to add to the technical picture? We'll evaluate technology choices next."

Wait for confirmation before proceeding.

## Phase 6: Technology Decisions

For every technology choice mentioned or implied in the source material, create a decision record using the ADR template (see `adr-template.md`).

Present each decision record one at a time (or in small batches if they're closely related). For each, ask: "Do you want to accept this recommendation, go with a different option, discuss further, or defer this decision?"

Update the status based on the human's response before moving on.

Wait for all decisions to be addressed before proceeding.

## Phase 7: Open Questions

Compile everything that remains unclear, ambiguous, or assumed:
- Gaps in the source material
- Assumptions you made during decomposition
- Items that need client/stakeholder clarification
- Risks identified during analysis

Categorize as:
- **Must resolve before starting** — blockers
- **Must resolve during Phase 1** — important but won't block kickoff
- **Can resolve as we go** — nice to know

Present and ask: "Any other questions we should capture? Want to reprioritize any of these?"
