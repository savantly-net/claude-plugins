# Savantly Claude Plugins

A marketplace of Claude Code plugins by [Savantly](https://github.com/savantly-net).

## Installation

```bash
# Add the marketplace (one time)
claude plugin marketplace add savantly-net/claude-plugins
```

Then install any plugin:

```bash
claude plugin install <plugin-name>@savantly
```

## Available Plugins

### Headkey

Memory layer for AI agents — store, organize, retrieve, and manage memories and beliefs via the [Headkey](https://headkey.ai) API.

```bash
claude plugin install headkey@savantly
```

**Features:**

- **Memory management** — ingest, retrieve, search, and forget memories
- **Belief tracking** — assert beliefs with confidence scores, detect conflicts
- **Knowledge graph** — create entities, define relationships, query connections
- **Sensory events** — stream events into short-term memory for async processing
- **Agent management** — create and configure AI agents with custom memory strategies

**Configuration:**

Set the `HEADKEY_API_KEY` environment variable with your API key:

```bash
# Shell environment
export HEADKEY_API_KEY="cibfe_your_key_here"
```

Or add to `.claude/settings.json` (project) or `~/.claude/settings.json` (global):

```json
{
  "env": {
    "HEADKEY_API_KEY": "cibfe_your_key_here"
  }
}
```

Or run `/headkey:setup` inside Claude Code for guided configuration.

## Development

Test a plugin locally:

```bash
claude --plugin-dir ./path-to-plugin
```

## License

MIT
