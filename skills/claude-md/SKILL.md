---
name: claude-md
description: This skill should be used when the user mentions "CLAUDE.md" together with headkey or memory, asks to "add memory instructions", "make Claude remember things between sessions", "configure persistent memory", "set up headkey in CLAUDE.md", or wants to generate instructions that teach Claude when to use Headkey tools.
version: 1.0.0
---

# Headkey CLAUDE.md Skill

This skill helps users generate and manage CLAUDE.md instructions that teach Claude when and how to use Headkey memory tools.

## When This Skill Applies

- User wants to add Headkey memory instructions to CLAUDE.md
- User asks how to make Claude remember things between sessions
- User mentions configuring persistent memory for a project
- User wants to customize which Headkey tools Claude uses automatically

## Quick Tool Reference

| Tool | When to Use | Key Params |
|------|-------------|------------|
| `ask` | Session start — load prior context | `question`, `include` |
| `remember` | Learned something significant | `content`, `tags`, `source`, `importance`, `visibility`, `visibilityScopes` |
| `recall` | Search for specific memories | `query`, `tags`, `limit` |
| `forget` | Remove outdated/wrong memories | `memoryIds`, `strategy` |
| `believe` | Recording a decision or assertion | `statement`, `confidence`, `category` |
| `beliefs` | Check existing decisions for conflicts | `query`, `category`, `status` |
| `relate` | Discovered how components connect | `subject`, `object`, `relationship` |
| `entities` | List known components/concepts | `name`, `type`, `withBeliefs` |

## Memory Patterns Overview

Five composable patterns, selected based on project complexity:

1. **Session Continuity** — always included. Call `ask` at session start, `remember` when learning.
2. **Architecture Graph** — for projects with 3+ services/modules. Map component relationships with `relate`.
3. **Decision Tracking** — for active development. Track decisions with `believe`, check conflicts before changes.
4. **Knowledge Gap Detection** — for complex projects. Use `ask` to check knowledge density before researching from scratch.
5. **Cross-Project Learning** — for monorepos or related projects. Use `visibility: "org"` to share knowledge across projects.

## Visibility Scopes

Three visibility levels control how memories are shared:

- **`private`** (default) — only the creating agent can access the memory.
- **`scoped`** — agents whose `readScopes` match the memory's `visibilityScopes` can access it. Use this to share knowledge between specific agent groups (e.g., agents working on related projects). Falls back to the agent's `defaultWriteScope` if no scopes are provided.
- **`org`** — all agents in the organization can access it. Use for genuinely org-wide knowledge (shared conventions, cross-cutting patterns, common APIs).

## Setup Command

Users can run `/headkey:claude-md` for a guided workflow that analyzes the project, selects appropriate patterns, and generates the CLAUDE.md section.

## Templates

See `operations/templates.md` for the full composable template blocks, composition rules, anti-patterns, and complete tier examples.

## Analysis Reference

See `operations/analyze.md` for project complexity signals, tech stack mapping, and how to handle existing CLAUDE.md files.
