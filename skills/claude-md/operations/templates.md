# CLAUDE.md Template Blocks

Composable blocks for generating the `## Headkey Memory` section in CLAUDE.md. Each block is a behavioral trigger: "When X, do Y." Claude already knows tool schemas from MCP — these instructions tell it *when* to act, not *how*.

## Template Blocks

### Session Continuity (always included)

```
At session start, call `ask` with "What do I know about {project}?" to load prior context before doing any work.
When you learn something significant about {project} (architecture, conventions, gotchas), call `remember` with source: "{project}" and relevant tags.
When you make a decision or choose an approach, call `believe` with the decision and confidence 0.7-0.9.
```

### Architecture Graph (3+ services or modules)

```
When discovering how components, services, or modules connect, call `relate` with subject, object, and relationship description.
When exploring unfamiliar parts of the codebase, call `entities` to check what is already mapped before investigating from scratch.
```

### Decision Tracking (active development)

```
Before making significant changes, call `beliefs` with category: "decision" to check for prior decisions and potential conflicts.
When a previous decision is reversed or updated, call `believe` with the new decision — conflicts with prior beliefs are detected automatically.
```

### Knowledge Gap Detection (complex projects)

```
When unsure what is already known about a topic, call `ask` with a specific question before researching from scratch.
When you discover gaps in project knowledge, call `remember` with importance: "high" and tag: "gap" to flag areas needing documentation.
```

### Cross-Project Learning (monorepo or related projects)

```
When storing knowledge that would be useful across related projects, call `remember` with visibility: "scoped" and visibilityScopes matching the shared scope name, so agents in those projects can access it.
For knowledge that applies org-wide (shared conventions, cross-cutting patterns), call `remember` with visibility: "org".
When starting work that might overlap with other projects, call `recall` with broad tags to check for shared knowledge.
```

## Composition Rules

1. **Session Continuity is always first.** The `ask` call at session start is the single highest-value instruction.
2. **Keep total lines between 3-12.** More than that and instructions compete with each other for attention.
3. **Each line must be a behavioral trigger.** Format: "When {condition}, call `{tool}` with {key params}."
4. **Use the actual project name.** Replace `{project}` in all templates with the detected project name.
5. **Don't duplicate what MCP provides.** Tool schemas, parameter types, and defaults come from MCP — only specify *when* to use tools and which params matter.
6. **Visibility defaults to private.** Memories are private to the agent unless explicitly shared. Use `visibility: "scoped"` with `visibilityScopes` to share with specific agent groups. Use `visibility: "org"` only for knowledge that genuinely applies to all agents in the org.

## Anti-Patterns

- **Don't add tool documentation.** CLAUDE.md is not a reference manual — it's behavioral configuration.
- **Don't instruct on every tool.** Only include tools relevant to the selected patterns.
- **Don't add conditional logic.** Keep instructions simple and unconditional within their pattern.
- **Don't exceed 15 lines.** If the section is getting long, remove lower-priority patterns instead of cramming.
- **Don't repeat MCP defaults.** If a tool parameter has a sensible default, don't mention it.

## Complete Tier Examples

### Minimal (simple projects)

```markdown
## Headkey Memory

At session start, call `ask` with "What do I know about my-cli-tool?" to load prior context before doing any work.
When you learn something significant about my-cli-tool (architecture, conventions, gotchas), call `remember` with source: "my-cli-tool" and relevant tags.
When you make a decision or choose an approach, call `believe` with the decision and confidence 0.7-0.9.
```

### Standard (medium projects)

```markdown
## Headkey Memory

At session start, call `ask` with "What do I know about acme-web-app?" to load prior context before doing any work.
When you learn something significant about acme-web-app (architecture, conventions, gotchas), call `remember` with source: "acme-web-app" and relevant tags.
When you make a decision or choose an approach, call `believe` with the decision and confidence 0.7-0.9.
When discovering how components, services, or modules connect, call `relate` with subject, object, and relationship description.
When exploring unfamiliar parts of the codebase, call `entities` to check what is already mapped before investigating from scratch.
Before making significant changes, call `beliefs` with category: "decision" to check for prior decisions and potential conflicts.
When a previous decision is reversed or updated, call `believe` with the new decision — conflicts with prior beliefs are detected automatically.
```

### Full (complex projects)

```markdown
## Headkey Memory

At session start, call `ask` with "What do I know about mega-platform?" to load prior context before doing any work.
When you learn something significant about mega-platform (architecture, conventions, gotchas), call `remember` with source: "mega-platform" and relevant tags.
When you make a decision or choose an approach, call `believe` with the decision and confidence 0.7-0.9.
When discovering how components, services, or modules connect, call `relate` with subject, object, and relationship description.
When exploring unfamiliar parts of the codebase, call `entities` to check what is already mapped before investigating from scratch.
Before making significant changes, call `beliefs` with category: "decision" to check for prior decisions and potential conflicts.
When a previous decision is reversed or updated, call `believe` with the new decision — conflicts with prior beliefs are detected automatically.
When unsure what is already known about a topic, call `ask` with a specific question before researching from scratch.
When you discover gaps in project knowledge, call `remember` with importance: "high" and tag: "gap" to flag areas needing documentation.
When storing knowledge that would be useful across related projects, call `remember` with visibility: "scoped" and visibilityScopes matching the shared scope name.
For org-wide knowledge (shared conventions, cross-cutting patterns), call `remember` with visibility: "org".
When starting work that might overlap with other projects, call `recall` with broad tags to check for shared knowledge.
```
