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
/flow:create-flywheel projects/thing/      # → STATUS.md, docs/history/, pointers, AGENTS.md protocol
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
                                   # STATUS line, history entry, requirement rows, rewrite the pointer
/clear                             # then wipe the context — the next turn must cold-start from the pointer
```

**It starts the work when there's nothing to pick.** If the pointer names one specific story, it's unblocked, nothing diverged, and its ACs are specific enough to build against, the session says what it's starting and starts. Stopping to ask about a single candidate spends your attention on a rubber stamp. It still stops — every time — for two live candidates, an open decision or spike, a divergence the verification turned up, or scope it would have to invent. `/flow:turn-flywheel ask` forces the propose-and-stop behavior on any run.

**When it stops, it stops with a choice.** Every stop — the pick between candidates, a divergence needing a disposition, an item going into the pointer's decision queue — comes as two or three named options, what each costs and unblocks, and a recommendation with its reasoning where there are grounds for one. It says plainly when there aren't. A recommendation is never a default: it won't start the recommended option while waiting on you.

**It closes itself.** The close isn't a command you have to remember: it fires when the story's ACs are met and the work is about to be committed (that commit carries the STATUS line and the history entry), when a blocker halts the work with nothing else startable, or when the context is materially consumed. One unit of work per session — after the close the session is over and doesn't reopen in the same context. `/flow:turn-flywheel close` still works when you want to land early.

**Close every session, including the ones where nothing landed.** A session that only discovered a blocker produces the most valuable pointer update available — and skipping the close is the single thing that degrades the whole system into three files nobody trusts.

**Then `/clear`.** This isn't just about keeping the context window lean, though it does that. The pointer only works if it is genuinely the thing the next session starts from. Carry a warm context across the boundary and the next turn quietly runs on remembered detail instead — so a pointer that is thin, stale, or wrong still *feels* fine, and you find out weeks later when someone else opens it cold. Clearing is what proves the pointer carries its own weight.

It also keeps the close honest in the other direction: if you can still see the whole session, it's tempting to write a thin history entry because everything is obviously in your head. It won't be tomorrow.

### You don't have to run the whole pipeline

`analyze-fit` ending in "don't build this" is a successful run. `create-spec` without a blueprint is fine if the project isn't funded yet. The only skill that expects its predecessor's output on disk is `create-blueprint`, and it will tell you what's missing rather than guess.

## What the flywheel installs

Seven roles, one fact in one home:

| File | Holds |
|---|---|
| `requirements/*.md` | The spec, with a per-row Status column |
| `STATUS.md` | Phases, gates, spikes, infra, open decisions |
| `docs/history/<story-id>.md` | One file per story: decisions, **rejected options**, traps, numbers, **what was NOT exercised** |
| `<workstream>/NEXT_SESSION.md` | Session state only — the goal, the decision queue, the baseline (branch and PR, never commit counts) |
| Root `NEXT_SESSION.md` | Index of active workstreams, one line each |
| `docs/HANDBOOK.md` | Standing knowledge — traps, conventions, how to run things |
| `plan/epics/*.md` | Frozen spec + dated amendments |

## Working in parallel

The flywheel was originally extracted from a solo project. Two changes make it safe for a team:

**One `NEXT_SESSION.md` per workstream, plus a root index.** The pointer is rewritten *wholesale* at every session close, which is the worst possible input to a git merge — so two developers must never share one. Separation comes from one pointer per stream.

**One `docs/history/` file per story**, not one shared append-only log. Two developers landing two stories touch two different files, so the narrative never conflicts.

**Both are committed. Never gitignore them.** It's tempting — they look like scratch state. But the pointer holds the *decision queue*, which is a queue a human is supposed to read, and an untracked pointer makes a handoff (vacation, review, someone picking up your stream) impossible at exactly the moment the accumulated context is worth the most.

## Why `docs/history/` exists when `git log` exists

Git records what happened. These files record what *didn't*:

- **Rejected options** — work that was considered and refused leaves no commit and no diff. It is invisible to git by construction, and it's the first thing a future session re-litigates from scratch.
- **What was NOT exercised** — the negative space around a story's testing. Nobody writes it in a commit message and no diff reveals it.
- **The numbers as of then** — test and mutation counts at the moment the story landed.

Also practical: squash-merge workflows flatten per-commit granularity anyway, and an agent scanning months of `git log` burns context that a targeted `STATUS.md` line → history file read does not.

## Contributing

Skills live in `flow/skills/<name>/SKILL.md`, with supporting files (`phases.md`, `templates.md`, `output-structure.md`, `adr-template.md`) alongside. `flow/reference/writing-style.md` is shared by all five — it is the one place the artifact writing rules are defined, and each skill points at it by relative path.

The skills reference each other by slash command. If you rename one, sweep every reference — a half-done rename breaks the handoff between skills silently, which is the failure mode this whole system is built to avoid:

```
grep -rn "/flow:\|/create-spec\|/analyze-fit\|/create-blueprint\|/create-flywheel\|/turn-flywheel" flow/skills/
```

Bump `version` in both `.claude-plugin/marketplace.json` and `flow/.claude-plugin/plugin.json` when you change a skill.
