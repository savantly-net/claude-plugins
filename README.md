# Savantly Claude Code Plugins

Claude Code plugins for Savantly engineers.

Currently one plugin: **`flow`** — the spec-to-shipped workflow, five skills that hand off to each other.

## Install

```
/plugin marketplace add savantly-net/claude-plugins
/plugin install flow@savantly
```

To give a whole project team the skills on clone, commit this to the project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "savantly": {
      "source": { "source": "github", "repo": "savantly-net/claude-plugins" },
      "autoUpdate": true
    }
  },
  "enabledPlugins": { "flow@savantly": true }
}
```

## The pipeline

Alphabetical order in the `/` menu doesn't match the order you use these in. This is the order:

```
        new project                     existing codebase
             │                                 │
     /flow:create-spec                 /flow:analyze-fit
   idea → requirements,              idea → same, but graded
   architecture, ADRs                against the real code
             │                                 │
             └────────────┬────────────────────┘
                          ▼
               /flow:create-blueprint
          requirements → MVP scope, epics,
          designs, phases, dependencies
                          │
                          ▼
               /flow:create-flywheel
           plan → the dev process itself
        (installs it; run once per project)
                          │
                          ▼
                /flow:turn-flywheel
          open a session · do the work · close it
       (open it each session; it closes itself)
```

| Skill | Takes | Produces |
|---|---|---|
| `/flow:create-spec` | A charter, idea, or brief | `requirements/`, `architecture/`, ADRs |
| `/flow:analyze-fit` | A feature idea + the current codebase | The same, plus an impact map, a constraint check, and approach options — including "don't build this" |
| `/flow:create-blueprint` | Either of the above | `plan/` — MVP scope, epics, stories, designs, phases |
| `/flow:create-flywheel` | A plan, or an existing codebase | The tracked docs and the session protocol in `AGENTS.md` |
| `/flow:turn-flywheel` | A scaffolded project | An oriented session at the start — working on the next unit already, if only one is live — and a landed one at the end |

Each skill is interactive and stops for human review between phases. None of them estimate time or cost.

**Every file they write is in Simplified Technical English.** The rules live in `flow/reference/writing-style.md` — the enforceable core of ASD-STE100: one topic per sentence, 20/25-word limits, active voice with the actor named, one word per meaning, noun clusters of three or fewer, one obligation per requirement row, no vague quantities. The approved 900-word dictionary is not applied and the artifacts don't claim conformance. It governs generated files only: conversation stays normal prose, quoted material is reproduced verbatim, and an already-decided document is amended by appending — never restyled, because its wording is someone's decision.

## Using them

### Picking an entry point

One question: **does the code exist yet?**

- **No** — new product, new service, a greenfield rewrite → `/flow:create-spec`
- **Yes** — a feature, integration, or capability landing in something you already run → `/flow:analyze-fit`

Rule of thumb: if answering "should we build this?" makes you say *"well, it depends how X currently works"* — that's `analyze-fit`. It reads the actual code before it grades the idea, and "don't build this" is one of the outcomes it's allowed to reach.

### When to reach for each

| Skill | Reach for it when | Have ready | Run it |
|---|---|---|---|
| `create-spec` | A charter, RFP, or napkin idea needs to become something you can plan against | The source doc, or be ready to talk it through | Once per project |
| `analyze-fit` | Someone asks "can we add X?" and the honest answer needs the codebase | A description of the idea; run it **from the repo in question** | Once per feature idea |
| `create-blueprint` | Requirements are settled and you need scope, epics, and a build order | A directory with `requirements/` + `architecture/` | Once per project (again after a major re-scope) |
| `create-flywheel` | A plan is ready to be worked — or an existing project needs the process retrofitted | The plan directory, or just the repo | **Once per project, ever** |
| `turn-flywheel` | You're sitting down to work | Nothing | **Once at the start — the close fires itself** |

### What a run feels like

The first three are **long interactive sessions**, not one-shot generators. Each works through 7 phases, presents its output, and waits for you before moving on. Expect to actually participate — the phases where you push back are the ones that make the artifacts worth having.

- **Nothing is written until the end.** You can bail after any phase and lose only the conversation.
- **Technology choices are never finalized without you.** Anything unconfirmed is marked `PROPOSED`.
- **Vague input gets flagged, not filled in.** If the brief is thin, the skill says so rather than inventing acceptance criteria.
- **Type them explicitly.** These are heavy workflows — invoke them on purpose rather than letting them fire off a passing mention.

### A greenfield run

```
/flow:create-spec docs/charter.md          # → requirements/, architecture/, ADRs, open-questions.md
/flow:create-blueprint projects/thing/     # → plan/: MVP scope, epics, designs, phases
/flow:create-flywheel projects/thing/      # → STATUS.md, HANDBOOK, pointers, AGENTS.md protocol
/flow:turn-flywheel                        # → first session opens
```

### A feature on an existing codebase

```
cd the-repo
/flow:analyze-fit "let franchisees export their territory map as a PDF"
```

That alone may be the whole job — an impact map, a constraint check, and three approach options is often enough to decide. If it turns into real work:

```
/flow:create-blueprint docs/use-cases/territory-pdf-export/
/flow:turn-flywheel                        # the repo already has the flywheel installed
```

### The daily loop

Once a project is scaffolded, `turn-flywheel` is the only one you type regularly:

```
/flow:turn-flywheel                # start of session: orient, verify, then start the next story — or stop for your pick
...ordinary engineering...
                                   # the close fires by itself when the story lands:
                                   # STATUS line, requirement rows, design amendments, rewrite the pointer
/clear                             # then wipe the context — the next turn must cold-start from the pointer
                                   # (or, when only you can act next, the close parks the workstream and names your action)
```

**It starts the work when there's nothing to pick.** If the pointer names one specific story, it's unblocked, nothing diverged, and its ACs are specific enough to build against, the session says what it's starting and starts. Stopping to ask about a single candidate spends your attention on a rubber stamp. It still stops — every time — for two live candidates, an open decision or spike, a divergence the verification turned up, or scope it would have to invent. `/flow:turn-flywheel ask` forces the propose-and-stop behavior on any run.

**When it stops, it stops with a choice.** Every stop — the pick between candidates, a divergence needing a disposition, an item going into the pointer's decision queue — comes as two or three named options, what each costs and unblocks, and a recommendation with its reasoning where there are grounds for one. It says plainly when there aren't. A recommendation is never a default: it won't start the recommended option while waiting on you.

**It closes itself.** The close isn't a command you have to remember: it fires when the story's ACs are met and the work is about to be committed (that commit carries the STATUS line and the requirement rows), when a blocker halts the work with nothing else startable, or when the context is materially consumed. One batch per session — one unit, or several live units whose files don't overlap, built in parallel lanes and committed one unit at a time — and after the close the session is over and doesn't reopen in the same context. `/flow:turn-flywheel close` still works when you want to land early.

**Close every session, including the ones where nothing landed.** A session that only discovered a blocker produces the most valuable pointer update available — and skipping the close is the single thing that degrades the whole system into three files nobody trusts.

**Then `/clear`.** This isn't just about keeping the context window lean, though it does that. The pointer only works if it is genuinely the thing the next session starts from. Carry a warm context across the boundary and the next turn quietly runs on remembered detail instead — so a pointer that is thin, stale, or wrong still *feels* fine, and you find out weeks later when someone else opens it cold. Clearing is what proves the pointer carries its own weight.

**Unless the next move is yours.** When no session can start anything and every remaining item needs a person — a 👤 check, an account, a step on hardware — the close *parks* the workstream: it names the one action that wakes it, and that action is the last line, not `/clear`. Before it parks, it looks for prep that makes your part smaller (screenshots for a check, the exact commands in order). Opening a parked workstream before you act gets a few-line report; telling it the action is done gets a short close that ticks the clause.

It also keeps the close honest in the other direction: if you can still see the whole session, it's tempting to skip the rejected option or the untested path because everything is obviously in your head. It won't be tomorrow.

### You don't have to run the whole pipeline

`analyze-fit` ending in "don't build this" is a successful run. `create-spec` without a blueprint is fine if the project isn't funded yet. The only skill that expects its predecessor's output on disk is `create-blueprint`, and it will tell you what's missing rather than guess.

## What the flywheel installs

Seven roles, one fact in one home:

| File | Holds |
|---|---|
| `requirements/*.md` | The spec, with a per-row Status column |
| `STATUS.md` | Phases, gates, spikes, infra, open decisions |
| `<workstream>/NEXT_SESSION.md` | Session state only — the goal, the decision queue, the baseline (branch and PR, never commit counts) |
| Root `NEXT_SESSION.md` | Index of active workstreams, one line each |
| `docs/HANDBOOK.md` | Standing knowledge — traps, conventions, how to run things |
| `docs/limits.json` | A byte cap for each tracked document, enforced by a test |
| `plan/epics/*.md` | Frozen spec + dated amendments |

## Working in parallel

The flywheel was originally extracted from a solo project. One change makes it safe for a team:

**One `NEXT_SESSION.md` per workstream, plus a root index.** The pointer is rewritten *wholesale* at every session close, which is the worst possible input to a git merge — so two developers must never share one. Separation comes from one pointer per stream.

**Pointers are committed. Never gitignore them.** It's tempting — they look like scratch state. But the pointer holds the *decision queue*, which is a queue a human is supposed to read, and an untracked pointer makes a handoff (vacation, review, someone picking up your stream) impossible at exactly the moment the accumulated context is worth the most.

## Where the reasoning lives

Git records what happened. Two things it can't record, and the close writes them where a reader already looks:

- **Rejected options** — work that was considered and refused leaves no commit and no diff. It goes into the design doc as a dated `Rejected:` line, under the section it constrains, because that's what the next session reads before taking the obvious approach.
- **What was NOT exercised** — the negative space around a story's testing. It goes into the pull request body beside the deploy impact, and into the commit message.

The reasoning for a requirement amendment goes into the PR body and the commit message that make it. `git blame` on the row finds the commit, and the PR survives a squash merge. A story with nothing rejected and nothing untested writes nothing extra.

Earlier versions wrote a `docs/history/<story-id>.md` narrative at every landing. Most of it repeated the STATUS line, the design doc and the PR, and it was rarely read — so it's gone. A project that already has one keeps it as a read-only record.

## Contributing

Skills live in `flow/skills/<name>/SKILL.md`, with supporting files (`phases.md`, `templates.md`, `output-structure.md`, `adr-template.md`) alongside. `flow/reference/writing-style.md` is shared by all five — it is the one place the artifact writing rules are defined, and each skill points at it by relative path.

The skills reference each other by slash command. If you rename one, sweep every reference — a half-done rename breaks the handoff between skills silently, which is the failure mode this whole system is built to avoid:

```
grep -rn "/flow:\|/create-spec\|/analyze-fit\|/create-blueprint\|/create-flywheel\|/turn-flywheel" flow/skills/
```

Bump `version` in both `.claude-plugin/marketplace.json` and `flow/.claude-plugin/plugin.json` when you change a skill.

## License

Apache License 2.0. See [LICENSE](LICENSE).

Copyright 2026 Savantly LLC.

The files these skills generate in your project are yours.
