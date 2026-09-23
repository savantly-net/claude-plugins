# Blueprint Phases

## Phase 1: Scope & MVP Cut

Read all requirements (business, functional, non-functional) and the open questions.

Present a table of every functional requirement group with a recommended classification:

- **MVP** — must ship in the first release
- **Post-MVP** — important but can follow
- **Out of scope** — not planned

For each classification, explain your reasoning briefly. Consider:
- What's the minimum set that delivers BR-1 (core value proposition) end-to-end?
- Which NFRs are hard constraints vs. optimization targets?
- Which open questions block MVP vs. can be resolved later?

Present and ask: "Does this MVP cut feel right? Anything to move in or out?"

Write a brief MVP definition statement summarizing what the first release delivers and what it explicitly does not.

Wait for confirmation before proceeding.

## Phase 2: Epics & Stories

For each functional requirement group that is in MVP scope, create an epic. For each FR within that group, create one or more stories.

**Epic format:**
```markdown
# Epic: [Name]
Covers: FR-X.1 through FR-X.N
Dependencies: [other epics this depends on]

## Stories
[list of stories]
```

**Story format:**
```markdown
### [Story Title]
**Refs:** FR-X.Y, NFR-Z
**Description:** [what needs to be built, in implementation terms]
**Acceptance Criteria:**
- [ ] [specific, testable criterion]
- [ ] [another criterion]
- [ ] 👤 [a criterion only a person can verify — mark it, and say what the person does]
**Dependencies:** [other stories or epics this blocks on]
**Notes:** [any implementation hints, edge cases, or warnings]
```

Keep stories small enough to be implementable in a focused session but large enough to be meaningful. If a story feels too big, split it. If it feels trivial, merge it.

### Every criterion names what verifies it, and a machine is the default

**Write each criterion so that a test can close it.** A session builds the story, runs the
suite, and ticks the box. That is the loop the flywheel runs, and a criterion a machine cannot
reach steps out of it.

**Reach in this order:** a unit test, then an integration test, then a scripted check against
the database or the API, then a person. **Stop at the first one that can see the effect.**

**Mark a criterion that needs a person with 👤 when you write it**, and say what the person
does. A marked criterion is countable, so the plan can state how much of itself depends on
somebody being at the keyboard. **An unmarked one is discovered at the close**, when a session
has already built the story and cannot tick the box.

**State an artefact, not a property.** "The install takes under 30 minutes" needs a person with
a stopwatch. "`README.md` lists every install step, and a test asserts the script exits 0 on a
clean checkout" needs a file. **The second is usually available, and it is usually the thing
the first was really asking about.**

⚠️ **Some criteria genuinely need a person** — a judgment about whether a message reads well, a
timing a human experiences, a visual check. **Do not contort those into fake tests.** Mark them,
count them, and let the developer see the total before the plan is agreed.

For post-MVP items, create a separate summary list of deferred epics/stories (no full detail needed).

Present epics one at a time. Ask: "Does this breakdown feel right? Too granular? Too coarse? Any stories missing?"

Wait for confirmation on all epics before proceeding.

## Phase 3: Dependency Mapping

Analyze the epics and stories to produce a dependency graph:
- Which epics must be completed before others can start?
- Which stories within an epic have internal ordering constraints?
- Are there any circular dependencies (red flag)?
- Which epics/stories can be parallelized?

Present as a textual dependency diagram or ordered list showing:
1. What can start immediately (no dependencies)
2. What unblocks next
3. Critical path — the longest chain of sequential dependencies

Ask: "Does this ordering make sense? Any dependencies I'm missing or overstating?"

Wait for confirmation before proceeding.

## Phase 4: Spikes & Prototypes

Review the full plan for unknowns and risks that should be validated before committing to full implementation.

For each spike:
- **Title** — what are we investigating?
- **Question** — the specific uncertainty to resolve
- **Why it matters** — what breaks if the assumption is wrong?
- **Approach** — how to validate (prototype, benchmark, PoC, vendor conversation)
- **Success criteria** — what answer means "proceed as planned" vs. "re-evaluate"
- **Blocks** — which epics/stories are blocked until this is resolved?

Spikes should come from:
- Technology choices that haven't been proven in this specific context
- Performance targets (NFRs) that are ambitious
- Integration points with external systems
- Open questions that are marked as blockers

Present and ask: "Any other unknowns that worry you? Should any of these be resolved before we start, or can they run in parallel with early work?"

Wait for confirmation before proceeding.

## Phase 5: Technical Design

For each component identified in `architecture/components.md`, produce a technical design document.

**Design document structure:**
```markdown
# [Component Name] — Technical Design

## Purpose
[One paragraph — what this component does, referencing architecture/components.md]

## Technology
[Reference the relevant ADR(s)]

## API / Interfaces
[Specific endpoints, function signatures, message formats, or CLI commands this component exposes]

## Data Model
[Key entities, schemas, or state this component manages — tables, message shapes, config structures]

## Internal Behavior
[How the component works internally — key algorithms, state machines, decision logic]

## Integration Points
[How this component connects to others — what it calls, what calls it, message formats]

## Configuration
[What's configurable — environment variables, config files, feature flags]

## Error Handling
[How failures are handled — retries, fallbacks, dead letters, escalation]

## Observability
[What metrics, traces, and logs this component emits]
```

If you don't have enough information to design a section concretely, mark it as `[NEEDS SPIKE: reference spike title]` rather than guessing.

Present designs one at a time. Ask: "Does this design feel right? Anything over-specified or under-specified?"

Wait for confirmation on all designs before proceeding.

## Phase 6: Infrastructure Plan

Produce a provisioning plan for all infrastructure needed to run the system.

For each piece of infrastructure:
- **What** — service/resource name
- **Where** — AWS region, EKS cluster, namespace
- **Config** — key sizing and configuration decisions
- **Prerequisites** — what must exist before this can be provisioned
- **Provisioning method** — Terraform, Helm, manual, etc. (suggest, don't decide)

Order the list by dependency — what must be provisioned first.

Identify any infrastructure decisions that are still open (instance types, sizing, etc.) and flag them.

Present and ask: "Does this infrastructure plan match your environment? Anything missing or incorrectly assumed?"

Wait for confirmation before proceeding.

## Phase 7: Implementation Phases

Synthesize everything into a phased build plan. Each phase should:
- Have a clear **goal** — what capability exists at the end of this phase that didn't before?
- List the **epics/stories** included
- List the **infrastructure** to be provisioned
- List the **spikes** to be completed (if any are prerequisites)
- Define a **phase gate** — what must be true to move to the next phase?

Phases should be ordered so that:
1. Early phases deliver a working (if limited) end-to-end flow as soon as possible
2. Later phases add breadth, scale, and polish
3. Spikes are front-loaded or run in parallel with early phases
4. Each phase gate is objectively verifiable, **and each clause names what verifies it** — a
   clause only a person can check is marked 👤, because the flywheel will stop on it and hand
   it to the developer

Present and ask: "Does this phasing make sense? Want to reorder anything or adjust the phase gates?"

Wait for confirmation, then write all documents.

## After writing: offer the dev process

Once all documents are written, ask one final question: "Want me to scaffold the session-flywheel dev process on top of this plan? (`/create-flywheel` — tracked Status columns on the requirement tables, STATUS.md generated from this plan, per-workstream pointers, HANDBOOK, and the session protocol in AGENTS.md.)"

If yes, invoke the `create-flywheel` skill in greenfield mode against this project directory. If the skill is not available, say so and point at the `flow` plugin (`/plugin install flow@savantly`).
