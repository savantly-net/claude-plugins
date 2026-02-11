---
description: Generate CLAUDE.md memory instructions for the current project — teaches Claude when and how to use Headkey tools
allowed-tools: [Read, Write, Edit, Glob, Grep, AskUserQuestion, mcp__headkey-local__ask, mcp__headkey-local__beliefs, mcp__headkey-local__entities, mcp__headkey-local__recall, mcp__headkey-local__remember]
---

# Generate Headkey Memory Instructions for CLAUDE.md

Analyze the current project and generate a `## Headkey Memory` section for CLAUDE.md that teaches Claude when and how to use Headkey tools in this project. The user may provide a focus area in `$ARGUMENTS` (e.g., "decisions", "architecture", "minimal").

## Phase 1: Analyze Project

Detect the project context:

1. **Project name** — resolve in priority order:
   - `package.json` → `name`
   - `Cargo.toml` → `[package] name`
   - `pyproject.toml` → `[project] name`
   - `go.mod` → module path
   - Git remote → repo name
   - Fall back to current directory name

2. **Stack and complexity** — scan for signals:
   - Count top-level directories, source files, config files
   - Check for monorepo indicators (workspaces, lerna, nx, turborepo)
   - Check for multiple services (docker-compose, k8s manifests, multiple package.json)
   - Detect frameworks and languages from manifests and file extensions
   - **Simple**: single-purpose project, <5 top-level source dirs, one language
   - **Medium**: multiple modules, 1-2 languages, some infrastructure
   - **Complex**: monorepo, microservices, 3+ languages, CI/CD pipelines

3. **Existing CLAUDE.md** — check if one exists at project root:
   - If it has a `## Headkey Memory` section already, note this for Phase 4 (update instead of append)
   - Read the full file to understand existing instructions and avoid conflicts

Report findings to the user: project name, detected stack, complexity tier.

## Phase 2: Check Existing Headkey Knowledge

Call these tools to see if Headkey already has context about this project:

1. `ask` — "What do I know about {project name}?" with include: ["memories", "beliefs", "entities"]
2. `beliefs` — check for any existing beliefs (limit 5)
3. `entities` — list known entities

If prior knowledge exists, tell the user what was found. This context may inform which patterns are most useful.

## Phase 3: Select Patterns

Based on complexity tier and any user-provided `$ARGUMENTS` focus:

**Minimal** (simple projects):
- Session Continuity only

**Standard** (medium projects):
- Session Continuity
- Architecture Graph
- Decision Tracking

**Full** (complex projects):
- Session Continuity
- Architecture Graph
- Decision Tracking
- Knowledge Gap Detection
- Cross-Project Learning (if monorepo or related projects detected)

If `$ARGUMENTS` specifies a focus area, adjust selection:
- "minimal" / "simple" → force Minimal tier
- "full" / "everything" → force Full tier
- "decisions" → include Decision Tracking regardless of tier
- "architecture" / "graph" → include Architecture Graph regardless of tier

Tell the user which patterns were selected and why.

## Phase 4: Propose Snippet

Read the skill template file at `skills/claude-md/operations/templates.md` relative to the plugin directory. Use it to compose the appropriate snippet for the selected patterns.

Compose the `## Headkey Memory` section using the selected pattern templates, substituting `{project}` with the detected project name.

Present the exact content in a fenced code block and explain:
- Where it will be inserted (new CLAUDE.md, or appended/updated in existing one)
- Which patterns are included and what they do

Ask the user for confirmation before applying. Use AskUserQuestion with options:
- "Apply as shown"
- "Adjust patterns" (go back to Phase 3)
- "Edit manually" (write to CLAUDE.md and let user edit)

## Phase 5: Apply

Based on confirmation:

1. **No existing CLAUDE.md** — create one with a minimal header and the Headkey section:
   ```
   # CLAUDE.md

   ## Headkey Memory
   {generated content}
   ```

2. **Existing CLAUDE.md without Headkey section** — append the section at the end

3. **Existing CLAUDE.md with Headkey section** — replace the existing `## Headkey Memory` section (from the heading to the next `##` heading or end of file)

After writing:
- Call `remember` to store that this project was configured with Headkey memory instructions, including the tier and patterns used. Use tags: ["claude-md", "configuration", "{project-name}"].
- Report success and remind the user that Claude will now use Headkey tools according to these instructions in future sessions.
