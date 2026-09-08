# TOOLS.md - Local Notes

Skills define *how* tools work. This file is for *your* specifics — the stuff that's unique to your setup: connected services, MCP servers, calendars, Telegram chats, SSH hosts, preferred folders, device nicknames, and anything environment-specific.

## Examples

```markdown

### Telegram

- Primary chat → Personal Telegram account
- Use for:
  - Notifications when long-running tasks finish
  - Daily summaries
  - Error alerts
  - Important reminders

### Google Calendar (MCP)

- Default calendar → Personal
- Timezone → America/Santiago
- Default event duration → 60 minutes
- Default reminder → 30 minutes before
- Use natural language whenever possible
  Example:
  "Meeting tomorrow at 3 PM"

### GitHub

- Default account → <your username>
- Preferred branch → main
- Use for:
  - Reviewing Pull Requests
  - Reading Issues
  - Repository summaries

### Google Docs

- Default folder → AI Engineering
- Use Markdown formatting whenever possible.

### SSH

- home-server → 192.168.x.x
- vps → Your production VPS

```

## Why Separate?

Skills are shared. Your environment is yours.

This file stores everything specific to your personal setup so that skills can remain portable while still knowing how to interact with your infrastructure.

For example:

- Which Telegram chat should receive notifications.
- Which Google Calendar should be used by default.
- Which GitHub account or repositories are yours.
- Which SSH aliases exist.
- Which folders in Google Drive should store generated documents.

Keeping this information here means you can update your environment without modifying your skills.

---

## Available Tools

### Telegram

Use Telegram for:

- Notifications
- Progress updates
- Daily summaries
- Task completion messages

Avoid sending unnecessary messages or duplicate notifications.

---

### Google Calendar (MCP)

Use the Google Calendar MCP whenever a task involves:

- Scheduling meetings
- Creating reminders
- Blocking focus time
- Checking availability
- Managing recurring events

Always use the default calendar unless the user specifies another.

---

### GitHub

Use GitHub to:

- Inspect repositories
- Review Pull Requests
- Read Issues
- Summarize commits
- Analyze project activity

Prefer reading existing information before asking the user.

---

### Google Docs

Use Google Docs for:

- Meeting notes
- Documentation
- Daily logs
- Technical reports
- Learning journals

Generate clean, well-structured documents.

---

### SSH

Use configured SSH hosts only when explicitly requested by the user.

Never execute destructive commands without confirmation.

---

Add whatever helps you do your job. This is your cheat sheet.

## Related

- [Agent workspace](/concepts/agent-workspace)