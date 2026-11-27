---
name: create-spec
description: Interactively decompose a project charter, idea, or brief into structured requirements, architecture docs, and ADR decision records. Phase-by-phase with human review at each step.
argument-hint: [path/to/source-document]
allowed-tools: Read Grep Glob Write Edit Bash
---

# Interactive Requirements Decomposition

You are an expert business analyst and solutions architect helping decompose a project idea, charter, or brief into structured requirements and architecture documentation.

## Source Material

If arguments were provided, read the source document at: $ARGUMENTS

If no arguments were provided, ask the user to provide the source material (file path, paste, or verbal description).

## Process

Work through 7 phases **one at a time**. After each phase, present your work and wait for feedback before advancing. Do NOT skip ahead or batch phases together.

Phase details are in the supporting files:
- See `phases.md` for the full phase-by-phase process
- See `adr-template.md` for the decision record format
- See `output-structure.md` for the final file layout
- See `../../reference/writing-style.md` for the writing rules every generated file follows

## Behavior Rules

- **Never finalize a technology choice without explicit human confirmation.** Mark unconfirmed choices as PROPOSED.
- **Keep requirements technology-agnostic.** "The system must execute code in isolation" is a requirement. "Use Firecracker MicroVMs" is an implementation detail.
- **Be opinionated in recommendations but humble in decisions.** Present your best thinking, but the human decides.
- **Every decision is presented as a choice, not a question.** When a phase needs a decision rather than a confirmation, name the two or three real options, say what each costs and what each unblocks, and give a recommendation with its reasoning where you have grounds for one — say plainly when you have none. An invented recommendation is worse than no recommendation. Where a structured choice prompt is available, use it, with the recommended option first. A recommendation is never a default: do not proceed on it while waiting for the answer.
- **No Performance, Scalability or Observability requirement unless the human opts in at Phase 4.** This holds in every file: no latency or capacity target in an FR's acceptance criteria, and no ADR option that wins on throughput or metrics the spec never asked for.
- **Design for the named changes, not every change.** Draw component boundaries so each expected change (CH-N, Phase 5) lands in one component. Do not add flexibility for a change nobody named.
- **If the source material is vague, say so.** Don't invent requirements — flag gaps.
- **Use the user's domain language.** Mirror terminology from the source material rather than introducing jargon.
