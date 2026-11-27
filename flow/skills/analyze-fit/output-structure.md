# Output Structure

After all 7 phases are complete, write the final documents into the project repository so they can be committed. The layout matches `/create-spec`'s output so `/create-blueprint` can consume it unchanged, with one extra `codebase/` directory for the brownfield-specific artifacts.

```
docs/use-cases/<use-case-slug>/
├── use-case.md                    # the cleaned use-case brief (from Phase 1)
├── codebase/
│   ├── impact-map.md              # touched subsystems + constraints (Phase 2)
│   ├── fit-analysis.md            # fit/gap per subsystem + constraint check (Phase 3)
│   └── options.md                 # considered approaches + chosen direction (Phase 4)
├── requirements/
│   ├── business.md                # BR-N (Phase 5)
│   ├── functional.md              # FR-X.Y (Phase 5)
│   └── non-functional.md          # NFR-N (Phase 5)
├── architecture/
│   ├── overview.md                # how it sits in the existing system + Expected changes (Phase 6)
│   ├── components.md              # new + modified components (Phase 6)
│   └── decisions/
│       ├── adr-001-<topic>.md     # one per *new* decision introduced (Phase 6)
│       └── ...
└── open-questions.md              # blockers + watch items (Phase 7)
```

## Slug rules

- Derive `<use-case-slug>` from the use-case title: lowercase, hyphen-separated, no punctuation.
- Keep it short (≤40 chars). Example: `cross-tenant-memory-sharing`, not `allow-agents-to-share-memories-across-different-tenants-for-federated-teams`.

## Rules for output location

- Default to `docs/use-cases/<slug>/` at the repo root. This is committed intentionally — the artifacts are durable context for the feature.
- If the repo already has a different convention (e.g., `projects/`, `specs/`, `rfcs/`, or a pre-existing `docs/use-cases/`), ask the user where to write before creating files.
- If a directory already exists at the target path, **never silently overwrite.** Ask whether to merge, overwrite, or pick a new slug.
- Do not write to `customers/*/projects/` — that's a `/create-spec` convention and doesn't apply here.

## options.md — brownfield-specific artifact

`codebase/options.md` preserves the Phase 4 decision trail even after Phase 5+ elaborate the chosen path. Include:

- All options presented (Minimal / Proper / Ambitious / Don't-build), each with its tradeoffs
- Which one the user picked and why
- What the rejected options would have cost or gained, for future reference

This matters because use-case decisions get revisited. Without this file, the "why we didn't do X" context is lost.

## Compatibility with `/create-blueprint`

`/create-blueprint` reads:
- `requirements/business.md`
- `requirements/functional.md`
- `requirements/non-functional.md`
- `architecture/overview.md`
- `architecture/components.md`
- `architecture/decisions/*.md`
- `open-questions.md`

All of these are produced by Phases 5-7. The `use-case.md` and `codebase/` files are extra context that `/create-blueprint` can read for background but isn't required to consume.

At the end, tell the user:
- The full path to the written directory
- That they can run `/create-blueprint docs/use-cases/<slug>/` next
- A one-line summary of the recommended approach
- Any blocker-category open questions that should be resolved before `/create-blueprint`
