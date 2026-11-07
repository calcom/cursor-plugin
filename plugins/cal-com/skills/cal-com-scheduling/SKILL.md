---
name: cal-com-scheduling
description: Use the Cal.com MCP tools to read and manage the user's scheduling (bookings, availability, event types, schedules, teams). Use when the user asks about their meetings, calendar, availability, booking links, or how a Cal.com feature works.
---

# Cal.com scheduling

The `cal-com` MCP server (`https://mcp.cal.com/mcp`) acts as the signed-in Cal.com user. Every write goes to their real calendar and can email real attendees, so be careful.

## When to use

- "What's on my calendar today / this week?"
- "Find a time with X and book it", "reschedule", "cancel"
- "What event types do I have?", "change my working hours"
- "How does X work in Cal.com?" (round robin, routing forms, seats, ...)

## Core rules

1. **Discover, never guess.** Never invent IDs, booking UIDs, emails, usernames, slugs or time zones. Look them up with the read tools, or ask the user.
2. **Check availability before booking.** Call `get_availability` before `create_booking` or `reschedule_booking`, and only offer slots it returned.
3. **Confirm before changing anything.** Before you create, reschedule, cancel, confirm or decline a booking, or create, update or delete anything else, show the user exactly what will happen (who, what, when, in their time zone) and wait for an explicit yes.
4. **Be explicit about time zones.** Get the user's time zone from `get_me`. The API takes UTC ISO 8601 (`2026-11-09T14:00:00Z`). Convert carefully, and show times to the user in their own time zone with the zone named, e.g. "Mon 9 Nov, 15:00 (Europe/Berlin)".
5. **Docs questions go to `search_docs`.** For "how does X work" or "how do I set up X", call `search_docs` rather than answering from memory.
6. **Link things.** When you mention a booking, event type, schedule or settings page, use `get_app_link` to give the user a link they can open.

## Discovery tools (read-only)

| Need | Tool |
|---|---|
| User's id, username, email, time zone | `get_me` |
| Event types (ids, slugs, lengths) | `get_event_types`, `get_event_type`, `get_event_type_settings` |
| Bookings | `get_bookings` (filter by status, `afterStart` / `beforeEnd`, attendee), `get_booking` |
| Attendees of a booking | `get_booking_attendees`, `get_booking_attendee` |
| Open slots | `get_availability` |
| Busy times on connected calendars | `get_connected_calendars`, then `get_busy_times` |
| Working hours | `get_schedules`, `get_schedule`, `get_default_schedule` |
| Teams | `get_my_teams`, `get_team_memberships`, `get_org_teams` |
| Conferencing apps | `get_conferencing_apps` |
| Docs | `search_docs` |
| Links into the web app | `get_app_link` |

## Write tools (confirm first)

- Bookings: `create_booking`, `reschedule_booking`, `cancel_booking`, `confirm_booking`, `add_booking_attendee`, `mark_booking_absent`
- Event types: `create_event_type`, `update_event_type`, `delete_event_type`, `duplicate_event_type`
- Schedules: `create_schedule`, `update_schedule`, `delete_schedule`
- Profile: `update_me`

Anything named `delete_*`, `cancel_*` or `unassign_*` is destructive. Name the exact item and ask again even if the user already asked in general terms.

## Workflows

### List bookings for a day

1. `get_me` → `timeZone`.
2. Work out the day's start and end in that zone, then convert both to UTC.
3. `get_bookings` with `afterStart` / `beforeEnd` set to those UTC bounds (and `status: upcoming` for future meetings).
4. Show each booking's time in the user's zone, title, attendees and status. Say so if there are none.

### Book a meeting

1. `get_me` for the time zone. `get_event_types` to pick the event type. If more than one fits, ask.
2. Ask for the attendee's name and email if you don't have them. Never make them up. Ask for the attendee's time zone, or use the user's and say so.
3. `get_availability` for the event type over the requested range.
4. Offer a few slots in the user's time zone. Wait for a choice.
5. Summarise: event type, start and end (user's zone and UTC), attendee name and email, location. Ask "Book it?"
6. On an explicit yes, call `create_booking` with `start` in UTC. Never set `allowConflicts` or `allowBookingOutOfBounds` unless the user asks for it explicitly.
7. Report the result with the booking link from `get_app_link`.

### Reschedule or cancel

1. Find the booking with `get_bookings` / `get_booking` and confirm it is the right one (title, time, attendees).
2. Reschedule: `get_availability` with `bookingUidToReschedule`, offer slots, confirm, then `reschedule_booking`.
3. Cancel: confirm and ask for an optional reason, then `cancel_booking`.

## Anything else

For an API v2 operation without a dedicated tool, use `find_api_operation` → `describe_api_operation` → `call_api_operation`, with the same confirmation rules. If nothing fits, say plainly that the Cal.com integration doesn't support it yet. Don't call an unrelated tool hoping it works.

If a tool returns 401 or 403, the sign-in expired or the OAuth scopes don't cover the action. Tell the user to re-authenticate the `cal-com` server in Cursor's MCP settings. Don't retry in a loop.
