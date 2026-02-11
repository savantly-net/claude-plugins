---
description: Configure Headkey API connection (set API key and agent ID for this project or globally)
allowed-tools: [Bash, Read, Write, Edit, AskUserQuestion]
---

# Headkey Setup

Help the user configure their connection to the Headkey Memory API.

## Steps

1. Check if `HEADKEY_API_KEY` is already set:
   - Run `echo $HEADKEY_API_KEY` to check
   - If set, show the key prefix (first 10 characters only) and confirm the configuration
   - Also check if `HEADKEY_AGENT_ID` is set and display it if so

2. Ask the user what type of API key they have:
   - **Account key** (Recommended) — a single key for your Headkey account, used with a per-project agent ID. Best when you work across multiple projects.
   - **Agent key** — a key tied to a specific Headkey agent. Simpler for single-project use.

3. Ask for their Headkey API key (prefix: `cibfe_`).

4. Based on their key type:

   **For account key (recommended):**
   - Set `HEADKEY_API_KEY` **globally** in `~/.claude/settings.json` so it's available in all projects:
     ```json
     {
       "env": {
         "HEADKEY_API_KEY": "cibfe_your_account_key_here"
       }
     }
     ```
   - Preserve any existing settings in the file
   - Ask for the **agent ID** for this project — this can be the agent UUID or the friendly slug from the agent configuration
   - Set `HEADKEY_AGENT_ID` **per-project** in `.claude/settings.json` in the current project root:
     ```json
     {
       "env": {
         "HEADKEY_AGENT_ID": "my-project-agent"
       }
     }
     ```
   - Note: the agent ID is not a secret, but since the project `.claude/settings.json` may also contain other secrets, still recommend adding it to `.gitignore`
   - Check if `.gitignore` exists and whether it already includes `.claude/settings.json`; if not, offer to add it

   **For agent key:**
   - Ask the user what scope they want:
     - **Project-level** — set the key in `.claude/settings.json` in the project root
     - **Global** — set the key in `~/.claude/settings.json`
   - Create or update the chosen settings file to include:
     ```json
     {
       "env": {
         "HEADKEY_API_KEY": "cibfe_your_agent_key_here"
       }
     }
     ```
   - `HEADKEY_AGENT_ID` is not needed (the key is already tied to a specific agent)
   - If project-level, remind the user to add `.claude/settings.json` to `.gitignore`
   - Check if `.gitignore` exists and whether it already includes `.claude/settings.json`; if not, offer to add it

5. Verify the configuration works by restarting Claude Code or checking that the Headkey MCP tools are available.

6. Report success and explain that Headkey tools are now available in this session.

## Important

- Never log or display the full API key — only show the prefix (first 10 characters)
- Warn the user not to commit API keys to version control
- Account keys are preferred because they simplify key management: one key globally, agent selection per project
- If the user already has a global key and wants to override it for a specific project, project-level settings take precedence
