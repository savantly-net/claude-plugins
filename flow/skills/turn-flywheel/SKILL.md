---
name: turn-flywheel
description: Turn the session flywheel — open a session (orient from the workstream's NEXT_SESSION.md, verify its claims against the code, then start the next unit of work when exactly one is live, or propose when there is a real choice) or close one (the landing ritual: STATUS line, per-story history entry, requirement rows, design amendments, then rewrite the pointer). Use at the start of a session on a flywheel project; the close fires by itself when the work lands. Companion to /create-flywheel, which installs the process. LOOP MODE, the `loop` argument, makes the session declare the outcome of its own turn in a machine-readable block, so a runner can drive sessions unattended.
argument-hint: [workstream-slug] | close | ask | loop
---

# Turn the Flywheel

`/create-flywheel` **installs** the process. This skill **runs** it.

The core loop: every session cold-starts from a workstream's `NEXT_SESSION.md`, and
every session's last act is rewriting it. Between those two points is ordinary
engineering, which this skill does not try to systematize. What it systematizes is
the opening and the close — because those are what erode, and their failure is silent.

**This skill's text governs at runtime.** A project scaffolded by an earlier
`/create-flywheel` may carry an older protocol in its `AGENTS.md` — one that says the
session always stops for the pick, waits to be told to close, or hands every phase
gate to the developer. Where the two differ, this skill supersedes it; that is a version skew, not a conflict between two decided
documents, so it needs no memo. Refresh the project's `AGENTS.md` section at the next
close if it is materially stale.

**Know which version is governing before you rely on a rule that changed.** A project
document may name a version this skill has not reached yet, and `AGENTS.md` cannot tell
you which one is loaded. **Read the plugin's own manifest** — the `version` field of the
`plugin.json` beside this skill, or the version directory in the plugin cache path. **If a
project instruction is conditioned on a version, check it rather than assuming**: the
skill that is running is the one that governs, not the one the project expects.

## Mode detection

Match **whole words only** — the argument may also be a workstream slug, and a
substring match sends `backend` to CLOSE mode and `tasks` to propose-only.

- The argument is, or contains as a standalone word, `close`, `end`, or `wrap` →
  **CLOSE mode**.
- It is, or contains as a standalone word, `ask` → **OPEN mode, propose-only**: run
  the open exactly as below but never auto-start, even when the work is unambiguous.
  This composes with a slug: `<slug> ask` scopes *and* forces the stop.
- Otherwise → **OPEN mode**, optionally scoped to a named workstream slug.

**LOOP MODE is a separate switch, and it is not a mode of its own.** The argument is,
or contains as a standalone word, `loop` → **loop mode is on**. The session then runs
OPEN mode or CLOSE mode exactly as above, and it also obeys **LOOP MODE** at the end of
this file. `loop` composes with a slug, which scopes the run. Do not pair `loop` with
`ask`: in loop mode a stop is an outcome block, and the block is what a machine reads.

If a workstream is genuinely named `close`, `ask`, or `loop`, say what you read it as
and confirm before acting.

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

### A dormant workstream

**When the root index lists your workstream under Dormant, the open is a verify-and-report,
not a session.** Run section 2 against the pointer, report the result in a few lines, and
present anything the pointer's decision queue still holds as a choice. **Do not run CLOSE
mode** — there is no unit, so there is no landing point, and a nine-step checklist of N/A
is noise. Rewrite the pointer only when verification found a divergence; that rewrite is a
commit, and it is the one thing a dormant open can land. If the human brings work, it is
scope the pointer does not name — present it and stop, per **Presenting a decision**.

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
| env vars, dashboards, third-party state | **not readable directly — that is not the same as unmeasurable.** See below before calling it a human's job |

Report every divergence found, plainly. A divergence is itself a finding: the
pointer gets corrected at close **even if nothing else happens this session**.

### "I cannot read it" is not "I cannot measure it"

The last row is where sessions give up too early, and the cost compounds: a
blocker written into the queue as *"no session can settle this"* gets copied
forward at every close, unexamined, for as long as the project lasts.

**Before you surface a third-party unknown as needing a human, name the cheapest
reversible action that would make the system answer it.** Usually one exists:

- **Push a branch and read what answers.** CI, preview bots and deployment
  integrations comment the platform, the project, the environments and the URLs —
  facts no amount of reading the repository will produce.
- **Ask the provider's own CLI.** A `list` command against the account you already
  hold credentials for is read-only and usually settles the question outright.
- **Fetch the public surface.** An unauthenticated `curl` of a deployed page, feed
  or health endpoint measures what is actually live.
- **Read the platform's records through its API** — deployment history, statuses,
  environment names — rather than its dashboard.

**Prefer the artifact's own output over the platform's metadata whenever the two
could disagree.** A deployment record names the addresses the platform assigned; the
running application names the address it actually believes it has, and when a custom
domain exists only the second one is right.

### The guard that makes the above safe

**Before any action that pushes, merges or deploys, say what it does to the world
outside the repository.** Read it off the plan's own deployment and infrastructure
decisions — **that is usually where the coupling is already written down, unread.**

- **Does the build run migrations?** Then a push to a watched branch is a schema
  change, and a merge to the default branch is a production schema change.
- **Does it change a public surface, or need a variable that is not set?**
- **Does it cost money, send mail, or call a third party for real?**

**A session never merges a branch that touches migrations**, and never treats
"just push it and see" as free until it has answered these. The two halves of this
section are one rule: **measure instead of assuming, and know what the measurement
costs before you take it.**

## 3. Choose the work

Start from the goal the pointer states, then cross-check it against `STATUS.md`
before accepting it:

- Is the phase gate blocked?
- Is a spike this story depends on still unanswered?
- Is an open decision marked due this phase, and still open?
- **Has every story it depends on landed?** `STATUS.md` will not tell you — it
  flattens each phase into a list and keeps no story-to-story edges. Read the
  candidate's `**Dependencies:**` field in its epic file and `plan/dependencies.md`,
  then confirm each named predecessor is ✅ in `STATUS.md`. A predecessor sitting at
  🟨 counts as not landed.

**Never start a story whose blocking spike or decision is open.** Surface it and
propose the alternative — `plan/dependencies.md` names what can proceed in
parallel, and there is almost always something. A story built on an unanswered
spike is rework with extra steps.

Then read, for the candidate only: its epic entry (the AC checkboxes are the
definition of done), its design doc, and its `docs/history/<story-id>.md` entry if
one exists. If the story touches a surface another story already built, read that
story's history entry too — its **rejected options** section is where you find out
why the obvious approach was already refused.

### The one-candidate test

Auto-start is a fast path **through** these checks, never around them. Apply all
five; every one must hold:

1. The pointer's goal names a **specific** story or spike — not a menu, not "continue
   the epic".
2. Verification found **no divergence that touches it**.
3. It is **unblocked** on all four axes: no open spike it depends on, no open decision
   it needs, no unlanded predecessor story, and no phase gate standing in front of it.
   The predecessor check is read from the epic's `**Dependencies:**` field and
   `plan/dependencies.md` — never inferred from a story's position in `STATUS.md`'s
   per-phase list.
4. Nothing in the pointer's "things that need a human" queue is a prerequisite for it,
   and — in adopt mode — no audit item awaiting disposition covers the same surface.
5. Its ACs and design doc exist and are **specific enough to build against**. If
   starting means inventing scope, this fails.

**All five hold → start.** Present the full picture anyway — the story, its acceptance
criteria, what it depends on, what it will touch, what verification found, and the one
line on why it was the only candidate — then say explicitly that you are **starting
now**, and start. The announcement is the human's veto: it must be legible enough to
interrupt, so never bury the pick in a paragraph and never begin editing before it has
been said.

**Any of the five fails → stop, with a choice.** Present at most three candidates and
name precisely which test failed and for which one, per **Presenting a decision** below.

### Always stop, regardless

- Two or more unblocked candidates that the pointer does not order between.
- A verification divergence that touches the candidate, or that changes what should
  be worked on at all.
- The work needs an answer from the decision queue, or from `STATUS.md`'s open
  decisions.
- Adopt-mode audit items awaiting disposition on the same surface.
- The "unit" is a release, or anything outward-facing and hard to reverse. **A phase
  gate is a stop only when a clause is unchecked, or when closing it is itself
  outward-facing** — see **Closing a phase gate** below.
- The `ask` argument was passed.

**A stop is not a close.** A stop ends the *turn*; the session stays open in this context
and waits for the answer. Write no pointer, run no close checklist, and say nothing about
`/clear` — the answer has to land somewhere, and a cleared context is not it. The close
comes later, in the same context, once the answer has either produced work or gone into the
pointer's decision queue for a later human. The one exception is a stop whose answer nobody
can give now: then the question *is* the session's output, it goes into the queue, and the
close runs on it. Say which of the two you are doing.

### Closing a phase gate

**The gate line is the conjunction of its clauses, not a separate judgment.** When
every clause is `[x]`, a session marks the line ✅ — that is a reading of the boxes,
not an inference about them. Announce it the way you announce a pick: say the phase,
say that every clause is checked, say you are closing it. **The announcement is the
developer's veto, so make it legible enough to interrupt.**

**Judgment lives on the clauses, and that is where a human's attention belongs.**
A clause a session cannot verify objectively — one phrased as something a *person*
does, or one that weighs how good a piece of evidence is — **is checked by the
developer, on the clause.** Take it to them as a choice, per **Presenting a
decision**, and never check it yourself.

**Still a stop, and these do not change:**

- **Any unchecked clause.** One ⬜ and the gate is not closable by anyone yet.
- **A clause you believe is wrongly checked.** Say so and stop; a wrong ✅ underneath
  is worse than an open gate.
- **A gate whose close is itself outward-facing** — it cuts a release, it deploys, it
  tells somebody outside the team that a thing is done.

**Why this is safe:** the rule against checking a clause by inference is unchanged,
and it is what the line rests on. A gate is only as honest as its boxes. **If closing
a gate feels like a judgment rather than a reading, that is a signal a clause was
checked too generously — go back to the clause, not to the line.**

### Presenting a decision

This applies to every stop in this skill, not only the pick: a divergence that needs a
disposition, a conflict between decided documents, an item going into the pointer's
decision queue.

A decision handed back as an open question is work handed back. Present it as a
**choice**:

- **The real options** — two or three, each named. Not "what do you want to do?"
- **What each costs and what each unblocks**, in one line apiece.
- **A recommendation, when you have grounds for one** — say which, and why. The
  reasoning is the point: it is what lets the human refuse it on its merits.
- **Say plainly when you have no grounds.** A recommendation invented to look
  decisive is worse than none, and this system runs on a stated preference meaning
  something.

**The choice leads the message.** Put it at the top, before the verification table and
the orientation report; the human reads for what they have to decide, and a
recommendation under a table of green checks is a recommendation they scroll past. The
evidence follows the choice, not the other way round.

Where a structured choice prompt is available, use it, with the recommended option
first. A recommendation is never a default: do not begin the recommended option while
waiting for the answer, and do not re-ask a decision the human has already made.

**A blocker that has stopped the next unit for three closes is itself a finding.**
Not every long-lived entry qualifies — a judgment the human is right to sit on can
wait indefinitely, and re-raising it is noise. The rule fires on the entry that
**blocks the work**: when you are about to copy one forward unchanged again,
**re-examine the frame rather than the options.**

- **Is it actually unmeasurable, or only unread?** Run the previous section. A
  question that eight closes called impossible is usually one nobody tried to
  measure.
- **Are all the options "the human does X"?** Then the option set is the problem,
  not the human's silence. **An option set with no cheap session action in it is
  incomplete** — find one, or say plainly that you looked and there is none.
- **Say the count out loud** when you present it: *"this has blocked the next unit
  for four closes"* is information the human does not otherwise have, and it is
  what tells them the framing has gone wrong.

**In LOOP MODE this stop also writes a `decision` block**, and the options of the block
are the options above. See **LOOP MODE** at the end of this file.

## 4. Work

Ordinary engineering, with four standing rules:

- **Code and doc diverge → follow the code, amend the doc in the same commit** —
  and say which one was wrong. Sometimes the doc is right and the code is the bug.
- **Two decided documents conflict → do not resolve it.** Write a memo under
  `memos/` naming the options and a recommendation, per **Presenting a decision**.
  The pick stays human.
- **Note traps as you hit them.** They go to `docs/HANDBOOK.md` at close, not into
  the pointer.
- **Never widen a result type, gate, or guarantee silently.** If pressure to do so
  appears, it belongs in the story's `docs/history/` entry as a rejected option.
- **Know what landing the work does before you land it.** Re-read the guard in
  section 2. **A session never merges a branch that touches migrations**, and it
  says the deploy impact out loud — in the pull request body, and to the human —
  before asking for a merge.

### When to close — the close is not a command you wait for

It is what the session does at a landing point. Enter CLOSE mode on your own when any
of these is true:

- **The unit chosen at open has landed** — its ACs are met and the work is ready to
  commit. That commit is the one that carries Part 1, so run Part 1 *before* committing,
  not after.
- **A blocker halts the named work** and nothing else is sensibly startable without a
  human. This is the most valuable close there is; do not let it look like a wasted
  session.
- **Context is materially consumed**, or a compaction has already happened. Landing
  early with an honest pointer beats being cut off mid-story with none.

**Not a landing point:** a stop that is waiting for the human's answer (see **A stop is
not a close**), and a dormant-workstream open whose verification came back clean (see
**A dormant workstream**). The first stays open; the second reports and ends.

**One unit of work per session.** After the close, the session is over — say so and
stop. Do not open again in the same context, however much room is left: an auto-reopen
would carry the warm context across the boundary and leave the pointer unexercised,
which is the whole thing the `/clear` exists to prevent.

---

# CLOSE mode

Two parts. Part 1 runs once per story that landed. Part 2 runs once per session,
**including a session where nothing landed but something was learned** — a blocker, a
divergence, an answer that went into the queue — because that session has produced the
most valuable pointer update available. **It does not run for a session that learned
nothing**: a dormant-workstream open with every verification step green, or a stop that
is still waiting for its answer. A close that rewrites the pointer to say what it already
said is not a close; it is a checklist with nothing under it.

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
6. **Say what landing this does.** Name the deploy impact of the unit — migrations,
   public surfaces, environment variables, anything outward-facing — and put it
   **at the top of the pull request body** where the person merging will read it.
   **Read it off the plan's own deployment and infrastructure decisions**; the
   coupling is usually already written down and simply never surfaces at the moment
   it matters. **"Nothing — no code and no migration" is the answer more often than
   not, and saying it is the point**: a reader who is told every time learns to
   trust the one time it says otherwise.

## Part 2 — session end, once

6. **Rewrite your workstream's `NEXT_SESSION.md`.** Every section, against the new
   state. **Not a patch** — a patched pointer keeps the sentences nobody re-read,
   which is how they go stale. Sections: the goal for next session · the human's
   decision queue · operational state (branch, PR, what fires, what is live, what
   is only local) · the baseline · where things live. **Rewrite only your own
   pointer**; another workstream's is not yours to rewrite, and a wholesale rewrite
   of a shared pointer is how a merge conflict becomes lost session state.

   **Every entry in the decision queue is written as a choice**, per **Presenting a
   decision**: the options, what each costs, and the recommendation with its reasoning.
   A queue entry phrased as a bare question makes the human do the analysis this
   session already did and then threw away.

   **Record no fact that writing it down invalidates.** A commit count and a HEAD
   SHA are the two that bite: the pointer is committed *after* it is written, so the
   count is already wrong by one before anyone reads it, and the SHA names the commit
   before the close. Never write either into the pointer or `STATUS.md`. Record what
   stays true instead — **branch name, PR number and its state, whether the branch is
   merged, whether the work is pushed, and the last landed story ID.** Test, lint, and
   mutation numbers stay: running them doesn't change them, and they are dated
   measurements, not a position in history.
7. **New traps and conventions → `docs/HANDBOOK.md`**, never into the pointer. If
   you find yourself writing standing knowledge into `NEXT_SESSION.md`, it is in
   the wrong file.
8. **Root `NEXT_SESSION.md` index** — update this workstream's line if its state
   changed. Other workstreams' lines are theirs; correct one only if you have
   direct evidence it is wrong, and say that you did.
9. **Re-read the phase gate, clause by clause.** Check only clauses that are
   objectively true now. A clause phrased as something a *person* does is not
   closed by a passing test suite — **take that one to the developer, on the
   clause.** **Then, if every clause is `[x]`, close the gate line in the same
   commit and say so**, per **Closing a phase gate**.

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

**Never write the `/clear` sentence in a message that also asks the human a question.**
The two cancel: a message cannot wait for an answer and tell the reader to discard the
place the answer goes. If there is a question, it is a stop, and a stop is not a close.

If the human keeps working in the same context after a close, say once that the
pointer has not been exercised and the next real cold start is still unverified.
Do not repeat it.

**In LOOP MODE the close ends with an outcome block, and the block is the last thing in
the message.** Write the `/clear` sentence above it. A close that landed work writes a
`continue` block. A close that landed nothing writes the block that states why. See
**LOOP MODE** below.

---

# LOOP MODE — the session declares its own outcome

**This section applies only when the argument carries the word `loop`.** Without that
word the skill behaves exactly as it does above, and loop mode changes nothing. **The
gate is the point: ordinary interactive use must not change.**

**A human does not normally type `loop`.** A runner passes it — an agent that drives
flywheel sessions unattended, with nobody at the keyboard. That runner reads the session
and it cannot read prose. **The session therefore states the outcome of its turn, and the
runner reads the statement.**

## The rule

**In loop mode the session never gives control back without an outcome block.** The
block is the last thing in the message.

To give control back is to declare an outcome. A message that asks the developer a
question is a `decision`. A message that reports landed work is a `continue`. A message
that reports no remaining work is a `done`. **There is no fourth outcome, and there is no
outcome for work in progress.** A session that still works does not give control back, so
it writes no block.

Write no block while a tool call runs. Write no block in a message that continues the
work. Write the block in the message that ends the turn.

## The block

The block is a fenced code block. The fence carries the info string
`untethered-outcome`. The payload is one JSON object.

**The block is the last fence in the message.** A reader takes the last matching fence
and ignores every earlier fence. Write no text after the block.

**The three forms below are normative for the shape, at version 1.** A runner that
consumes these blocks may publish a contract document of its own; where it does, that
document governs for that runner, and it must not contradict the forms below.

### continue — the turn landed a unit of work

```untethered-outcome
{
  "version": 1,
  "outcome": "continue",
  "story": "E02.1",
  "landed": true,
  "pointer": { "path": "NEXT_SESSION.md", "story": "E08.1", "rewritten": true }
}
```

`story` names the unit that this turn landed. `pointer.story` names the unit for the next
turn. The two fields differ. `pointer.path` is relative to the root of the repository.
`pointer.rewritten` is true when the close rewrote the pointer whole.

**Write `pointer.blockedBy` when the next unit cannot start.** The field is optional, and
a next unit that is ready to build carries no field. **The field states what blocks the
unit, in one sentence.** The machine stops the run on it, because a blocked next unit is
no unambiguous next unit of work.

```untethered-outcome
{
  "version": 1,
  "outcome": "continue",
  "story": "F01.3",
  "landed": true,
  "pointer": {
    "path": "NEXT_SESSION.md",
    "story": "F01.4",
    "rewritten": true,
    "blockedBy": "F01.4 waits on an open decision that the developer owns"
  }
}
```

⚠️ **Do not leave the field out because the pointer says the same thing in prose.** The
machine reads the block and it does not read the pointer. **A block that names a blocked
unit with no `blockedBy` starts that unit**, and a turn then builds work that a human
already stopped.

⚠️ **A blocked next unit is still a `continue` when this turn landed a unit of work.** The
outcome describes what this turn did. `blockedBy` describes what the next turn cannot do.
**Write `done` only when this turn landed nothing and nothing remains.**

### decision — the turn reached a fork that the developer owns

```untethered-outcome
{
  "version": 1,
  "outcome": "decision",
  "question": "Which unit does the next session build?",
  "options": [
    { "key": "1", "label": "E08.1, the JSONL journal" },
    { "key": "2", "label": "E01.1, the configuration file" }
  ]
}
```

`question` states the fork in one sentence. `options` holds 2 items or more. A fork with
one option is not a decision. Each `key` is a decimal digit. Number the options from `1`
upward, in order. **The developer replies with the key from a phone**, so the label must
identify the option without the message that carried it.

### done — no unit of work remains

```untethered-outcome
{
  "version": 1,
  "outcome": "done",
  "reason": "The phase gate holds a clause that the developer must check."
}
```

`reason` states why no unit of work remains.

## Which outcome fires

| The session | The outcome |
|---|---|
| lands a unit of work, and completes the close | `continue` |
| lands a unit of work, and finds the next unit blocked | `continue`, with `pointer.blockedBy` |
| stops at a choice, per **Presenting a decision** | `decision` |
| finds a blocker, and nothing else is startable | `done` |
| finds an empty backlog | `done` |
| reaches a phase gate holding a clause a session cannot verify | `done` |

**A stop and a fork are the same event.** Every stop that this skill names above is a
`decision` block: two or more unblocked candidates, a divergence that changes the work, an
open decision, a gate that is outward-facing, and a conflict between two decided
documents. The options of the block are the options that **Presenting a decision**
already asks for.

**A blocker is not a fork.** A fork gives the developer a choice between two courses. A
blocker leaves no course. A blocker is `done`, and `reason` names it.

## The block does not replace the prose

The session writes its ordinary report first. A human reads the report, and the report
still names the options, the costs, and the recommendation. **The block repeats the
machine-readable part of that report, and it adds nothing that the prose omits.** A block
that disagrees with the prose above it is a defect.

## What loop mode does not change

Loop mode changes what the session **writes**. It changes nothing that the session
**decides**.

- The one-candidate test is unchanged. Loop mode does not lower the bar for an auto-start.
- **The pick stays human.** A `decision` block reaches the developer by a message. It is
  not a licence to pick.
- A conflict between two decided documents still gets a memo, and the block that reports
  it is a `decision`.
- The close is unchanged. Every step of Part 1 and Part 2 runs before a `continue` block.
- **Never fabricate an outcome.** A `continue` block on a turn that landed nothing is the
  same failure as a wrong ✅, and the machine acts on it.

---

# Standing rules

- **Never fabricate a status.** ⬜ is better than a wrong ✅. A wrong ✅ costs a
  future session the time to discover it, plus the trust in every other row.
- **Write every artifact in Simplified Technical English** — `../../reference/writing-style.md`. It governs the STATUS line, the history entry, the requirement row, the HANDBOOK entry, and the pointer; it does not govern what you say to the human, and it is never a reason to reword a decided document.
- **Never mark a gate CLAUSE closed by inference.** Clauses are written to be
  checked, not reasoned about. **The gate LINE is different — it is the conjunction
  of its clauses**, so a session closes it when every clause is `[x]`. Reading the
  boxes is not inferring from them.
- **The close-out rides with the work, not with "the next commit."** That deferral
  is how the whole system degrades into three files nobody trusts.
- **A stop is a choice, not a question.** Options, costs, and a recommendation with
  its reasoning where you have grounds — in the session, in a memo, and in the
  pointer's decision queue alike.
- **The pick stays human whenever there is a pick to make.** One unambiguous
  candidate is not a choice, and stopping to ask about it just spends a human's
  attention on rubber-stamping. Two candidates, an open decision, or a divergence *is*
  a choice — and those stop. Conflicts between two decided documents, and **any gate
  clause a session cannot verify objectively**, stay human unconditionally. **A gate
  line whose clauses are all checked is not a judgment call and does not stop.**
- **No self-invalidating facts in tracked files.** The test: **would the next
  ordinary action make this wrong?** Commit counts and HEAD SHAs fail it outright.
  **Record what is in flight, never a history of what was** — the branch and pull
  request a reader could still commit to, and the last landed story ID. **A merged
  branch's name, and any instruction conditioned on whether it has merged yet, goes
  stale at the next merge**, and a reader cannot tell a stale name from a live one,
  so they learn to distrust all of them. Send the reader to the live list instead.
- **A pointer is written before its own commit, so say so rather than claiming
  otherwise.** If the close's own branch is in flight while the file says nothing
  is, name that one exception and what it touches. **The next session must be able
  to tell the close's own pull request from somebody else's work.**
- **Do not install tooling, boards, or trackers.** This is files-in-git by design;
  status updates ride the same commits as the work they describe.
- **Stay in your own lane.** One pointer per workstream, one history file per story.
  If you are about to edit a file another workstream owns, that is the signal to
  stop and say so — not to merge on their behalf.
