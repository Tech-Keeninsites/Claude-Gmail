---
name: email-brief
description: Read unread Gmail messages and today's Google Calendar, then produce a prioritized brief (subject, sender, priority) split into Internal (@keeninsites.com) and External senders, and push a summary notification. Use when the user asks for their email/inbox brief, daily digest, or runs /email-brief. Works both as a scheduled routine and as a manually invoked command.
---

# Email + Calendar Brief

Produces a same-day brief of unread email and calendar events, split by
sender domain, and delivers it as a push notification. Designed to be run
either automatically on a schedule or manually via `/email-brief`.

## Steps

1. **Read unread email.**
   Call `mcp__Gmail__search_threads` with `query: "is:unread in:inbox"`,
   `pageSize: 50`, `view: THREAD_VIEW_MINIMAL`. Each returned thread may
   contain multiple messages (sent + unread); only consider messages whose
   `labelIds` include `UNREAD` when building the brief.

2. **Read today's calendar.**
   Call `mcp__Google-Calendar__list_events` with `startTime`/`endTime`
   spanning local midnight-to-midnight for today (use the user's calendar
   timezone, e.g. `America/New_York` for tech@keeninsites.com).

3. **Classify each unread message.**
   - **Internal**: sender address ends in `@keeninsites.com`.
   - **External**: everything else (including affiliated shops like
     `wolfpackits.com`, `fatguymedia.com`, and client/vendor domains).

4. **Assign a priority per message**, using judgment over these signals:
   - **High** — a client/vendor is blocked, waiting on a deadline, or
     escalating (e.g. "why can't I view it live", "when will this be
     complete", security/site-down issues, service/domain expiration
     notices needing action).
   - **Medium** — active work requests, pending replies, or meeting
     invitations for today that need a response/attention.
   - **Low** — FYI/confirmation threads, newsletters, digests, resolved
     threads, or old informational forwards.

5. **Build the brief** with these sections:
   - **Today's Calendar** — time, title, organizer.
   - **Internal** (`*.keeninsites.com` senders) — Subject / Sender name / Priority.
   - **External** (all other senders) — Subject / Sender name / Priority.
   Sort each section High → Medium → Low.

6. **Notify.** Send the brief via the `PushNotification` tool (or reply
   directly in chat if invoked manually and a live user is present) so it
   reaches the user's phone/Claude App. Lead with the single most
   important item (e.g. a High-priority external escalation), then give
   inbox counts (total unread / internal / external) and the day's meeting
   count.

## Manual run

Invoke this skill directly at any time with `/email-brief` — it runs the
same steps above regardless of whether it was triggered by the schedule or
by the user.

## Notes

- Do not mark messages as read or archive them; this is a read-only briefing.
- If Gmail or Calendar access fails (auth error, no scope), say so plainly
  in the notification instead of silently producing an empty brief.
