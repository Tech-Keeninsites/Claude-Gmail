# Claude-Gmail

Automation for keeninsites.com inboxes.

## Email + Calendar Brief

`.claude/skills/email-brief/SKILL.md` reads unread Gmail messages and
today's calendar, produces a brief (subject / sender / priority) split
into Internal (`@keeninsites.com`) and External senders, and pushes a
notification. It runs on a schedule and can also be triggered manually
with `/email-brief`.
