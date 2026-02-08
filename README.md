# Headkey Plugin for Claude Code

A Claude Code plugin that connects to the [Headkey Memory API](https://headkey.dev) — giving AI agents persistent memory, beliefs, and knowledge graph capabilities.

## What it does

This plugin adds Headkey's MCP tools directly into Claude Code, enabling:

- **Memory management** — ingest, retrieve, search, and forget memories
- **Belief tracking** — assert beliefs with confidence scores, detect conflicts
- **Knowledge graph** — create entities, define relationships, query connections
- **Sensory events** — stream events into short-term memory for async processing
- **Agent management** — create and configure AI agents with custom memory strategies

## Installation

```bash
# Add the Savantly marketplace
claude plugin marketplace add savantly/headkey-plugins

# Install the plugin
claude plugin install headkey@savantly
```

## Configuration

Set the `HEADKEY_API_KEY` environment variable with your API key:

### Option 1: Shell environment

```bash
# Add to ~/.zshrc or ~/.bashrc
export HEADKEY_API_KEY="cibfe_your_key_here"
```

### Option 2: Claude Code settings

Add to `.claude/settings.json` (project-scoped) or `~/.claude/settings.json` (global):

```json
{
  "env": {
    "HEADKEY_API_KEY": "cibfe_your_key_here"
  }
}
```

### Option 3: Setup command

Run `/headkey:setup` inside Claude Code for guided configuration.

## Usage

Once configured, Headkey MCP tools are available automatically. Claude will use them based on context, or you can reference them directly:

```
Remember that the user prefers TypeScript over JavaScript
```

```
What do we know about the authentication system?
```

```
List all agents
```

## Development

Test the plugin locally:

```bash
claude --plugin-dir ./
```

## License

MIT
