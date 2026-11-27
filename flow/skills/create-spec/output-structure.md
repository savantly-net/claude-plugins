# Output Structure

After all 7 phases are complete, write the final documents to the project directory using this structure:

```
projects/<project-name>/
├── charter.md                    # cleaned source (if not already created)
├── requirements/
│   ├── business.md
│   ├── functional.md
│   └── non-functional.md
├── architecture/
│   ├── overview.md           # ends with the Expected changes (CH-N) table
│   ├── components.md
│   └── decisions/
│       ├── adr-001-<topic>.md
│       ├── adr-002-<topic>.md
│       └── ...
└── open-questions.md
```

## Rules for output location

- Check if there's already a project folder for this work — look in the current working directory and in `customers/*/projects/`.
- If a charter.md already exists, don't overwrite it — reference it.
- If the location is ambiguous, ask the user where to write.
- Tell the user when all files are written and summarize what was produced.
