# catch-me-up

Come back to a Claude Code session after a few days — or just a long, winding one —
and `/catch-me-up` re-orients you on the thread. It reads the durable state your
project leaves behind, reconciles it against the conversation, and hands you a
briefing: what you're doing, the problems you're solving (and how you know they're
problems), what's done, what's left — plus the questions you haven't asked yet but
should.

It's a Claude Code plugin from Family IT Guy.

## Why it's different from rereading the chat

After time away — or after a context compaction — the chat scrollback is the *least*
reliable record. It can be stale or partly gone. The state that survives lives in
files. `/catch-me-up` reads those first and treats the scrollback as a hint, not the
source of truth.

## Heads up: it works best with a state-capture habit

This plugin was built around the way I run my own sessions, where each project keeps
durable state on disk — a current-state snapshot, a handoff doc, session recovery
guides, a project index. `/catch-me-up` reads all of that when it's there.

If you don't keep files like those, it still works: it falls back to your conversation
thread plus your git history (recent commits, what's uncommitted, what changed since
you were last here). That's genuinely useful — but the richest catch-ups come when
there's durable state for it to read. If you find yourself wanting more, the move is
to start leaving a `HANDOFF.md` or a short status note at the end of a work session;
the plugin will pick it up next time.

## When to reach for it

- You're returning to a session or project after time away and need to get your
  bearings before you touch anything.
- A session has gotten long and murky and you want a clean read on where things stand.
- You want a second pass on the work itself — the assumptions and open questions you've
  stopped seeing because you're too close to it.

## Install

```
/plugin marketplace add Family-IT-Guy/plugins
/plugin install catch-me-up@familyitguy
```

To update later: `/plugin marketplace update familyitguy` then
`/plugin install catch-me-up@familyitguy`. (Or turn on auto-update in the `/plugin`
menu under Marketplaces.)
