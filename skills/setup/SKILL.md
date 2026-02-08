---
name: setup
description: This skill should be used when the user asks to "set up headkey", "configure headkey", "connect to headkey", mentions "HEADKEY_API_KEY", "HEADKEY_API_URL", or discusses headkey configuration and authentication setup.
version: 1.0.0
---

# Headkey Setup Skill

This skill helps users configure their connection to the Headkey Memory API.

## When This Skill Applies

- User wants to set up or configure the Headkey plugin
- User needs help with API key configuration
- User is troubleshooting connection issues
- User mentions `HEADKEY_API_KEY` or `HEADKEY_API_URL`

## Required Environment Variables

| Variable | Description | Default |
|---|---|---|
| `HEADKEY_API_URL` | Base URL of the Headkey API | `https://www.headkey.ai/api/mcp` |
| `HEADKEY_API_KEY` | API key for authentication (prefix: `cibfe_`) | _(required)_ |

## Configuration Methods

### Method 1: Shell Environment (Recommended)

Add to `~/.zshrc` or `~/.bashrc`:

```bash
export HEADKEY_API_URL="https://www.headkey.ai/api/mcp"
export HEADKEY_API_KEY="cibfe_your_key_here"
```

### Method 2: Claude Code Project Settings

Add to `.claude/settings.json` in the project root:

```json
{
  "env": {
    "HEADKEY_API_URL": "https://www.headkey.ai/api/mcp",
    "HEADKEY_API_KEY": "cibfe_your_key_here"
  }
}
```

### Method 3: Claude Code User Settings

Add to `~/.claude/settings.json` for global access:

```json
{
  "env": {
    "HEADKEY_API_URL": "https://www.headkey.ai/api/mcp",
    "HEADKEY_API_KEY": "cibfe_your_key_here"
  }
}
```

## Verification

After configuration, verify the connection works by using any Headkey MCP tool (e.g., listing agents). If the connection fails, check:

1. The API URL is correct and accessible
2. The API key is valid and not expired
3. Network connectivity to the Headkey API
