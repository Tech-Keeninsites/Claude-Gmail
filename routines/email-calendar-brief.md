# Email & Calendar Brief Routine

Reads unread Gmail messages and upcoming Google Calendar events for
`ai@keeninsites.com`, then produces a segregated priority brief and pushes
a notification to the Claude app.

## How to run manually

Trigger this routine on demand in one of two ways:

1. **In a Claude Code chat/session**, send:
   > Run the email & calendar brief routine (see `routines/email-calendar-brief.md`).
2. **As a Claude Code on the web Trigger**: create a Trigger against this
   repo pointing at this file's instructions, on whatever cadence you want
   (e.g. daily at 8am). Triggers also expose a manual "Run now" action in
   the web UI, which re-executes the same steps below on demand.

## Steps

1. **Fetch unread email** via the Gmail MCP tool:
   `search_threads(query: "is:unread -in:draft", pageSize: 50, view: THREAD_VIEW_MINIMAL)`,
   paginating with `pageToken` until exhausted.
2. **Fetch calendar** via the Google Calendar MCP tool:
   `list_events(startTime: <now>, endTime: <now + 7 days>, orderBy: "startTime")`.
3. **Build the brief**:
   - One line per unread email/thread: subject, sender name, assigned priority
     (High / Medium / Low) based on content (security/site-down/client
     escalation = High; internal requests needing a reply = Medium;
     newsletters/marketing/notifications = Low).
   - Segregate into two sections:
     - **Internal** — sender address ends in `@keeninsites.com`.
     - **External** — everyone else.
   - Note stale backlog (unread items older than ~2 weeks) as an aggregated
     count rather than itemizing each one, and offer to list them in full
     if asked.
   - Summarize upcoming calendar events for the next 7 days (title, date/time,
     organizer, notable attendees).
4. **Notify**: send a concise summary through the push-notification channel
   (Claude app / email) so it reaches the user even if they're away —
   lead with anything High priority.
5. Reply in-session with the full brief text.

## Notes

- Threads matching `is:unread` may include already-read messages that share
  a thread with an unread one; only the unread message(s) drive priority.
- Keep the "Low priority" bucket terse — don't burn space itemizing obvious
  newsletters/spam.
