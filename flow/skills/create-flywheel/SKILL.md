---
name: create-flywheel
description: Scaffold the session-flywheel dev process for a project — tracked requirement tables, STATUS.md, per-story docs/history/, per-workstream NEXT_SESSION pointers, HANDBOOK, and the session protocol written into AGENTS.md. Works on fresh /create-blueprint output (greenfield mode) or an existing codebase (adopt mode, with an optional two-direction spec audit). Multi-developer safe.
argument-hint: [path/to/project-or-repo]
allowed-tools: Read Grep Glob Write Edit Bash
---

# Session Flywheel

You are scaffolding a documentation-driven dev process whose core loop is: **every session cold-starts from a `NEXT_SESSION.md` pointer, and every session's last act is rewriting it** — so context compounds across sessions instead of evaporating. The system was extracted from the viddry project (2026-07, Jeremy) after its lessons were learned the hard way; you are installing the *end state*, including the fixes.

## The architecture — one fact, one home

| File | Holds | Changes when |
|---|---|---|
| `requirements/functional.md`, `non-functional.md` | The spec, **with a per-row Status column** — the authoritative per-requirement view | A story lands or a requirement-level decision is made (same commit) |
| `STATUS.md` | Phases, gates, spikes, infrastructure, open decisions, story one-liners | Work lands |
| `docs/history/<story-id>.md` | **One file per story.** The narrative: decisions and why, rejected options, traps, test numbers, what was NOT exercised | A story lands |
| `<workstream>/NEXT_SESSION.md` | **Session state only**: goal, human's decision queue, live workspace/infra state, baseline | Rewritten at every session end |
| Root `NEXT_SESSION.md` | **Index of active workstreams** — one line each: owner, goal, state | A workstream's state changes |
| `docs/HANDBOOK.md` | **Standing knowledge**: framework traps, test rules, how to run things, conventions | Only when something is learned |
| Epic files (`plan/epics/`) | Frozen spec + dated amendment annotations; AC checkboxes are the *definition of done*, never a maintained status | A spec-level divergence is discovered |

The failure modes this prevents (all observed): status duplicated into epic checkboxes drifts silently; requirement docs never amended while design docs move (a loosened gate still stated as hard); NEXT_SESSION ballooning into a handbook that gets error-prone full rewrites every session; narratives bloating the tracker until it stops being scannable.

## Why the history is one file per story

The narrative is not recoverable from `git log`, and that is the point. Git records what happened; these files record what *didn't* — the options considered and **rejected** (which by definition leave no commit and no diff), the paths **not exercised**, and the test numbers as of that moment. Squash-merge workflows flatten per-commit granularity anyway, and an agent scanning six months of log burns context that a targeted `STATUS.md` line → `docs/history/<story-id>.md` read does not.

One file per story rather than a single append-only log is a **concurrency** decision: two developers landing two stories touch two different files, so the log never conflicts. Phase-gate narratives go to `docs/history/phase-<n>-gate.md` under the same rule.

## Process

### 1. Locate and classify

If arguments were provided, treat `$ARGUMENTS` as the project directory. Otherwise look for `requirements/` + `plan/` (create-spec / create-blueprint output) at the repo root or under `docs/` or `projects/<name>/`; if ambiguous, ask.

Determine the mode and confirm both mode and target layout with the user before writing anything:

- **Greenfield** — fresh `/create-spec` + `/create-blueprint` output, no meaningful code yet. All statuses start ⬜; `NEXT_SESSION.md`'s goal is the first phase's first stories/spikes.
- **Adopt** — an existing codebase. Offer the **two-direction audit** first (recommended): (a) spec→code — every FR/NFR traced to shipped code, a tracked-open item, or a written deferral; anything with no disposition is flagged to the human; (b) code→spec — every built surface traced back to a requirement/story; flag inventions and violations of explicit out-of-scope lists. Populate the Status columns from audit *findings*, never from optimism. Dispositions for the flagged items are the human's call.

### 2. Scaffold (templates in `templates.md`)

1. **Add the Status column** to `requirements/functional.md` and `non-functional.md`, with the tracked-table preamble. `business.md` gets a note that BRs are goals tracked through the rows citing them.
2. **Generate `STATUS.md`** mechanically from `plan/phases.md` + `plan/epics/*` + `plan/spikes.md` + `plan/infrastructure.md`: one section per phase with its gate, story checkbox one-liners, spikes table, infra items, open decisions from `open-questions.md`.
3. **Create `docs/history/`** with its `README.md` convention stub, **`docs/HANDBOOK.md`** (seeded with the universal conventions from `templates.md`; project-specific traps accumulate later).
4. **Set up the pointers** — per the "Set up the pointers" section below. At minimum: a root `NEXT_SESSION.md` index and one workstream pointer.
5. **Annotate epic files** with the definition-of-done header note.
6. **Write the session protocol into `AGENTS.md`** (or `CLAUDE.md` if that's the repo's convention) — this is the load-bearing step; without it the files are furniture. Append, never overwrite; if a "where things live" section already exists, reconcile.

### 3. Set up the pointers (ask before assuming)

Ask the user **how many parallel workstreams this project will have, and who owns them.** The answer decides the layout:

- **One developer, one stream** — a single `NEXT_SESSION.md` at the project root, plus a root index with one line. The index costs nothing now and means adding a second stream later is not a migration.
- **Two or more streams** — one directory per workstream (`workstreams/<slug>/NEXT_SESSION.md`, or the project's existing per-project directories), plus the root `NEXT_SESSION.md` index. **This is what keeps parallel developers out of each other's way**: the pointer is rewritten wholesale at every close, which is the worst possible input to a git merge, so two developers must never share one.

Both pointers are **committed, not ignored**. Ignoring them would take the decision queue ("things that need a human") and the handoff context out of the team's hands — which is precisely the value the pointer exists to create. Separation comes from *one pointer per stream*, not from hiding them from git.

The root index is line-oriented and merges cleanly. Each line is owned by its workstream; a session corrects another workstream's line only with direct evidence, and says that it did.

### 4. Hand off

Summarize what was created and state the first pointer's goal. Remind the user: the flywheel only works if session-end rewrites are non-negotiable — a half-updated pointer misdirects the next session worse than no pointer.

## The session protocol (written into AGENTS.md verbatim from `templates.md`)

- **Session start:** read the root `NEXT_SESSION.md` index, then your workstream's pointer. Verify its claims against the code/DB before trusting them — it records what was true at last close, and humans work between sessions. **A third-party fact you cannot read is not one you cannot measure**: before writing "only a human can settle this", name the cheapest reversible action that would make the system answer, and **prefer the running application's own output over the platform's metadata.**
- **Choosing the work:** one specific, unblocked, buildable unit with no divergence touching it → say what you are starting and start; a single candidate is not a choice. Two live candidates, an open decision or spike, or scope that would have to be invented → present and stop.
- **When a story lands, in the same commit:** check its `STATUS.md` line (date + one-line gist) · write `docs/history/<story-id>.md` (including rejected options and what was NOT exercised) · update the affected FR/NFR Status rows · amend any diverged design doc in both directions · **state the unit's deploy impact at the top of the pull request body**, read off the plan's own deployment decisions.
- **Requirement-level decisions** (loosening a gate, deferring a clause) are dated amendments **on the requirement row itself** — a design doc may carry the reasoning but must never contradict the row.
- **The close is not a command you wait for** — it fires at a landing point: the unit's ACs are met and the work is about to be committed, a blocker halts it with nothing else startable, or context is materially consumed. One unit of work per session; the session does not reopen in the same context.
- **A stop is not a close.** A stop ends the turn and waits for the answer in the same context — no pointer rewrite, no close checklist, no `/clear` in a message that asks a question.
- **A dormant workstream gets a verify-and-report, not a session.** Verify the pointer, report in a few lines, present what its decision queue still holds as a choice, and end; rewrite the pointer only on a divergence, and do not run the close.
- **Session end:** rewrite your workstream's `NEXT_SESSION.md` — every section, against the new state. Update your line in the root index. New traps/conventions learned go to `docs/HANDBOOK.md`, not into the pointer.
- **No self-invalidating facts** in the pointer or `STATUS.md`. The test: **would the next ordinary action make this wrong?** Commit counts and HEAD SHAs fail it. **Record what is in flight, never a history of what was** — the branch and pull request a reader could still commit to, and the last landed story ID. **A spent branch name goes stale at the next merge**, and a reader cannot tell a stale one from a live one.
- **Then `/clear`.** Every session starts cold. A context carried across the boundary means the next turn runs on memory rather than the pointer — which hides a stale pointer until someone opens it cold weeks later.
- **Every stop is a choice, not a question:** name the two or three real options, what each costs, and the recommendation with its reasoning where there are grounds — in the session and in the pointer's decision queue alike. Say plainly when there are no grounds. **The choice leads the message**, above the verification report.
- **A blocker that has stopped the next unit for three closes is itself a finding.** When you are about to copy one forward unchanged again, **re-examine the frame, not the options**: is it unmeasurable or only unread, and **are all the options "the human does X"?** An option set with no cheap session action in it is incomplete. **Say the count out loud** when you present it.
- **Never merge a branch that touches migrations**, and never treat "just push it and see" as free until you have said what the push and the merge do outside the repository.
- **Conflicts between two decided documents** get a memo with the options and a recommendation; the pick stays human.

## Behavior rules

- **Never overwrite existing files without asking** — especially requirements/plan docs (they are someone's decisions) and any existing STATUS/NEXT_SESSION (offer merge).
- **Never fabricate a status.** Greenfield rows are ⬜; adopt-mode rows come from the audit or stay flagged. A wrong ✅ is worse than a stale ⬜.
- **Write every scaffolded file in Simplified Technical English** — the rules are in `../../reference/writing-style.md`, and they are installed into the project's `HANDBOOK.md` and `AGENTS.md` so later sessions keep to them. Existing requirement and plan text is **not** restyled: its wording is someone's decision.
- **Respect the repo's layout** — `docs/` at root, `projects/<name>/`, whatever exists. The system is the seven roles, not the seven paths.
- **Never write a self-invalidating fact into a scaffolded file** — no commit counts, no HEAD SHAs in `STATUS.md` or any pointer, and no list of spent branch names. The test is whether the next ordinary action makes it wrong.
- **Never gitignore a pointer or a history file.** If parallel developers are colliding, the fix is one pointer per workstream, not an untracked one.
- **Do not install a task tracker, board, or tooling** — this is files-in-git by design; status updates ride the same commits as the work they describe.
