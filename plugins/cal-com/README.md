# Cal.com for Cursor

Use your Cal.com account from Cursor's agent. Check today's meetings, find free slots and book them, and look up event types, schedules and teams. It uses the hosted [Cal.com MCP server](https://cal.com/docs) at `https://mcp.cal.com/mcp`.

## What's included

| Component | Path | What it does |
|---|---|---|
| MCP server | `mcp.json` | Connects the `cal-com` server at `https://mcp.cal.com/mcp` (Streamable HTTP, OAuth) |
| Skill | `skills/cal-com-scheduling/` | Tells the agent how to use the Cal.com tools: look up IDs instead of guessing, check availability before booking, confirm before any change, handle time zones explicitly |
| Rule | `rules/cal-com.mdc` | Always-on short version of the same guardrails |
| Command | `commands/cal-today.md` | `/cal-today`: today's bookings in your time zone (read-only) |
| Command | `commands/cal-book.md` | `/cal-book`: find a free slot and book it, after you confirm |

## Install

- **From the Cursor Marketplace:** find **Cal.com** and click **Add to Cursor**. *(Available once the plugin is listed.)*
- **From this repo (local):** copy `plugins/cal-com` to `~/.cursor/plugins/local/cal-com`, then restart Cursor or run **Developer: Reload Window**. You can also use **Import from Disk** in Cursor's Customize view. On Enterprise plans an admin may need to turn on **Allow Local Plugin Imports**.
- **For a team:** in the Cursor dashboard, go to **Plugins & MCPs → Add Marketplace → Import from Repo** and enter `https://github.com/calcom/cursor-plugin`.

## Sign in

You don't need an API key. The first time the agent uses a Cal.com tool, Cursor opens a browser window to sign in to Cal.com and approve access. The server supports OAuth 2.1 with PKCE and dynamic client registration, so Cursor registers itself automatically.

- **Re-authenticate:** in Cursor's MCP settings (Customize), find `cal-com` and sign in again. Turning the server off and on also prompts a new sign-in if the token is no longer valid.
- **Sign out:** remove or turn off the `cal-com` server in Cursor. Signing out locally doesn't sign out Cloud Agents. For those, open **Customize** and log out on the server's **Cloud** row.

## Usage

Try:

- `/cal-today`
- `/cal-book 30 min with Jane next Tuesday afternoon`
- "What event types do I have?"
- "How do routing forms work in Cal.com?"

The agent always asks before it creates, reschedules or cancels a booking, or changes or deletes anything.

## Troubleshooting

- **401 / "Bearer token required" / "Invalid or expired access token":** you're not signed in or the token expired. Re-authenticate the `cal-com` server (see above).
- **403 on a specific action:** your Cal.com account or the granted OAuth scopes don't allow it. Organization and team actions need admin rights in Cal.com.
- **The sign-in window doesn't open:** check that your default browser can open links from Cursor. Then turn the `cal-com` server off and on in MCP settings, and look at **Output → MCP Logs** (Cmd/Ctrl+Shift+U) for the error. The desktop app listens on `http://localhost:8787/callback`, so make sure nothing else is using port 8787 and no firewall blocks localhost.
- **The server or plugin doesn't appear:** your organization may restrict MCP servers or local plugins. Ask your Cursor admin to allow the `cal-com` server (`https://mcp.cal.com/mcp`) or the plugin.

## Links

- Cal.com docs: https://cal.com/docs
- Plugin source and issues: https://github.com/calcom/cursor-plugin
