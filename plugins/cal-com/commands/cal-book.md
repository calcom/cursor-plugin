---
name: cal-book
description: Find a free slot on one of your Cal.com event types and book it, confirming with you before anything is created.
---

# Book a Cal.com meeting

Use the `cal-com` MCP server and follow the `cal-com-scheduling` skill. Use details the user gave with the command (who, when, which meeting) and ask for anything missing. Never invent names, emails, IDs or time zones.

1. **Who and what.** Call `get_me` for the user's time zone. Call `get_event_types` and pick the event type that matches. If more than one could fit, list them and ask. Get the attendee's name and email from the user. Ask for the attendee's time zone, or use the user's and say so.
2. **When.** Turn the requested window ("tomorrow afternoon", "next week") into a concrete range in the user's time zone, then into UTC.
3. **Check availability.** Call `get_availability` for that event type and range. If nothing is free, say so and suggest a wider range.
4. **Offer slots.** Show up to five free slots in the user's time zone, with the zone named. Wait for the user to pick one.
5. **Confirm.** Summarise: event type and length, start and end (user's zone, plus UTC), attendee name and email, location. Ask: "Book it?" Don't continue without an explicit yes.
6. **Book.** Call `create_booking` with the event type, `start` in UTC and the attendee. Don't set `allowConflicts` or `allowBookingOutOfBounds`.
7. **Report.** Give the booked time in the user's zone, the attendee, and a link from `get_app_link`. If booking failed, say why and don't retry automatically.
