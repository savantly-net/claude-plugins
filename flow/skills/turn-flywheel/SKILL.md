---
name: turn-flywheel
description: Turn the session flywheel — open a session (orient from the workstream's NEXT_SESSION.md, verify its claims against the code, then start the next unit of work — or a batch of several, when several are live and touch separate files — or propose when there is a real choice) or close one (the landing ritual: STATUS line, requirement rows, design amendments with any rejected option, then rewrite the pointer). Use at the start of a session on a flywheel project; the close fires by itself when the work lands. Companion to /create-flywheel, which installs the process. LOOP MODE, the `loop` argument, makes the session declare the outcome of its own turn in a machine-readable block, so a runner can drive sessions unattended.
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
documents, so it needs no memo. When the project's `AGENTS.md` section is materially
stale, name `/flow:upgrade-flywheel` in the session's report; it replaces the stale scaffold
text in its own commit, apart from the unit's work. **"One unit of work per session" in a project protocol is
that skew too**: since 1.10.0 a session takes a batch (see **A batch**). A project that
wants fewer lanes says so with a number, for example "a batch holds one lane". **A protocol
that ends every close with `/clear`, even when no session can start anything, is skew as
well**: since 1.11.0 such a close parks the workstream (see **A parked workstream**). **A
protocol that hands the person a step the session could take after one yes is skew too**:
since 1.12.0 an action runs by its class (see **Who acts**).

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

If the target project has no `STATUS.md` / `NEXT_SESSION.md`,
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
mode** — there is no unit, so there is no landing point, and a ten-step checklist of N/A
is noise. Rewrite the pointer only when verification found a divergence; that rewrite is a
commit, and it is the one thing a dormant open can land. If the human brings work, it is
scope the pointer does not name — present it and stop, per **Presenting a decision**.

### A parked workstream — it waits on a person

**A workstream is parked when no session can start a unit and every remaining item needs a
person**: a 👤 clause, or a human-only action (see **Who acts**). An action of class `ask`
does not need the person to act: the session asks once and acts, so it never parks a
workstream. Parked is not dormant. A dormant workstream is paused by
choice. A parked workstream waits on one named action, and that action wakes it.

- **The close parks it.** Part 2 writes `Parked: waits on <person> to <action>` into the
  pointer's goal, and step 8 puts the same words on the workstream's line of the root index.
  Name ONE action, the one that unblocks the most, and point to the rest of the queue.
- **Look for prep before you park.** A session can still make the person's part smaller: it
  produces the evidence that a 👤 clause asks for (screenshots, a measured value, a dry run),
  or it rehearses the action and measures what the action needs. **A prep step is a
  startable unit.** When one exists, name it as the next unit and do not park.
- **Prep ends in a request, not a procedure.** When the prepared steps are authorized
  actions, the prep ends in one approval request, per **Ask once, for the whole batch**, and
  the session runs the steps after the yes. Write steps for the person only for the part
  that is human-only. Prep runs once for each action: when the prep is done and only a
  human-only part remains, the next close parks.
- **A parked open whose action is not done is a verify-and-report**, the same as a dormant
  open: verify, report in a few lines, repeat the action, and end. No close.
- **A parked open whose action is done wakes the workstream.** The person says so in the
  argument or in the conversation, or verification measures it. Check the clause that the
  person checked, on the person's word and never by inference. Then run a short close: Part 2
  steps 6, 8, 9 and 10, in one commit. When a unit can start again, the workstream is live.
- **Park only on a human-only action, never on a decision and never on an `ask` action.** A
  choice that the person owns is a stop, per **Presenting a decision**. An `ask` action is a
  stop with one approval request.

**Why:** a close that ends in `/clear` when no session can start anything sends the person
to open a session that can only report that it waits on them. Each 👤 check then costs a
full open and close around it. Parking puts the person's action at the bottom of the
message, where it is the next thing that happens.

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

**The checks do not depend on each other, so run them at the same time** — parallel tool
calls in one message, and a background run or a subagent for the test suite when it is
slow. Wait for every result before you trust the pointer.

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
costs before you take it.** **Then classify the action**, per **Who acts** below: the answers
to these questions are the properties that pick its class.

### Who acts — a decision, an authorized action, or a human-only action

**Every stop is one of three kinds. Classify it before you stop, park, or give a step to a
person.** The kind decides who acts, and a wrong kind is the most expensive error in this
skill: a decision taken by a session overrules the developer, and an action handed to the
developer spends a session on work that a session could do.

| Kind | The test | What the session does |
|---|---|---|
| **Decision** | It sets or changes an architecture choice, a business rule, a contract, a requirement, an acceptance criterion, or the scope of a unit; or two decided documents conflict. | Stop, per **Presenting a decision**. Always human, whatever the mapping says. |
| **Authorized action** | It is mechanical: a decided document, or the developer's answer in this session, already fixes what it does. The project maps its class to `run` or `ask`. | `run`: take it, and record it. `ask`: ask once for the whole batch, then take it. |
| **Human-only action** | A measurement shows that the session cannot reach the system, or the project maps the class to `human`. | Park, per **A parked workstream**. |

#### Action classes

**This skill names the classes. The project maps each class** to `run`, `ask` or `human`, in
the section **Action classes** of its `AGENTS.md`. **A class that the project does not map is
`ask`.** A mapping names classes only. A project may add a note that names its own systems;
this skill names none.

| Class | The action |
|---|---|
| `local` | It changes the working copy only: files, branches, commits. Always `run`; a project cannot map it. |
| `branch-share` | It pushes a branch that only this work uses, or opens a draft change request. |
| `shared-change` | It merges into a branch that other people build on, or it writes where other people work: a comment, a review, an issue. |
| `publish` | It releases an artifact that other people consume: a package, an image, a version tag. |
| `test-environment` | It changes an environment that the project owns and that serves no customer: its configuration, its secrets, a one-off job. |
| `production` | It changes an environment that serves customers, or it changes customer data. |
| `spend` | It costs money, or it calls a third party for real: mail, messages, paid calls. |

**Judge each action by five properties, and take the strictest class that applies:** can a
later step undo it; does it reach people outside the session; does it touch production or
customer data; does it cost money; does it publish something that other people consume.

🔴 **A mapping never turns a decision into an action**, and it never lifts a hard rule of
this skill: a session never merges a branch that touches migrations, whatever the class says.

#### Ask once, for the whole batch

**When the work reaches `ask` actions, collect every one that the unit needs, in order, and
ask for them in one message.** For each step, give its class, what it does outside the
repository (the guard above), and how a later step undoes it. The developer approves all of
them, some of them, or none. A yes covers the listed steps in this session only.

- **Run each step's read-only preview first**, when one exists: a dry run, a plan, a diff.
  When the preview differs from what the message said, stop and show it.
- **Stop at the first step whose result differs from what the message said.** Report what
  ran, what did not, and the state that the stop left.
- **A step that writes a secret makes the value inside the step.** The session never prints
  the value and never reads it back.

#### Precedent counts

**Before you call an action human-only, read the record**: the commit messages, the pointer,
and the bodies of the change requests. When a session already took this class of action with
the developer's approval, the class is at least `ask`, unless the project maps it to `human`.
**A precedent never makes a class `run`.**

**"No access" is a measurement, not an assumption.** Run the cheapest read-only command that
shows whether the session can reach the system, per **"I cannot read it" is not "I cannot
measure it"**.

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
definition of done) and its design doc. **Read the design doc's `Rejected:` lines** —
they are where you find out why the obvious approach was already refused. If the
story touches a surface another story already built, read that surface's design doc
too. A legacy `docs/history/<story-id>.md` from an earlier version is read the same
way, if one exists; never write a new one.

**When that reading is wide, give it to one read-only `Explore` subagent** and take back a
short summary: the ACs, the interfaces, the `Rejected:` lines, and the files the story will
touch. The main context keeps the decision, and not the file dumps.

### The one-candidate test

Auto-start is a fast path **through** these checks, never around them. Apply all
five; every one must hold:

1. The pointer's goal names a **specific** story or spike — not a menu, not "continue
   the epic". A list of named live units is not a menu: it is a batch candidate set (see
   **A batch**). A pointer that asks the human to pick among them is a menu.
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

### A batch — more than one unit, when the plan allows it

**A session lands as many units as can run at once without a choice.** The batch is the set
of units the session takes at open. It is one unit when one unit is live. **It is several
when several are live and they do not touch each other**: a lane left idle is the cost that
one unit for each session pays, and development is fast enough that the cost is real.

**A unit joins the batch when every one of these holds:**

1. **It passes tests 2 to 5 of the one-candidate test on its own**, and the pointer,
   `STATUS.md` or `plan/dependencies.md` names it as live.
2. **Its files do not overlap the files of another lane.** Read them from the design doc's
   **Build Parts** and the epic. A shared file — the database schema, the migrations
   directory, a permission catalog, the lockfile, a shared route — puts the two units in
   **one lane**, which one agent builds in sequence.
3. **It depends on no other unit of the batch, unless both are in one lane** and the lane
   builds them in dependency order. The successor starts after the predecessor's ACs are met
   and its commit exists.
4. **At most one lane carries a migration.**
5. **The batch holds at most three lanes**, unless the project sets a number. The main
   session verifies every lane, and its context is the limit.

**When more units are live than lanes**, take them in this order: the order that the
pointer gives; then the unit that unblocks the most later units; then the order of
`STATUS.md`. This is a reading of the plan, not a pick. 🔴 **Never override a declared
dependency**, and when the pointer leaves the order to the human, stop.

**Announce the batch the way you announce one unit** — each unit, its ACs, its lane and its
files, and the line on why it is live — then say that you are **starting now**, and start.
The announcement is the human's veto.

**Build it with the rules of Parallel work inside one unit**: one subagent for each lane (a
one-lane batch needs none), each one isolated, none writing to git or to a tracked flywheel
file. The main session writes each shared interface first, reads every diff against its
unit's ACs, commits each unit with its own Part 1, runs the full suite once on the integrated
result, and runs one Part 2 close.

**A lane that fails does not hold back the others.** Land the units that met their ACs. The
failed unit keeps its status — ⬜, or 🟨 with the open half named — and the pointer names it.
**Context consumed in the middle of a batch is a landing point**: land what is done, close,
and name the rest as next.

**A batch never spans workstreams.** It has one pointer and one close.

### Always stop, regardless

- Two or more unblocked candidates that cannot all join one batch, and the pointer leaves
  the order between them to the human.
- A verification divergence that touches the candidate, or that changes what should
  be worked on at all.
- The work needs an answer from the decision queue, or from `STATUS.md`'s open
  decisions.
- Adopt-mode audit items awaiting disposition on the same surface.
- The work reaches an action whose class is `ask` or `human` (see **Who acts**). An `ask`
  stop is one approval request for the whole batch, and the session runs the steps after
  the yes. **A phase gate is a stop only when a clause is unchecked, or when closing it is
  itself outward-facing** — see **Closing a phase gate** below.
- The `ask` argument was passed.

**In LOOP MODE, two of these stops come first to a test.** The first bullet is an ordering
choice, and the third holds only when no other unit can start. **Apply A choice the loop
session takes itself** before you stop on either: a choice that passes it is taken and
recorded, and one that fails it is the stop.

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

**The last thing in the message is the next action.** In a terminal the bottom of the
message is what stays on screen, so evidence goes first — the orientation, the verification
table, what was found — and whatever the human does next goes last. When the session is
waiting for an answer, that is the choice: options, recommendation, nothing after them.
When the session has closed and the next action is `/clear`, the `/clear` sentence is last —
even when the close's report presented choices above it, because those went into the
pointer's queue for a later human and are not being asked now. A choice above a `/clear`
sentence is a queued choice; a choice at the very end is a live one. **When the close parks
the workstream, the person's action is last, not `/clear`** — see **Last line of a close**.
(In LOOP MODE the outcome block comes after any of these and repeats it.)

Where a structured choice prompt is available, use it, with the recommended option
first. A recommendation is never a default: do not begin the recommended option while
waiting for the answer, and do not re-ask a decision the human has already made.

**A queue entry that has blocked the next unit for three closes, unchanged, is itself a
finding.** Count one entry, not a sentence. **A pointer sentence such as "no session can
start a unit" is not an entry**: it can repeat at every close while each close clears a
different item, and that is progress. An entry has progress when a clause under it is
checked, its action is partly done, or its options changed. To count, write on the entry
the date it entered the queue (`since 2026-11-06`), and count the closes since that date in
the `git log` of the pointer. A judgment the human is right to sit on does not count, and
neither does an entry the human put last on purpose.

**When the rule fires, do not copy the entry forward a fourth time. Take one exit:**

- **Measure it.** Is it unmeasurable, or only unread? Run the previous section. A question
  that eight closes called impossible is usually one that nobody tried to measure.
- **Prep it.** Take the session step that makes the person's part smaller, per **A parked
  workstream**, and do it now. When the person's part is an authorized action, the exit is
  the approval request, not a written procedure.
- **Reframe it.** Are all the options "the human does X"? Then the option set is the
  problem. Propose an amendment as a choice in the queue: defer the clause, split it, or
  replace the person's check with a test.
- **Park it**, when nothing else can start, per **A parked workstream**. A parked
  workstream has taken its exit, and the rule does not fire on its action again until it
  wakes.

**Say the count out loud** with the exit: *"this entry has blocked the next unit for four
closes"* is information the human does not otherwise have.

**In LOOP MODE this stop also writes a `decision` block**, and the options of the block
are the options above. See **LOOP MODE** at the end of this file.

## 4. Work

Ordinary engineering, with these standing rules:

- **Code and doc diverge → follow the code, amend the doc in the same commit** —
  and say which one was wrong. Sometimes the doc is right and the code is the bug.
- **Two decided documents conflict → do not resolve it.** Write a memo under
  `memos/` naming the options and a recommendation, per **Presenting a decision**.
  The pick stays human.
- **Note traps as you hit them.** They go to `docs/HANDBOOK.md` at close, not into
  the pointer — into the entry that already covers the concept, when there is one.
- **Never widen a result type, gate, or guarantee silently.** If pressure to do so
  appears, refuse it and record the refusal as a `Rejected:` line in the design doc.
- **Know what landing the work does before you land it.** Re-read the guard in
  section 2. **A session never merges a branch that touches migrations**, and it
  says the deploy impact out loud — in the pull request body, and to the human —
  before asking for a merge.
- **Split the unit when its design splits it.** See the next section.

### Parallel work inside one unit

**One unit does not mean one agent.** When the design allows it, build the
parts of the unit at the same time with subagents. Parallelism across units is a **batch**
(see **A batch** above), and it stays inside one workstream. Two workstreams at the same
time is two sessions, each with its own pointer.

**Split a part off when every one of these holds:**

1. **The design names it.** The design doc's **Build Parts** section, the ACs, or the
   interfaces section shows it as a separate piece. Do not invent a split that the design
   does not show.
2. **It touches files that no other part touches.** Two parts that need the same file are
   one part.
3. **It depends on no interface that is not built yet.** Write the shared interface — the
   type, the schema, the function signature — in the main session first, then split.

**These always qualify, with no design to name them:** read-only research, a test run, a
lint or mutation run, and a review pass of the finished diff.

**Isolate every part that writes** in its own git worktree, and give each subagent the
part's ACs, the interface it builds against, and the files it owns.

🔴 **The main session owns the unit, and these never go to a subagent:**

- **The verification of what a subagent returns.** A subagent's "done" is a claim, the
  same as the pointer's. Read the diff, and check it against the ACs.
- **The integration.** Merge the parts, then run the full suite once on the result.
- **The close.** `STATUS.md`, the requirement rows, the design amendments, the handbook,
  and the pointer. No subagent edits a tracked flywheel file.
- **Every action outside the repository.** No subagent pushes, merges, or deploys. The
  guard in section 2 stays with the main session.
- **Every decision.** A subagent that reaches a choice returns it to the main session,
  and the main session applies **Presenting a decision**.

**A part that fails, or that returns work you cannot verify, comes back to the main
session.** Do it in sequence. Never land a part that you did not read.

### When to close — the close is not a command you wait for

It is what the session does at a landing point. Enter CLOSE mode on your own when any
of these is true:

- **The batch chosen at open has landed** — every unit in it met its ACs or stopped. Each
  unit's commit carries its own Part 1, so run Part 1 *before* each commit, not after.
- **A blocker halts the named work** and nothing else is sensibly startable without a
  human. This is the most valuable close there is; do not let it look like a wasted
  session.
- **Context is materially consumed**, or a compaction has already happened. Landing
  early with an honest pointer beats being cut off mid-story with none.
- **A parked workstream wakes**: the person reports its action done. The close is short
  (see **A parked workstream**).

**Not a landing point:** a stop that is waiting for the human's answer (see **A stop is
not a close**), and a dormant or parked open whose verification came back clean and whose
action is not done (see **A dormant workstream** and **A parked workstream**). The first
stays open; the second reports and ends.

**One batch per session.** After the close, the session is over — say so and
stop. Do not open again in the same context, however much room is left: an auto-reopen
would carry the warm context across the boundary and leave the pointer unexercised,
which is the whole thing the `/clear` exists to prevent.

---

# CLOSE mode

Two parts. Part 1 runs once per story that landed. Part 2 runs once per session,
**including a session where nothing landed but something was learned** — a blocker, a
divergence, an answer that went into the queue — because that session has produced the
most valuable pointer update available. **It does not run for a session that learned
nothing**: a dormant or parked open with every verification step green, or a stop that
is still waiting for its answer. A close that rewrites the pointer to say what it already
said is not a close; it is a checklist with nothing under it.

## Part 1 — per story, in the same commit as the work

1. **`STATUS.md` line** — check the box, add the date and a one-line gist. Blocked
   → 🚫 with the blocker named. Partially landed → 🟨 with the open half named.
2. **Record the two facts git cannot recover — and nothing else.** There is no
   per-story history file; the STATUS line, the design doc, the handbook and the
   pull request already hold the rest.
   - **A rejected option** → a `Rejected (<date>): <option> — <why>` line in the
     design doc, under the section it constrains. That is where the next session
     looks before it takes the obvious approach. No design doc → a dated amendment
     on the epic file.
   - **Test and mutation numbers** → the pull request body.
   - **What was NOT exercised** → named plainly in the pull request body, beside the
     deploy impact, and in the commit message. An unnamed gap is a claim that none
     exists, and that claim is usually false. A gap that outlives the story is also a
     🟨 on the STATUS line or a note at the function that carries it.

   No rejected option and no gap → write nothing extra. Do not pad.
3. **Requirement rows** — update Status on every affected FR/NFR. A
   requirement-level change (a loosened gate, a deferred clause) is a **dated
   amendment on the row itself, one sentence for the decision**; the reasoning
   goes to the pull request body and the commit message that make the amendment,
   never onto the row — `git blame` on the row finds the commit, and the pull
   request survives a squash merge. A design doc may carry the
   reasoning; it must never contradict the row.
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
   **Each entry carries the date it entered the queue** (`since <date>`), and a close keeps
   the date while the entry is unchanged. The three-close rule counts from it. A close that
   parks the workstream writes `Parked: waits on <person> to <action>` in the goal section.
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

   **Grep the handbook for the concept's nouns first, and correct the entry that
   exists.** A file nobody can search gets the same trap written into it twice.

   **The handbook is capped in `docs/limits.json`, and the cap check runs in the
   test command. To add, remove as much as you add** — merge two entries, or
   shrink a trap that a test now pins to one line naming the test. Lower the cap
   in the same commit when you compact; say so in the close when you raise one.

   **A knowledge file is corrected by replacement, never by a dated amendment**:
   the handbook, `STATUS.md`, `AGENTS.md`, design docs, the pointer. Write what
   is true now, and let git hold the sentence you replaced. Requirement rows,
   ADRs and epic files are the exception and keep the dated amendment. A design
   doc's `Rejected:` line is replaced when the option is taken up.
8. **Root `NEXT_SESSION.md` index** — update this workstream's line if its state
   changed. A close that parks the workstream writes `Parked: waits on <person> to
   <action>` on the line; a close that wakes it removes the word. Other workstreams' lines are theirs; correct one only if you have
   direct evidence it is wrong, and say that you did.
9. **Re-read the phase gate, clause by clause.** Check only clauses that are
   objectively true now. A clause phrased as something a *person* does is not
   closed by a passing test suite — **take that one to the developer, on the
   clause.** **Then, if every clause is `[x]`, close the gate line in the same
   commit and say so**, per **Closing a phase gate**.

10. **Report where the plan stands.** Count the story lines of `STATUS.md` and write one line:
    **how many stories are done, how many remain, how many of those are blocked, and how many
    open criteria need a person** (the 👤 ones). Put it in the pointer's goal section and say it
    in the close. **Derive it by counting, never by carrying a number forward** — a count
    written into a tracked file goes stale at the next landing, which the no-self-invalidating-facts
    rule already forbids.

    ⚠️ **A story at 🟨 is not done.** Count it as remaining and say which half is open. **The
    number that matters to a developer is not "how much is ticked" but "how much can a session
    still start on its own"** — so report the blocked count and the 👤 count beside the total.

Then report the checklist explicitly: which of the ten were done, which were N/A
and why. A silent skip is indistinguishable from a forgotten one.

## Last line of a close: the next action

**When a session can start the next unit, the last line is `/clear`**: say that the session
is landed and that the next turn starts from a cleared context.

**When the close parked the workstream, the last line is the person's action.** Name it in
one line, and say that no session is needed until it is done. Write the `/clear` sentence
above it: "after that, `/clear` and open the workstream". **A bare `/clear` at the end of a
parked close is a defect**: it sends the person to a session that can only report that it
waits on them.

This is not housekeeping. The pointer is only load-bearing if it is genuinely what
the next session starts from. A context carried across the boundary lets the next
turn run on remembered detail instead, so a thin or stale pointer still feels fine
— and the rot surfaces weeks later, to whoever opens it cold. Clearing is the only
thing that proves the pointer carries its own weight.

**Never write the `/clear` sentence in a message that is waiting for an answer.** The two
cancel: a message cannot wait for an answer and tell the reader to discard the place the
answer goes. A live question is a stop, and a stop is not a close. A close may still
*report* choices — the ones it wrote into the pointer's queue — and end with `/clear`; the
`/clear` sentence coming last is what marks them as queued rather than asked.

If the human keeps working in the same context after a close, say once that the
pointer has not been exercised and the next real cold start is still unverified.
Do not repeat it.

**In LOOP MODE the close ends with an outcome block, and the block is the last thing in
the message.** Write the `/clear` sentence above it. A close that landed work and named the
next unit writes a `continue` block. A close that landed the LAST unit writes a `done` block
that carries `story` and `landed`. A close that parked the workstream writes a `done` block
whose `reason` names the person's action. A close that landed nothing writes the block that
states why. See **LOOP MODE** below.

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
question is a `decision`. A message that reports landed work and names the next unit is a
`continue`. A message that reports no remaining work is a `done`, and it states the unit it
landed when it landed one. **There is no fourth outcome, and there is no
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

**`progress` is optional and it says where the plan stands.** It carries `done`, `remaining`,
`blocked` and `needsPerson`, each an integer counted from `STATUS.md` at the close. A runner
displays it; a session that cannot count them writes no field. **The field is optional, so it
raises no version** — a producer that never writes it keeps writing blocks that every reader
accepts.

```untethered-outcome
{
  "version": 1,
  "outcome": "continue",
  "story": "E02.1",
  "landed": true,
  "progress": { "done": 34, "remaining": 12, "blocked": 2, "needsPerson": 4 },
  "pointer": { "path": "NEXT_SESSION.md", "story": "E08.1", "rewritten": true }
}
```

⚠️ **`done` plus `remaining` is the whole backlog, and `blocked` and `needsPerson` are subsets
of `remaining`.** They overlap, so **do not add them up.**

**`progress` rides a `done` block too**, where it reports the state the run finished in.

`story` names the unit that this turn landed. `pointer.story` names the unit for the next
turn. The two fields differ. `pointer.path` is relative to the root of the repository.

**A batch that lands more than one unit also writes `stories`**: every unit that it landed,
in commit order. `story` names the first of them. The field is optional and raises no
version, and it rides a `done` block too. `pointer.story` names the first unit of the next
batch.
`pointer.rewritten` is true when the close rewrote the pointer whole.

**Write `pointer.blockedBy` when the next unit cannot start.** The field is optional, and
a next unit that is ready to build carries no field. **The field states what blocks the
unit, in one sentence.** The machine starts no unit on it, because a blocked next unit is
no unambiguous next unit of work. A runner may start one more session so that the session
raises the blocking decision; that session writes the `decision` block, never this one.

⚠️ **Before you write `blockedBy`, look for another unit that can start.** When one passes
the test of **A choice the loop session takes itself** below, name it in `pointer.story`,
write no `blockedBy`, and record the reorder in `decided`. **Write `blockedBy` only when no
unit of the backlog can start.**

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

⚠️ **A turn that lands the LAST unit of a backlog writes `done` and not `continue`**, because
`continue` requires `pointer.story` to name the next unit and there is no next unit to name.
**That `done` carries `story` and `landed`**, so the record of what the turn landed is not
lost. See the `done` form below.

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

**A `done` that landed a unit of work carries `story` and `landed` as well.** Both fields are
optional, they mean what they mean on a `continue`, and a `done` that landed nothing carries
neither.

```untethered-outcome
{
  "version": 1,
  "outcome": "done",
  "story": "E08.3",
  "landed": true,
  "reason": "E08.3 was the last unit of the backlog and nothing remains."
}
```

⚠️ **A `done` carries no `pointer` object**, because there is no next unit for a pointer to
name. The close still rewrites the pointer, and the block does not report it.

## Which outcome fires

| The session | The outcome |
|---|---|
| lands a unit of work, and completes the close | `continue` |
| lands a unit of work, finds the next unit blocked, and finds another unit that can start | `continue`, naming that unit, with the reorder in `decided` |
| lands a unit of work, and finds no unit of the backlog that can start | `continue`, with `pointer.blockedBy` |
| reaches a choice that passes every test of **A choice the loop session takes itself** | no block — take the choice, record it in `decided`, and keep working |
| stops at a choice that fails one of those tests, per **Presenting a decision** | `decision` |
| finds no unit that can start, and an open decision that the developer owns blocks one or more | `decision`, for the decision that unblocks the most work |
| finds a blocker that no answer of the developer removes, and nothing else is startable | `done` |
| finds an empty backlog | `done` |
| lands the LAST unit of the backlog, and nothing remains | `done`, with `story` and `landed` |
| reaches a phase gate holding a clause a session cannot verify | `done` |
| reaches an authorized action of class `run`, per **An authorized action** | no block — take it, record it in `decided` with `kind: "action"`, and keep working |
| reaches authorized actions of class `ask` | `decision`, with key `1` to run the listed steps and key `2` to decline them |
| parks the workstream on a person's action, per **A parked workstream** | `done`, and `reason` names the action |

**A stop and a fork are the same event.** Every stop that this skill names above is a
`decision` block: two or more unblocked candidates that cannot share a batch, a divergence that changes the work, an
open decision, a gate that is outward-facing, and a conflict between two decided
documents. The options of the block are the options that **Presenting a decision**
already asks for.

**A blocker is not a fork.** A fork gives the developer a choice between two courses. A
blocker leaves no course. A blocker is `done`, and `reason` names it.

**An open decision is a fork, even when it is the reason that nothing can start.** When the
pointer's queue holds decisions that the developer owns and they block every remaining unit,
write a `decision` block for the ONE decision that unblocks the most work, with that
decision's options and its recommendation first. **Do not write `done` because a unit is
blocked on a question**: the runner carries a `decision` to the developer's phone and resumes
the session with the answer, and a `done` ends the run with the question unasked. The other
decisions stay in the pointer, and the next stop raises the next one. **`done` is for a
blocker that no answer removes** — an empty backlog, an outside dependency, a gate clause, a
parked action.

## A choice the loop session takes itself

**A run is worth what it builds between two contacts with the developer.** A stop that the
developer answers by approving the recommendation costs a message, a wait, and often the
rest of the run. **In loop mode the session therefore takes two kinds of choice itself**,
runs the actions of class `run` (see **An authorized action**), and it records each one.
Every other choice is a `decision`, exactly as before.

### An ordering choice

**The choice is between units of work, not between ways to build one.** Two or more units
can start and nothing orders them, or the next unit is blocked and another unit could run
first.

**Take it when the unit you pick passes all four axes of the one-candidate test on its
own**: no open spike it depends on, no open decision it needs, **every predecessor that its
`**Dependencies:**` field and `plan/dependencies.md` declare has landed**, and no phase gate
stands in front of it.

🔴 **Never override a declared dependency.** A session that judges a declared dependency to
be "only an ordering" is changing the plan, and the plan is the developer's. That unit
cannot start, and the choice between it and waiting is a `decision`.

**Pick in this order:** the unit that the pointer or the plan recommends; else the unit that
unblocks the most later units; else the unit that comes first in `STATUS.md`.

### A choice between options, where the session has a recommendation

**Take the recommendation only when every one of these holds.** One failure makes it a
`decision`.

1. **It has grounds.** You wrote the reasoning, and it does not rest on a guess about what
   the developer prefers.
2. **It contradicts no decided document** — no requirement row, no ADR, no design
   document, no acceptance criterion, and no rule in the project's `CLAUDE.md` or
   `AGENTS.md`. 🔴 **A conflict between two decided documents always fails this test**,
   because taking either side overrules the other.
3. **No decided document reserves the choice for the developer.** A sentence such as "the
   developer picks", "a decision for the developer" or "needs approval" reserves it.
4. **It changes no acceptance criterion, landed or planned, and no public contract** — an
   exported name, a published format, a version, a schema, a migration.
5. **It is reversible inside the repository.** A later commit undoes it, and nothing outside
   the repository changed: no push, no deploy, no message, no new dependency, no spend.

🔴 **These are never taken, whatever the recommendation:** a gate clause, a 👤 criterion, an
outward-facing action that the project does not map to `run`, and anything that **Always
stop, regardless** names.

### The record

**Every choice taken this way goes on the record in three places.**

- **`decided` on the outcome block**: one entry for each choice.
- **The pointer**, under a heading **Choices the loop took**, for the developer to review.
  The next close carries an entry forward until the developer has seen it.
- **The commit message** of the unit the choice shaped.

```untethered-outcome
{
  "version": 1,
  "outcome": "continue",
  "story": "F03.2",
  "landed": true,
  "decided": [
    {
      "kind": "ordering",
      "question": "F03.3 waits on the gallon decision. What runs next?",
      "choice": "F03.4, whose declared predecessors have all landed",
      "reason": "F03.4 depends on F03.2 only, and the gallon decision stays in the queue"
    }
  ],
  "pointer": { "path": "NEXT_SESSION.md", "story": "F03.4", "rewritten": true }
}
```

`decided` is optional and it raises no version. Each entry carries `kind` — `ordering`,
`option` or `action` — and `question`, `choice` and `reason`, each one sentence. **It rides a `done`
block too.** ⚠️ **A choice that failed a test is not in `decided`**; it is the `decision`
block, or an entry of the decision queue.

### An authorized action

**An action is not a choice, and loop mode runs it by its class** (see **Who acts**). The
action must serve the unit that the session builds, and a decision must already fix what it
does.

- **`run`:** take it without a stop. Record it in `decided` with `kind: "action"`: the
  `question` names the step, the `choice` names its class, and the `reason` names the
  decision that fixed it.
- **`ask`:** write a `decision` block with two options. Key `1` runs the listed steps, and
  key `2` declines them. The `question` names the steps in one sentence. After a `1`, run the
  steps per **Ask once, for the whole batch**; after a `2`, park or name another unit.
- **`human`:** park, per **A parked workstream**. The close writes `done`, and `reason` names
  the action.

```untethered-outcome
{
  "version": 1,
  "outcome": "decision",
  "question": "Run the 4 test-environment steps of item 12: fill two secrets, apply, run the migration job?",
  "options": [
    { "key": "1", "label": "Run the 4 steps of item 12" },
    { "key": "2", "label": "Do not run them; park item 12" }
  ]
}
```

## The block does not replace the prose

The session writes its ordinary report first. A human reads the report, and the report
still names the options, the costs, and the recommendation. **The block repeats the
machine-readable part of that report, and it adds nothing that the prose omits.** A block
that disagrees with the prose above it is a defect.

## What loop mode does not change

Loop mode changes what the session **writes**. **It changes one thing that the session
decides**: the two kinds of choice in **A choice the loop session takes itself**, and the
actions of class `run`, each recorded. Nothing else.

- The one-candidate test is unchanged for the unit itself. Loop mode does not lower the bar
  for an auto-start.
- **Every other pick stays human.** A `decision` block reaches the developer by a message. It
  is not a licence to pick.
- A conflict between two decided documents still gets a memo, and the block that reports
  it is a `decision`.
- The close is unchanged. Every step of Part 1 and Part 2 runs before a `continue` block.
- **A subagent writes no outcome block and takes no choice of its own.** Only the main
  session declares the outcome, and only the main session applies **A choice the loop
  session takes itself**.
- **Never fabricate an outcome.** A `continue` block on a turn that landed nothing is the
  same failure as a wrong ✅, and the machine acts on it.

---

# Standing rules

- **Never fabricate a status.** ⬜ is better than a wrong ✅. A wrong ✅ costs a
  future session the time to discover it, plus the trust in every other row.
- **Write every artifact in Simplified Technical English** — `../../reference/writing-style.md`. It governs the STATUS line, the `Rejected:` line, the requirement row, the HANDBOOK entry, and the pointer; it does not govern what you say to the human, and it is never a reason to reword a decided document.
- **Never mark a gate CLAUSE closed by inference.** Clauses are written to be
  checked, not reasoned about. **The gate LINE is different — it is the conjunction
  of its clauses**, so a session closes it when every clause is `[x]`. Reading the
  boxes is not inferring from them.
- **The close-out rides with the work, not with "the next commit."** That deferral
  is how the whole system degrades into three files nobody trusts.
- **A stop is a choice, not a question.** Options, costs, and a recommendation with
  its reasoning where you have grounds — in the session, in a memo, and in the
  pointer's decision queue alike.
- **A close ends on the next action.** That is `/clear` when a session can start the next
  unit, and the person's action when the workstream is parked. A session that can only
  report "it waits on you" is a session that nobody needed.
- **A decision is the developer's, and an action belongs to its class.** Never park on an
  action that the session could take after one yes, and never take a decision because a
  class allows the action that follows it.
- **The pick stays human whenever there is a pick to make.** One unambiguous
  candidate is not a choice, and neither is a batch of every live unit that passes on
  its own; stopping to ask about either just spends a human's
  attention on rubber-stamping. Two candidates that cannot share a batch and that the
  pointer leaves to the human, an open decision, or a divergence *is* a choice — and
  those stop. Conflicts between two decided documents, and **any gate
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
- **Stay in your own lane.** One pointer per workstream.
  If you are about to edit a file another workstream owns, that is the signal to
  stop and say so — not to merge on their behalf.
