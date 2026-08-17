# Flywheel Templates

Adjust paths to the repo's layout (`docs/`, `projects/<name>/`, or root). `<date>` is today's date; `<project>` is the project name.

## 1. Tracked-table preamble (top of `requirements/functional.md` and `non-functional.md`)

```markdown
> **This table is tracked**: every row carries a Status, updated in the same commit as the story or decision that changes it. A requirement-level change is recorded here as a dated amendment on the row; this table must never contradict a decided document. Dispositions: `shipped` (story + date) · `open` (owner) · `deferred` (dated pointer to the scope doc) · `partial` (the shipped and open halves named) · `amended` (dated, with pointer).
```

Add a `Status` column to every table; initialize per mode (⬜ **Open** for greenfield; audit findings for adopt). Rows the MVP scope explicitly defers get `**Deferred**` with the pointer immediately — a deferral decided in writing is not "open".

`business.md` note:

```markdown
> BRs are goals, not shippable units — they carry no Status column. Their delivery is tracked through the FR/NFR rows that cite them.
```

## 2. `STATUS.md` skeleton

```markdown
# <project> — Build Status

> Concise tracker for all planned work. *What* each item is: `plan/` (epics, spikes, phases) and `design/`. **Requirement-level status lives in the tracked tables in `requirements/`** — this file holds what those rows can't: phases, gates, spikes, infrastructure, open decisions, and the story one-liners. *How it was built and why*: `docs/history/<story-id>.md`, one file per story. **Opening a story means reading its history file and its design doc, not just its line here.** Follow shipped code over a stale doc — then amend the doc in the same commit. Standing knowledge: `docs/HANDBOOK.md`.
>
> When a story lands: check the box here with the date and a one-line gist; write `docs/history/<story-id>.md`; update the requirement rows.
>
> **Legend:** ⬜ not started · 🟨 in progress · ✅ done · 🚫 blocked (note blocker)

**Current phase: Phase 0 — <name>** (entered <date>).

---

## Phase 0 — <name>

Gate: <gate conditions from plan/phases.md, verbatim>

### Spikes
| Status | ID | Spike | Blocks |
|---|---|---|---|
| ⬜ | S1 | <title> | <blocked stories> |

### Stories
- [ ] <id> <title>

### Infrastructure
- [ ] #1 <item>

**Phase 0 gate:** ⬜

---

<one section per remaining phase, same shape>

## Decisions still open (from open-questions.md & infra plan)

- [ ] <Qn> <question>

## Post-MVP backlog (not tracked here — see plan/mvp-scope.md)

<one-line list>
```

## 3. `docs/history/README.md` (the convention stub)

```markdown
# <project> — Build History

> **One file per story: `docs/history/<story-id>.md`.** Written in the same commit as the work it describes. Phase-gate narratives use `docs/history/phase-<n>-gate.md`.
>
> **Why these files exist when `git log` already exists:** git records what happened. These record what *didn't* — the options considered and **rejected** (which leave no commit and no diff), the paths **not exercised**, and the numbers as of that moment. That is the half a future session would otherwise pay to rediscover.
>
> One file per story rather than one shared log is deliberate: two developers landing two stories touch two different files, so this directory never produces a merge conflict.
>
> Never edit a landed entry to make it look better. Corrections are appended, dated.
```

Per-story entry template:

```markdown
# <story-id> — <title>

**Landed** <date> · **Phase** <n> · **Requirements** <FR/NFR ids> · **Owner** <name>

## What landed

<what now exists that didn't before, in terms a reader can verify against the code>

## Decisions and why

<each decision, its reasoning, and the alternative it beat>

## Rejected options

<what was considered and refused, and why — including any pressure to widen a result type, gate, or guarantee. This section is the reason this file exists; "none" is a valid answer only if it is true.>

## Traps hit

<what cost real time. Anything that will bite again goes to docs/HANDBOOK.md in this same commit.>

## Verification

- Tests: <numbers, from exit codes — not from memory>
- Mutation: <numbers for load-bearing guards>
- Click-through: <what a human actually did, and what the DB showed afterward>

## What was NOT exercised

<named plainly. An unnamed gap is a claim that none exists, and that claim is usually false.>
```

## 4. Root `NEXT_SESSION.md` (the workstream index)

```markdown
# Active Workstreams

> **Index only.** One line per active workstream; the detail lives in that workstream's own `NEXT_SESSION.md`. Each line is owned by its workstream — correct another's line only with direct evidence, and say that you did.

| Workstream | Owner | Pointer | State |
|---|---|---|---|
| <slug> | <name> | `workstreams/<slug>/NEXT_SESSION.md` | <one line: what's next, or what's blocking> |

## Dormant

<workstreams that are paused, with the date and why — so a reader knows they are not simply forgotten>
```

## 5. Workstream `NEXT_SESSION.md` initial

```markdown
# Next Session — <workstream>

> **This is a pointer, not a log and not a handbook.** It holds only *session state*: the goal, the human's decision queue, live workspace/infra state, and the baseline. Everything durable lives where it belongs — **standing knowledge: `docs/HANDBOOK.md`** · requirement status: `requirements/` (tracked tables) · phases/gates/stories/infra: `STATUS.md` · per-story narrative: `docs/history/<story-id>.md` · history: `git log`. Rewrite this file at the end of each session; if you are writing knowledge into it, you are writing it in the wrong file.
>
> **This pointer belongs to one workstream.** It is rewritten wholesale at every close, so a second developer must get their own — never share one.

## ⚠️ Operational state — read before touching anything

(none yet)

## ⚠️ Things that need <owner>

(decisions the next session must not make on its own — this is a queue a human reads, which is why this file is committed)

## Goal

<greenfield: the first phase's first stories/spikes, in dependency order, from STATUS.md>
<adopt: the audit's flagged items awaiting disposition, then the current phase's next work>

## Baseline at close

(test/lint/build state, recorded from exit codes — established in the first working session)
```

## 6. `docs/HANDBOOK.md` seed

```markdown
# <project> — Session Handbook (standing knowledge)

> Everything here is **knowledge, not state** — it changes only when something is learned or a convention is decided, in the commit that learns it. Session state lives in the workstream's `NEXT_SESSION.md`.

## Running the thing

(filled in when the app first runs: dev-server ports and collision warnings, required companion processes, health-check commands, operator scripts)

## Verification principles

- A screen that shows you what it was told cannot verify itself: verify effects in the database/storage, prices against the vendor's own ledger, delivered media by its bytes.
- A vendor's API shape is read from `node_modules` or the vendor's own pages, dated; a vendor's *legal* terms the same way — quoted and dated; third-party claims are named as such.
- Read the DB before trusting a status doc. A route proved by a spike is not a route proved by shipped code.

## Test rules

- The pure core first, then the IO. A fact the machine needs is a required argument, not a default.
- Mutation-test every load-bearing guard and record the real number.
- A human click-through, recorded in the story's `docs/history/` entry, verified against the DB. **Say what you could not exercise.**
- A fake external service must be as awkward as the real one.

## Framework & tooling traps — these have all cost real time

(append as they bite; each entry is one line with the fix)

## Shell & environment traps

(append as they bite)

## Standing gaps (named, not owed to any current session)

(append when a story ships with a stated gap)

## Conventions established (follow them)

- Requirement-level decisions are recorded on the requirement row (tracked tables); design-doc divergence is recorded in both directions, in the same commit; a conflict between two decided documents gets a memo with a recommendation, the pick staying human.
- Dependencies before stubs — and a story can be blocked by an unmade *decision*. A specified obligation with no honest implementation is refused in writing; a gap that survives a story is named at the function that carries it.
- Derive a status projection at read time; store judgements, histories, provider facts, human choices — with an observation time.
- A gate number's denominator is decided in writing before it is measured — and amended in writing too. A measurement that cannot be honest is refused, not approximated.
- Duplicate a decision's reasoning, never just its shape. Re-derive a number before copying it forward.
(append project-specific conventions as they are established)
```

## 7. Epic file header note (insert after each epic's title line)

```markdown
> **Status lives in the tracked requirement tables (`requirements/`) and `STATUS.md`; build narrative in `docs/history/<story-id>.md`.** AC checkboxes below are the *definition of done*, not a maintained status — an unchecked box on a shipped story is not a claim. Dated "as built" notes and divergence annotations here are authoritative spec amendments.
```

## 8. AGENTS.md section (append; reconcile if one exists)

```markdown
## Dev process — the session flywheel (installed <date>)

**Where things live:**
- **Requirement status**: `requirements/functional.md` + `non-functional.md` are **tracked tables** — every FR/NFR row carries a Status, updated in the same commit as the work or decision that changes it. Requirement-level decisions are dated amendments on the row.
- **Phases, gates, spikes, infra, open decisions**: `STATUS.md` (concise). **Per-story narrative**: `docs/history/<story-id>.md`, one file per story. **Standing knowledge**: `docs/HANDBOOK.md`. **Session state only**: the workstream's `NEXT_SESSION.md`, indexed from the root `NEXT_SESSION.md`.
- Epic files are frozen spec + dated amendments; their AC checkboxes are the definition of done, not a maintained status.

**The session protocol:**
- **Session start:** read the root `NEXT_SESSION.md` index, then your workstream's pointer; verify its claims against the code/DB before trusting them.
- **When a story lands, in the same commit:** check its `STATUS.md` line (date + gist) · write `docs/history/<story-id>.md`, including rejected options and what was NOT exercised · update the affected requirement rows · amend any diverged design doc in both directions.
- **Session end:** rewrite your workstream's `NEXT_SESSION.md` — every section, against the new state — and update your line in the root index. New traps/conventions go to `docs/HANDBOOK.md`, never into the pointer.
- **Then `/clear`.** Every session starts from a cleared context. The pointer is only load-bearing if it is actually what the next session reads; a context carried across the boundary hides a stale pointer until someone else opens it cold.
- **Conflicts between two decided documents** get a memo with a recommendation; the pick stays human.

**Working in parallel:** one `NEXT_SESSION.md` per workstream, one `docs/history/` file per story. Both are committed — never gitignored. The decision queue in a pointer is a queue a *human* reads, and an untracked pointer makes a handoff impossible. Separation comes from one file per stream, not from hiding files from git.
```
