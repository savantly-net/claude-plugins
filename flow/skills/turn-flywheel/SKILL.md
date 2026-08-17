---
name: turn-flywheel
description: Turn the session flywheel — open a session (orient from the workstream's NEXT_SESSION.md, verify its claims against the code, propose the next unit of work) or close one (the landing ritual: STATUS line, per-story history entry, requirement rows, design amendments, then rewrite the pointer). Use at the start of a session on a flywheel project, and again at the end. Companion to /create-flywheel, which installs the process.
argument-hint: [workstream-slug] | close
---

# Turn the Flywheel

`/create-flywheel` **installs** the process. This skill **runs** it.

The core loop: every session cold-starts from a workstream's `NEXT_SESSION.md`, and
every session's last act is rewriting it. Between those two points is ordinary
engineering, which this skill does not try to systematize. What it systematizes is
the opening and the close — because those are what erode, and their failure is silent.

## Mode detection

- Argument contains `close`, `end`, `wrap` → **CLOSE mode**.
- Otherwise → **OPEN mode**, optionally scoped to a named workstream slug.

If the target project has no `STATUS.md` / `NEXT_SESSION.md` / `docs/history/`,
stop and say so: it has not been scaffolded, and `/create-flywheel` is the skill for that.

---

# OPEN mode

## 1. Orient — read in this order, and stop there

1. **Repo root `NEXT_SESSION.md`** — the index of active workstreams. If more than
   one is live and the argument didn't name one, ask which. **Never adopt another
   workstream's pointer as your own** — read your line, then your own pointer.
2. **Your workstream's `NEXT_SESSION.md`** — the pointer. This is session state only.
3. **`STATUS.md`** — current phase, its gate, the open decisions.
4. **`docs/HANDBOOK.md`** — skim for sections relevant to what the pointer names.

**Do not read every epic and design doc yet.** Read the ones belonging to the
candidate story, once there is a candidate. Burning the context window on
orientation is how a session arrives at the work with nothing left to do it with.

**Read the files even if this context already remembers them.** If the session was
not opened cold, orientation is the step most likely to get skipped on the grounds
that you already know — which is exactly how a wrong pointer survives. Read, verify,
and say plainly that this was not a cold start.

## 2. Verify before trusting — the load-bearing step

The pointer records what was true at last close. **Humans work between sessions**,
and every observed flywheel failure so far has been a pointer that aged badly.
Check, don't assume:

| Claim in the pointer | How to check |
|---|---|
| "on branch X", "PR #N open/merged" | `git log --oneline -15`, `git branch --show-current`, `gh pr list --state all -L 10` |
| "no code exists for this yet" | `grep`/`glob` the paths the plan names |
| "migration N landed" | `ls prisma/migrations`, and confirm the model is actually in `schema.prisma` |
| "dependency X added" | `package.json` |
| "N tests passing" (the baseline) | run the suite if it's cheap; otherwise treat the number as unverified and say so |
| env vars, dashboards, third-party state | **cannot be verified from here** — surface as needing a human, never assume |

Report every divergence found, plainly. A divergence is itself a finding: the
pointer gets corrected at close **even if nothing else happens this session**.

## 3. Choose the work — and stop

Start from the goal the pointer states, then cross-check it against `STATUS.md`
before accepting it:

- Is the phase gate blocked?
- Is a spike this story depends on still unanswered?
- Is an open decision marked due this phase, and still open?

**Never start a story whose blocking spike or decision is open.** Surface it and
propose the alternative — `plan/dependencies.md` names what can proceed in
parallel, and there is almost always something. A story built on an unanswered
spike is rework with extra steps.

Then read, for the candidate only: its epic entry (the AC checkboxes are the
definition of done), its design doc, and its `docs/history/<story-id>.md` entry if
one exists. If the story touches a surface another story already built, read that
story's history entry too — its **rejected options** section is where you find out
why the obvious approach was already refused.

Present: the story, its acceptance criteria, what it depends on, what it will
touch, and anything the pointer got wrong. **Then stop.** The pick stays human —
that is the process, not politeness.

## 4. Work

Ordinary engineering, with four standing rules:

- **Code and doc diverge → follow the code, amend the doc in the same commit** —
  and say which one was wrong. Sometimes the doc is right and the code is the bug.
- **Two decided documents conflict → do not resolve it.** Write a memo under
  `memos/` with a recommendation. The pick stays human.
- **Note traps as you hit them.** They go to `docs/HANDBOOK.md` at close, not into
  the pointer.
- **Never widen a result type, gate, or guarantee silently.** If pressure to do so
  appears, it belongs in the story's `docs/history/` entry as a rejected option.

---

# CLOSE mode

Two parts. Part 1 runs once per story that landed. Part 2 runs once per session,
**including a session where nothing landed** — especially then, because a session
that only discovered a blocker has produced the most valuable pointer update
available.

## Part 1 — per story, in the same commit as the work

1. **`STATUS.md` line** — check the box, add the date and a one-line gist. Blocked
   → 🚫 with the blocker named. Partially landed → 🟨 with the open half named.
2. **`docs/history/<story-id>.md`** — write the narrative in its own file (template
   in `create-flywheel`'s `templates.md`). Mandatory content: what landed · the
   decisions and why · **the options considered and rejected** · traps hit ·
   test/mutation numbers · click-through results · **and what was NOT exercised,
   named plainly.** An unnamed gap is a claim that none exists, and that claim is
   usually false. The rejected options and the unexercised paths are the two things
   `git log` can never recover — a thin entry here is the whole file wasted.
3. **Requirement rows** — update Status on every affected FR/NFR. A
   requirement-level change (a loosened gate, a deferred clause) is a **dated
   amendment on the row itself**. A design doc may carry the reasoning; it must
   never contradict the row.
4. **Design docs** — amend any that diverged, in both directions.
5. **Epic file** — if the *spec* diverged rather than the status, add a dated
   amendment annotation. Never rewrite the original ACs; append.

## Part 2 — session end, once

6. **Rewrite your workstream's `NEXT_SESSION.md`.** Every section, against the new
   state. **Not a patch** — a patched pointer keeps the sentences nobody re-read,
   which is how they go stale. Sections: the goal for next session · the human's
   decision queue · operational state (branch, PR, what fires, what is live, what
   is only local) · the baseline · where things live. **Rewrite only your own
   pointer**; another workstream's is not yours to rewrite, and a wholesale rewrite
   of a shared pointer is how a merge conflict becomes lost session state.
7. **New traps and conventions → `docs/HANDBOOK.md`**, never into the pointer. If
   you find yourself writing standing knowledge into `NEXT_SESSION.md`, it is in
   the wrong file.
8. **Root `NEXT_SESSION.md` index** — update this workstream's line if its state
   changed. Other workstreams' lines are theirs; correct one only if you have
   direct evidence it is wrong, and say that you did.
9. **Re-read the phase gate, clause by clause.** Check only clauses that are
   objectively true now. A clause phrased as something a *person* does is not
   closed by a passing test suite.

Then report the checklist explicitly: which of the nine were done, which were N/A
and why. A silent skip is indistinguishable from a forgotten one.

## Last line of a close: tell the human to `/clear`

End the close by saying the session is landed and the next turn should start from a
cleared context.

This is not housekeeping. The pointer is only load-bearing if it is genuinely what
the next session starts from. A context carried across the boundary lets the next
turn run on remembered detail instead, so a thin or stale pointer still feels fine
— and the rot surfaces weeks later, to whoever opens it cold. Clearing is the only
thing that proves the pointer carries its own weight.

If the human keeps working in the same context after a close, say once that the
pointer has not been exercised and the next real cold start is still unverified.
Do not repeat it.

---

# Standing rules

- **Never fabricate a status.** ⬜ is better than a wrong ✅. A wrong ✅ costs a
  future session the time to discover it, plus the trust in every other row.
- **Never mark a gate closed by inference.** Gates are written to be checked, not
  reasoned about.
- **The close-out rides with the work, not with "the next commit."** That deferral
  is how the whole system degrades into three files nobody trusts.
- **The pick stays human**: story selection, conflicts between decided documents,
  and any gate judgment call.
- **Do not install tooling, boards, or trackers.** This is files-in-git by design;
  status updates ride the same commits as the work they describe.
- **Stay in your own lane.** One pointer per workstream, one history file per story.
  If you are about to edit a file another workstream owns, that is the signal to
  stop and say so — not to merge on their behalf.
