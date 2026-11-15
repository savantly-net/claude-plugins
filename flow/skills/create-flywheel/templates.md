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

> Concise tracker for all planned work. *What* each item is: `plan/` (epics, spikes, phases) and `design/`. **Requirement-level status lives in the tracked tables in `requirements/`** — this file holds what those rows can't: phases, gates, spikes, infrastructure, open decisions, and the story one-liners. *Why it was built this way*: the design doc, with its dated `Rejected:` lines, and the landing commit. **Opening a story means reading its design doc, not just its line here.** Follow shipped code over a stale doc — then amend the doc in the same commit. Standing knowledge: `docs/HANDBOOK.md`.
>
> When a story lands: check the box here with the date and a one-line gist; a rejected option goes to the design doc as a `Rejected (<date>):` line; what was not exercised goes to the pull request body and the commit message; update the requirement rows.
>
> **No self-invalidating facts here or in a pointer** — no commit counts, no HEAD SHAs, and no list of spent branch names. This file is committed *after* it is written, so a count is wrong by one before anyone reads it. Name the branch and pull request that are **in flight**, and the last landed story ID; send the reader to `git branch --merged` for what is spent.
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

## 3. Root `NEXT_SESSION.md` (the workstream index)

```markdown
# Active Workstreams

> **Index only.** One line per active workstream; the detail lives in that workstream's own `NEXT_SESSION.md`. Each line is owned by its workstream — correct another's line only with direct evidence, and say that you did.

| Workstream | Owner | Pointer | State |
|---|---|---|---|
| <slug> | <name> | `workstreams/<slug>/NEXT_SESSION.md` | <one line: what's next, or what's blocking> |

## Dormant

<workstreams that are paused, with the date and why — so a reader knows they are not simply forgotten>
```

## 4. Workstream `NEXT_SESSION.md` initial

```markdown
# Next Session — <workstream>

> **This is a pointer, not a log and not a handbook.** It holds only *session state*: the goal, the human's decision queue, live workspace/infra state, and the baseline. Everything durable lives where it belongs — **standing knowledge: `docs/HANDBOOK.md`** · requirement status: `requirements/` (tracked tables) · phases/gates/stories/infra: `STATUS.md` · why a design is what it is: the design doc's `Rejected:` lines · history: `git log`. Rewrite this file at the end of each session; if you are writing knowledge into it, you are writing it in the wrong file.
>
> **This pointer belongs to one workstream.** It is rewritten wholesale at every close, so a second developer must get their own — never share one.
>
> **No self-invalidating facts.** The test: would the next ordinary action make this wrong? Never record a commit count or a HEAD SHA: this file is committed *after* it is written, so the count is wrong by one before anyone reads it and the SHA names the commit before the close. **Record what is in flight — the branch and pull request a reader could still commit to — and the last landed story ID; never a list of spent branch names.** This file is written before its own commit, so **name the close's own branch as the one expected exception.** Test and lint numbers stay; running them doesn't change them.

## ⚠️ Operational state — read before touching anything

(branch · PR number and state · what is pushed vs. only local · what fires · what is live. Never a commit count or a SHA.)

(none yet)

## ⚠️ Things that need <owner>

(decisions the next session must not make on its own — this is a queue a human reads, which is why this file is committed)

(write each one as a **choice**, not a question: the two or three real options, what each costs and unblocks, and the recommendation with its reasoning where there are grounds for one — say so plainly when there are none. A bare question makes the reader redo the analysis the session already did.)

## Goal

<greenfield: the first phase's first stories/spikes, in dependency order, from STATUS.md>
<adopt: the audit's flagged items awaiting disposition, then the current phase's next work>

## Baseline at close

(test/lint/build state, recorded from exit codes — established in the first working session)
```

## 5. `docs/HANDBOOK.md` seed

```markdown
# <project> — Session Handbook (standing knowledge)

> Everything here is **knowledge, not state** — it changes only when something is learned or a convention is decided, in the commit that learns it. Session state lives in the workstream's `NEXT_SESSION.md`.

## Running the thing

(filled in when the app first runs: dev-server ports and collision warnings, required companion processes, health-check commands, operator scripts)

## Verification principles

- A screen that shows you what it was told cannot verify itself: verify effects in the database/storage, prices against the vendor's own ledger, delivered media by its bytes.
- A vendor's API shape is read from `node_modules` or the vendor's own pages, dated; a vendor's *legal* terms the same way — quoted and dated; third-party claims are named as such.
- Read the DB before trusting a status doc. A route proved by a spike is not a route proved by shipped code.

## Writing rules for these documents

These apply to every tracked file: requirements, ACs, designs, `STATUS.md` lines, and the pointer. They are the enforceable core of **ASD-STE100 Simplified Technical English** — the approved dictionary is not applied, so do not claim conformance.

- One topic per sentence. Instructions 20 words or fewer; descriptive text 25 or fewer. Six sentences per paragraph.
- Active voice, with the actor named. A passive requirement hides who is responsible.
- One word, one meaning — repeat the term, never vary it for style. In a spec a synonym reads as a distinction.
- Noun clusters of three words maximum. Keep articles. Present tense. No `-ing` form where a finite verb works.
- One obligation per requirement row (`must` / `must not` / `should`); one testable condition per AC.
- Specific quantities with units. "About", "quickly", and "as needed" are defects in a requirement.
- **State an artefact, not a property.** "The run is safe" invites a demonstration, and a demonstration invites a person. "A test asserts that no path populates `updatedPermissions`" names a file somebody can open.
- **An acceptance criterion names what verifies it.** A criterion whose verifier is a person is marked when it is written, never discovered at the close.
- **Not governed:** conversation with a human, quoted material (gates, vendor terms, error strings), and existing text — a decided document's wording is someone's decision, and no session restyles it.

## Caps, and how a document is corrected

`docs/limits.json` names a byte cap for each tracked document, and the cap check fails when a file is above it. **To add text to a capped file, remove as much text as you add.** A session that compacts a file lowers its cap in the same commit. A session that raises a cap says so in its report.

- **A decision of record keeps the dated amendment**, one sentence for the decision: requirement rows, ADRs, epic files. The reasoning goes to the pull request body and the commit message that make the amendment, never onto the row; `git blame` on the row finds the commit.
- **A knowledge file and a state file are corrected by replacement**: this handbook, `STATUS.md`, `AGENTS.md`, design docs, the pointer. A design doc's `Rejected:` line stays until the option is taken up; then replace it. Write what is true now; git holds the sentence you replaced.

**Before you write a section here, grep this file for the concept's nouns.** An entry that exists is corrected in place. A trap that a test or a type now pins shrinks to one line naming the test.

## Test rules

- The pure core first, then the IO. A fact the machine needs is a required argument, not a default.
- Mutation-test every load-bearing guard and put the real number in the pull request body.
- **A test the machine runs is the default, and a person is the fallback.** Reach for a unit or integration test first, then a scripted check against the DB or the API, then a person. **Name the fallback as a fallback** — a criterion that needs a person is a criterion that waits for one.
- A human click-through, recorded in the pull request body, verified against the DB. **Use it when nothing automated can reach the effect.** **Say what you could not exercise.**
- A fake external service must be as awkward as the real one.

## Framework & tooling traps — these have all cost real time

(one line each, with the fix; grep before you add, and merge into the entry that exists)

## Shell & environment traps

(one line each; same rule)

## Standing gaps (named, not owed to any current session)

(one line each, when a story ships with a stated gap; delete the line when the gap closes)

## Conventions established (follow them)

- Requirement-level decisions are recorded on the requirement row (tracked tables); design-doc divergence is recorded in both directions, in the same commit; a conflict between two decided documents gets a memo with a recommendation, the pick staying human.
- Dependencies before stubs — and a story can be blocked by an unmade *decision*. A specified obligation with no honest implementation is refused in writing; a gap that survives a story is named at the function that carries it.
- Derive a status projection at read time; store judgements, histories, provider facts, human choices — with an observation time.
- A gate number's denominator is decided in writing before it is measured — and amended in writing too. A measurement that cannot be honest is refused, not approximated.
- Duplicate a decision's reasoning, never just its shape. Re-derive a number before copying it forward.
(project-specific conventions go here, one line each, inside the file's cap)
```

## 6. Epic file header note (insert after each epic's title line)

```markdown
> **Status lives in the tracked requirement tables (`requirements/`) and `STATUS.md`; why a design is what it is: the design doc and its `Rejected:` lines.** AC checkboxes below are the *definition of done*, not a maintained status — an unchecked box on a shipped story is not a claim. Dated "as built" notes and divergence annotations here are authoritative spec amendments.
```

## 7. AGENTS.md section (append; reconcile if one exists)

```markdown
## Dev process — the session flywheel (installed <date>)

**Where things live:**

- **Requirement status**: `requirements/functional.md` and `non-functional.md` are tracked tables. Every row carries a Status, updated in the commit that changes it. A requirement-level decision is a dated amendment on the row, one sentence; the reasoning goes to the pull request body and the commit message.
- **Phases, gates, spikes, infrastructure, open decisions**: `STATUS.md`. **Rejected options**: a dated `Rejected:` line in the design document they constrain. **Standing knowledge**: `docs/HANDBOOK.md`. **Session state only**: the workstream's `NEXT_SESSION.md`, indexed from the root `NEXT_SESSION.md`. **Document caps**: `docs/limits.json`.
- Epic files are frozen spec plus dated amendments. Their acceptance-criteria checkboxes are the definition of done, not a maintained status.

**The session protocol:**

- **Session start:** read the root index, then your workstream's pointer, then verify its claims against the code and the database. A fact you cannot read may still be measurable: name the cheapest reversible action that makes the system answer. Prefer the running application's own output over the platform's metadata.
- **Choosing the work:** one specific, unblocked, buildable unit with no divergence against it → say what you start, and start. Two live candidates, an open decision or spike, a divergence, or scope you would have to invent → present the options and stop. A conflict between decided documents, and a gate clause no session can verify objectively, stay human.
- **When a story lands, in the same commit:** the `STATUS.md` line with its date and gist; a rejected option as a `Rejected (<date>):` line in the design document, or in the epic file when there is no design document; what was not exercised in the pull request body and the commit message; the affected requirement rows; every diverged design document, in both directions; the deploy impact at the top of the pull request body, read off the plan's own deployment decisions. "Nothing — no code and no migration" is usually the answer, and saying it every time is what makes the other answer believed.
- **The close fires at a landing point**, and never on a command: the unit's criteria are met and the work is about to be committed, a blocker halts it with nothing else startable, or the context is materially consumed. One batch for each session — one unit, or several live units whose files do not overlap — and the session does not reopen in the same context.
- **A stop is not a close.** A stop ends the turn and waits in the same context: no pointer rewrite, no checklist, no `/clear` in a message that asks a question.
- **A dormant workstream gets a verify-and-report, not a session.** Verify the pointer, report in a few lines, present what its decision queue still holds, and end. Rewrite the pointer only on a divergence.
- **Session end:** rewrite your workstream's `NEXT_SESSION.md`, every section, against the new state, and update your line in the root index. A new trap or convention goes to `docs/HANDBOOK.md`; grep that file for the concept's nouns first, and correct the entry that exists.
- **Every tracked document has a byte cap in `docs/limits.json`, and a test enforces it.** To add text, remove as much text as you add. A session that compacts a file lowers its cap in the same commit, and a session that raises one says so in its report.
- **A decision of record is amended, and a knowledge file is replaced.** Requirement rows, ADRs and epic files keep the dated amendment, with the reasoning in the pull request body and the commit message. `AGENTS.md`, the handbook, `STATUS.md`, the design documents and the pointer are corrected by replacement: write what is true now, and let git hold the sentence you replaced.
- **No self-invalidating facts in the pointer or `STATUS.md`.** The test: would the next ordinary action make this wrong? A commit count and a HEAD identifier fail it. Record what is in flight — the branch, the pull request and its state, what is pushed, the last landed story — and never a history of what was. A spent branch name goes stale at the next merge, so send the reader to `git branch --merged` and `gh pr list --state open`. The close's own branch is the one expected exception. Test and lint numbers stay.
- **Then `/clear`.** Every session starts cold. A context that crosses the boundary hides a stale pointer until someone opens it cold.
- **Write every tracked file in Simplified Technical English** — the rules are in `docs/HANDBOOK.md`. They do not govern conversation, quoted material, or the wording of a decided document.
- **Every stop is a choice, not a question.** Name the two or three real options, what each costs and unblocks, and the recommendation with its reasoning; say plainly when there are no grounds for one. A recommendation is never a default: do not start it while you wait for the answer. The last thing in the message is the next action — the live choice, or the `/clear` sentence.
- **A blocker that has stopped the next unit for three closes is itself a finding.** Re-examine the frame and not the options: is it unmeasurable or only unread, and is every option "the human does X"? Say the count out loud.
- **Never merge a branch that touches migrations**, and say what a push and a merge do outside the repository before you call either free.
- **Closing a phase gate:** the gate line is the conjunction of its clauses. When every clause is `[x]`, the session marks the line and announces it for the developer's veto. Judgment lives on the clauses, and a clause no session can verify objectively is checked by the developer, on the clause. A stop: any unchecked clause, a clause you believe is wrongly checked, and a gate whose close is itself outward-facing. Never check a clause by inference.
- **A conflict between two decided documents** gets a memo with the options and a recommendation. The pick stays human.
- **Parallel work inside one unit:** when the design doc's Build Parts, or its interfaces, split the unit into parts that own separate files, build the parts at the same time with subagents, each in its own worktree. Write a shared interface first. Read-only research, test runs and review passes always qualify. The main session reads and verifies every part, integrates them, runs the full suite, and alone writes the close and acts outside the repository. Parallelism across units is a batch, inside one workstream.
- **A batch:** when several units are live, each passes the one-candidate test on its own, and their files do not overlap, the session takes them together: one lane for each set of disjoint files, at most three lanes, at most one migration lane, and units that share a file in one lane in dependency order. The order of the pointer, then the unit that unblocks the most, then `STATUS.md` decides which units fill the lanes; a declared dependency is never overridden. Each unit lands in its own commit with its own Part 1, and the session runs one close. A batch never spans two workstreams.

**Working in parallel:** one `NEXT_SESSION.md` for each workstream, committed and never gitignored. The decision queue is a queue a human reads, and an untracked pointer makes a handoff impossible. Separation comes from one file for each stream, not from hiding files from git.
```

## 8. `docs/limits.json` and the cap check

Seed one entry for each tracked document: the pointers, `STATUS.md`, `docs/HANDBOOK.md`, the requirement tables, and `AGENTS.md` or `CLAUDE.md` — whichever holds the protocol in this repo. ⚠️ **The check skips a path it cannot find**, so a typo in a path reads as a pass. **Set each cap to the file's size now, rounded up to the next 1 000 bytes** — headroom that passes a typo fix and refuses a new section.

```json
{
  "rule": "To add text to a capped file, remove as much text as you add. A session that compacts a file lowers its cap in the same commit. A session that raises a cap says so in its report.",
  "measuredIn": "bytes",
  "maxBytes": {
    "AGENTS.md": 0,
    "STATUS.md": 0,
    "NEXT_SESSION.md": 0,
    "docs/HANDBOOK.md": 0,
    "requirements/functional.md": 0,
    "requirements/non-functional.md": 0
  }
}
```

**Wire the check into the project's test command**, so a close that adds cannot pass without an eviction. A project with a test runner writes it as a test in that runner; a project without one gets a script:

```sh
#!/bin/sh
# scripts/check-doc-limits.sh — fails when a tracked document is above its cap.
set -eu
status=0
while read -r file cap; do
  [ -f "$file" ] || continue
  size=$(wc -c < "$file" | tr -d ' ')
  if [ "$size" -gt "$cap" ]; then
    echo "$file holds $size bytes against a cap of $cap. To add text, remove as much as you add." >&2
    status=1
  fi
done <<ENTRIES
$(sed -n 's/^ *"\(.*\)": \([0-9][0-9]*\),*$/\1 \2/p' docs/limits.json)
ENTRIES
exit $status
```

**Measure the check before you trust it**: append 1 000 bytes to one capped file, run the command, read the failure, and revert.
