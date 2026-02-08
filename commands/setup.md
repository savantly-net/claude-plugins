---
description: Configure Headkey API connection (set API URL and key)
allowed-tools: [Bash, Read, Write, Edit]
---

# Headkey Setup

Help the user configure their Headkey API connection.

## Steps

1. Check if `HEADKEY_API_URL` and `HEADKEY_API_KEY` environment variables are already set:
   - Run `echo $HEADKEY_API_URL` and `echo $HEADKEY_API_KEY` to check
   - If both are set, confirm the configuration and test the connection

2. If not set, guide the user through configuration:
   - Ask for their Headkey API URL (default: `https://www.headkey.ai/api/mcp`)
   - Ask for their Headkey API key (prefix: `cibfe_`)
   - Explain they need to set these as environment variables

3. Show the user how to persist the configuration:
   ```bash
   # Add to shell profile (~/.zshrc, ~/.bashrc, etc.)
   export HEADKEY_API_URL="https://www.headkey.ai/api/mcp"
   export HEADKEY_API_KEY="cibfe_your_api_key_here"
   ```

   Or for project-scoped configuration, add to `.claude/settings.json`:
   ```json
   {
     "env": {
       "HEADKEY_API_URL": "https://www.headkey.ai/api/mcp",
       "HEADKEY_API_KEY": "cibfe_your_api_key_here"
     }
   }
   ```

4. Test the connection by making a simple API call:
   ```bash
   curl -s -H "Authorization: Bearer $HEADKEY_API_KEY" "$HEADKEY_API_URL/api/v1/agents" | head -c 500
   ```

5. Report success or troubleshoot any issues.

## Important

- Never log or display the full API key — only show the prefix
- Warn the user not to commit API keys to version control
