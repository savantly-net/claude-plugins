# Output Structure

After all 7 phases are complete, write the final documents to the project directory using this structure:

```
projects/<project-name>/
├── plan/
│   ├── mvp-scope.md              # MVP definition — what's in, out, deferred
│   ├── epics/
│   │   ├── epic-01-<name>.md     # one file per epic with all stories
│   │   ├── epic-02-<name>.md
│   │   └── ...
│   ├── dependencies.md           # dependency graph and critical path
│   ├── spikes.md                 # unknowns requiring prototyping/validation
│   ├── infrastructure.md         # provisioning plan and sequence
│   └── phases.md                 # phased build roadmap with gates
├── design/
│   ├── <component-1>.md          # one file per component
│   ├── <component-2>.md
│   └── ...
```

## Rules for output location

- Write into the same project directory that contains the `/create-spec` output.
- Never overwrite existing create-spec output (requirements/, architecture/, charter.md, open-questions.md).
- If `plan/` or `design/` directories already exist, ask the user whether to overwrite or merge.
- Tell the user when all files are written and summarize what was produced.

## Traceability

Every document must maintain traceability:
- Stories reference FR-* and NFR-* IDs
- Designs reference ADR-* decisions
- Infrastructure items reference components
- Phases reference epics and spikes

This allows anyone to trace from "why are we doing this?" (business requirement) through "what does it do?" (functional requirement) to "how is it built?" (design) to "when do we build it?" (phase).
