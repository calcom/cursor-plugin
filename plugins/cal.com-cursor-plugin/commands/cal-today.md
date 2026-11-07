---
name: cal-today
description: Show today's Cal.com bookings in your time zone.
---

# Today's Cal.com bookings

Use the `cal-com` MCP server. This command is read-only. Don't create, change or cancel anything.

1. Call `get_me` and read `timeZone`. If it's missing, ask the user for their time zone. Don't guess.
2. Work out today's date in that time zone, and the start (00:00) and end (23:59:59) of that day. Convert both to UTC ISO 8601.
3. Call `get_bookings` with `afterStart` and `beforeEnd` set to those UTC bounds. Use `take` and `skip` to page if needed.
4. Sort by start time and show a short list. For each booking: start and end in the user's time zone, title, attendees (names), status (flag unconfirmed or cancelled ones) and location or meeting link if there is one.
5. Put the date and time zone in the heading, e.g. "Monday 9 November (Europe/Berlin)". If there are no bookings, say so in one line.
6. Optionally end with a link to the bookings page from `get_app_link`.
