# Cal.com plugin for Cursor

Manage your [Cal.com](https://cal.com) scheduling from Cursor's agent. Check your meetings, find free slots, book, reschedule and cancel (always after you confirm), and look up event types, schedules and teams.

The plugin connects Cursor to the hosted Cal.com MCP server at `https://mcp.cal.com/mcp`. You sign in with OAuth on first use. No API key is needed.

## What's included

The plugin lives in [`plugins/cal-com`](plugins/cal-com):

- **MCP server** (`mcp.json`): `cal-com` → `https://mcp.cal.com/mcp`
- **Skill** `cal-com-scheduling`: how to use the Cal.com tools safely and well
- **Rule** `cal-com.mdc` (always on): never guess IDs, emails or time zones; check availability before booking; confirm before any change
- **Commands:** `/cal-today` (today's bookings in your time zone) and `/cal-book` (find a slot and book it after you confirm)

See the [plugin README](plugins/cal-com/README.md) for install, sign-in and troubleshooting.

## Install

- **Cursor Marketplace:** search for **Cal.com** and click **Add to Cursor** (once listed).
- **Local:** copy `plugins/cal-com` to `~/.cursor/plugins/local/cal-com` and reload Cursor.
- **Team marketplace:** Cursor dashboard → **Plugins & MCPs → Add Marketplace → Import from Repo** → `https://github.com/calcom/cursor-plugin`.

## Development

This repo follows the [Cursor plugin template](https://github.com/cursor/plugin-template). The marketplace manifest is `.cursor-plugin/marketplace.json`.

Validate before committing:

```bash
node scripts/validate-template.mjs
```

To add another plugin, see [`docs/add-a-plugin.md`](docs/add-a-plugin.md).

## Links

- Cal.com docs: https://cal.com/docs
- Cursor plugin docs: https://cursor.com/docs/plugins
