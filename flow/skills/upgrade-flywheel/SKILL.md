---
name: upgrade-flywheel
description: Upgrade a project that an earlier /create-flywheel scaffolded to the current flywheel — compare each scaffolded file against the current templates, present the gaps, then replace the stale scaffold text (the AGENTS.md protocol section, the Action classes block, the document caps and their check, the pointer and handbook preambles) while every project fact, status value and filled-in mapping stays. Run it when the plugin has moved ahead of a project's protocol. Not for an unscaffolded project (use /create-flywheel) and not a session (use /turn-flywheel).
argument-hint: [path/to/project-or-repo]
allowed-tools: Read Grep Glob Write Edit Bash
---

# Upgrade the Flywheel

`/create-flywheel` **installs** the process once. `/turn-flywheel` **runs** it. This skill
**brings an installed process up to the current version** of the plugin.

The plugin changes between versions; a scaffolded project does not change with it. At
runtime `/turn-flywheel` governs, so an old protocol in `AGENTS.md` is a version skew and
not a conflict. But a stale file still misleads: a developer reads the old rule, a test
has no cap to enforce, and the **Action classes** block that a session reads is missing.
This skill removes the skew from the files themselves.

**The source of truth is `../create-flywheel/templates.md`.** This skill holds no template
text of its own. Compare the project against that file as it is now, so this skill never
drifts behind a release.

**The word is "upgrade", never "migration".** In these skills a migration is a database
migration, and one word has one meaning.

## What is scaffold text, and what is project content

This is the one distinction the skill must get right. An upgrade replaces scaffold text and
keeps every project fact.

| Scaffold text — replace with the current template | Project content — keep exactly |
|---|---|
| The `AGENTS.md` (or `CLAUDE.md`) section **Dev process — the session flywheel**: where things live, the session protocol, the working-in-parallel note | Lines the project added to that section: a lane-count override, a note that names its own systems, a project-specific rule |
| The **Action classes** block: its heading and its list of class names | The value after each class name, when the developer filled one in |
| The blockquote preamble at the top of `STATUS.md`, of each pointer, and of the root index | Every phase, gate, story line, status mark, decision, and every section of session state |
| The handbook's fixed sections: the writing rules, caps and correction, verification principles, test rules, and the universal conventions | Every trap, gap, run instruction, and project convention |
| The tracked-table preamble and the epic header note | Every requirement row, Status value, amendment, and AC |
| `docs/limits.json`, its `rule` text, and the cap check | The cap values a session already set |

**An old form of a current rule is scaffold text.** In the protocol section and the
handbook's fixed sections, a bullet whose bold lead-in or topic a current rule covers is
that rule's earlier wording: replace it. For example, the old "A blocker that has stopped
the next unit for three closes" is the current "A queue entry that has blocked the next
unit for three closes".

**A line keeps its place only when it names something of the project's**: a system, a
number, a person, a path, or an override of a rule. Any other line that matches no current
rule goes to the developer in step 4 as keep or drop. Never keep it, and never drop it,
without the answer.

## Changes that touch the files

Most versions changed the protocol text only, and the section replacement in step 4
covers them. A few changed what is on disk; check for each one by its effect:

- **1.6.0 — document caps.** `docs/limits.json` and a cap check in the test command. Before
  1.6.0, the handbook said "append as they bite"; now it says merge into the entry that exists.
- **1.7.0 — no per-story history file.** The `STATUS.md` preamble, the handbook and
  `AGENTS.md` no longer tell a session to write `docs/history/<story-id>.md`. A rejected
  option is a `Rejected (<date>):` line in the design doc. **The `docs/history/` directory
  stays as a read-only record**: remove the instruction to write to it, never the files.
- **1.11.0 — a parked workstream.** The root index's State column allows
  `Parked: waits on <person> to <action>`.
- **1.12.0 — action classes.** `AGENTS.md` carries an **Action classes** block, and the
  protocol has a **Who acts** rule.
- **1.13.0 — a version stamp.** The protocol section's heading names the plugin version:
  `(flow <version>, installed <date>)`.

## Process

### 1. Locate

If arguments were given, treat `$ARGUMENTS` as the project directory. Otherwise look for
`STATUS.md` and a `NEXT_SESSION.md` at the repo root, under `docs/`, or under
`projects/<name>/`. Find the file that holds the protocol: `AGENTS.md`, or `CLAUDE.md` when
that is the repo's convention.

- **No `STATUS.md` and no `NEXT_SESSION.md`** → the project is not scaffolded. Stop and name
  `/create-flywheel`.
- **The working tree has uncommitted changes** → stop and say so. An upgrade goes in its own
  commit, never mixed with story work.

### 2. Read the two versions

- **The current version**: the `version` field of `../../.claude-plugin/plugin.json`, the
  manifest of the plugin this skill is in.
- **The project's version**: the stamp in the protocol section's heading,
  `(flow <version>, installed <date>)`. A project scaffolded before 1.13.0 has only
  `(installed <date>)`; say "before 1.13.0", and do not guess a closer number.

When step 3 finds no gap, report that the project is current and end. When the stamp is
also current, write nothing. When only the stamp is old, the one change is the stamp: present
it in step 4 like any other gap.

### 3. Compare, file by file

For each role, read the project's file and the matching template section, and list each
gap. A gap is a scaffold sentence that differs, a scaffold sentence that is missing, or a
stale instruction that the template no longer gives.

| Role | Template section |
|---|---|
| Tracked-table preamble in `requirements/` | §1 |
| `STATUS.md` preamble | §2 (the blockquote only) |
| Root `NEXT_SESSION.md` preamble | §3 (the blockquote and the State column hint) |
| Each workstream pointer's preamble | §4 (the blockquote only) |
| `docs/HANDBOOK.md` fixed sections | §5 |
| Epic header note | §6 |
| Protocol section and **Action classes** | §7 |
| `docs/limits.json` and the cap check | §8 |

**Check what the cap check covers**: every path in `docs/limits.json` must exist, because the
check skips a path it cannot find. A path that does not exist is a gap.

**Check for pointers that are in flight.** A pointer whose workstream has an open branch or
pull request is rewritten wholesale at that workstream's next close. An upgrade that edits
it now makes a merge conflict. **Leave that pointer as it is**, and list it in the report: a
second run of this skill, after that branch lands, upgrades its preamble.

### 4. Present the gaps, then stop

Give one list, grouped by file. For each gap, say what changes and whether it is
**replace** (scaffold text), **add** (a missing file or block), or **keep** (project content
that sits in scaffold space). Then present the choices that are the developer's:

- **Action classes.** The upgrade writes the class names with blank values. A blank class is
  `ask`, so the next session asks once before a push or a merge. Offer to record a mapping
  now when the developer gives one; never choose a mapping yourself.
- **A rule the project changed on purpose.** When a project line contradicts a current rule,
  for example "a batch holds one lane", it may be a decision. Ask whether to keep it as a
  project override or to drop it.
- **Caps.** The current protocol section is longer than an old one, so its cap must rise.
  Name each cap that rises and its new value.

This stop is a choice, per the protocol: name the options, their cost, and a recommendation.

### 5. Apply

On approval, on a branch that holds nothing else:

1. **Replace the protocol section** with §7. Keep the heading's install date, and stamp the
   heading with the current version: `(flow <version>, installed <date>)`. Put each kept
   project line back under the rule it qualifies.
2. **Write the Action classes block** with each filled-in value carried over, and each other
   class blank.
3. **Replace the preambles** in `STATUS.md`, the root index, and each pointer that is not in
   flight. Change nothing below a preamble.
4. **Replace the handbook's fixed sections.** Keep every project entry in the sections that
   collect them.
5. **Remove each instruction to write `docs/history/`.** Keep the directory and its files.
6. **Add or repair `docs/limits.json` and the cap check**, per §8. A cap is the file's size
   after the upgrade, rounded up to the next 1 000 bytes. A cap that exists changes only when
   the upgrade made its file longer than the cap; a cap the upgrade did not breach stays.
7. **Do not restyle anything else.** Requirement rows, ACs, designs and epics keep their
   wording, because their wording is someone's decision.

### 6. Verify

- Run the project's test command, or the cap check script, and read its exit code.
- **Measure the cap check**, when this upgrade added it: append 1 000 bytes to one capped
  file, run the check, read the failure, and revert.
- Grep the scaffolded files for `docs/history/` instructions that remain, and for a protocol
  section without a version stamp.

### 7. Commit and report

Commit on the branch, with a message that names the version step, for example
`flywheel: upgrade from before 1.13.0 to 1.13.0`. **Do not push and do not open a pull
request**: those are `branch-share` and `shared-change` actions, and the project's
**Action classes** decide them.

Then report:

- the version step, and each file changed;
- each cap that rose, with the old and new values;
- each project line kept, and each "kept, unplaced" line;
- each pointer left as it is, and that a second run after its branch lands upgrades it;
- the Action classes that are still blank, and what a blank class does.

The last line is the next action: `/flow:turn-flywheel` opens the next session under the
current protocol.

## Behavior rules

- **Never overwrite a project fact.** When you are not sure whether a line is scaffold text or
  project content, it is project content.
- **Never fabricate a mapping, a status, or a cap.** A blank class stays blank, a status stays
  as the project wrote it, and a cap comes from a measured size.
- **Never touch session state.** The pointers' sections, the index lines, and `STATUS.md`
  below its preamble belong to the sessions that wrote them.
- **Never delete `docs/history/`.** It is a read-only record.
- **Respect the repo's layout.** Use the project's paths and the file it already uses for the
  protocol; do not move a file to match a template path.
- **Write every changed scaffold line in Simplified Technical English**, per
  `../../reference/writing-style.md`. The template text already obeys it.
- **This is not a session.** Do not open a unit, do not run the close, and do not rewrite a
  pointer's session state.
