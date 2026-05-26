---
name: catch-me-up
description: Deep on-demand re-orientation on the CURRENT work thread. Use when the user returns to a session after time away ("catch me up", "where were we", "I'm back", "remind me what we're doing", "I've been away a few days") OR mid-session when the thread has gotten long and murky and they want a refresh ("I'm lost, recap this", "re-orient me", "what's the state of this", "summarize where we are"). Produces a narrative briefing — what we're doing, what problems we're solving and how we know they're problems, what's done, what's left — plus a foresight pass of questions the user hasn't asked but should. Read-only: it reconciles the live thread against durable state docs and REPORTS drift, never edits them. This is NOT session-start (that's a once-per-boot state-validation sequence); catch-me-up fires any number of times and answers "re-orient me on THIS thread, deeply."
---

# Catch Me Up

Re-orient the user on the work thread they're standing in — richly enough that someone who hasn't looked at it in days can pick the work back up, and honestly enough that they can see what's unresolved and what they're not yet asking.

The job is **synthesis from all available state**, not a reread of one source. The chat scrollback is one input among several, and after time away (or a context compaction) it may be the *least* reliable one — it can be stale or partly gone. Durable state lives in files. Read them.

## Identify the thread

Figure out what work this session is actually about before reading anything:

- Run `pwd` and check git context. In a `projects/` multi-project repo, determine which subproject the work belongs to (same ownership judgment the handoff cascade uses).
- The thread is "what this session has been doing," not "everything in the repo." If the session spans more than one thing, say so and catch up on each.

## Read the state

Gather from every source that exists; skip silently what doesn't. How you *weight* them depends on how cold the trail is: mid-session with the recent thread visible, the live conversation is primary and the docs are mostly a reconciliation check; returning after days away (or to a compacted thread where the scrollback is short or summarized), the durable docs are primary and the scrollback is a hint, not the record. The longer you've been gone, the more you trust the files over the chat. Concretely:

- **The live thread** — what you can see of the conversation.
- **Handoff doc** — the live cursor from the last stopping point. Per the handoff cascade: subproject `HANDOFF.md`, or repo-root `HANDOFF.md` for a single-project repo, or `~/.claude/handoffs/$CLAUDE_CODE_SESSION_ID.md` for ownerless work. (`echo "$CLAUDE_CODE_SESSION_ID"` to resolve.)
- **`SESSION_HANDOFF.xml`** — status, next priorities, and especially `<tested_hypotheses>`. CLOSED hypotheses are settled; they are off-limits as "open questions" in your foresight pass.
- **`status.yml`** — current-state snapshot (`summary`, `open[]`, `last_activity`).
- **Recovery guides in `.claude/guides/`** — the richest narrative session-end leaves behind (problems overcome, insights, decisions). There may be dozens; do **not** read them all. Date-sort and read the most recent few, plus any whose filename matches the thread topic:
  ```
  ls -t <project>/.claude/guides/*.md 2>/dev/null | head -5
  ```
- **`context/projects.md`** entry (assistant repo) — the project's index row and identity.
- **Git state** — both what changed while you were away and what's unfinished right now:
  - Uncommitted work in the tree — often the clearest signal of where the work was left mid-stream:
    ```
    git branch --show-current && git status --short
    ```
  - Commits since the last activity, with messages and files touched (the messages carry the *why*; `--oneline` alone loses it):
    ```
    git log --stat --since="<status.yml last_activity, or a few days ago>"
    ```
  - If a handoff or guide names a stopping-point SHA, diff it against now to see exactly what moved: `git diff --stat <sha>..HEAD`.
  - **Check git where the *artifacts* live, not just where you're standing.** The thread's output may sit outside the cwd repo — global config under `~/.claude` / `~/.agents`, a sibling project, a different repo entirely. If the work touched files elsewhere, run the same checks there with `git -C <that-dir> ...`. And some artifact locations aren't git repos at all; there, git shows nothing and you fall back to filesystem recency (`ls -lt <dir>`) to see what was last touched. Trusting cwd's git log alone will silently miss the work when the product lives elsewhere.

## Reconcile — report drift, never fix it

As you read, hold the live thread against the durable docs. They can disagree: the chat may say "we decided to drop X" while `status.yml` still lists X as open, or a guide may describe a plan the latest commits already abandoned. **Code and live state win over notes** when they conflict — a doc captures intent at a moment and goes stale fast.

Surface every meaningful discrepancy as part of the briefing. Do **not** edit the docs to resolve it — this skill is read-only. Writing state back is the job of `session-end` / `handoff`; two writers to the same doc is how state corrupts. If the drift is worth fixing, name it and let the user decide to run those.

## The briefing

Lead with the bottom line — one or two sentences a returning reader gets in ten seconds — then the detail. Cover:

- **What we're doing** — the goal and the active thread, in plain terms.
- **What problems we're solving, and how we know they're problems** — not just the task, the evidence the problem is real. If the thread asserts a problem with no evidence, say that; it's a thing the user may want to check.
- **What we've done** — decisions made, what was tried and its result, what's settled (including CLOSED hypotheses, so they don't get relitigated).
- **What's left** — open items, the live cursor, the obvious next action.
- **Drift** — any place the thread and the docs disagree, with which one you trust and why.

Cite where each load-bearing claim came from (which doc, which commit, the thread) so the user can tell recovered-from-durable-state from inferred-from-scrollback. Mark anything you're inferring rather than reading as unverified.

## Foresight: the questions not yet asked

This is the part a plain recap doesn't give, and where the skill earns its keep. After the briefing, turn a critical eye on the *work itself* — the same move `assess` makes on a prompt, pointed at the work-in-progress. Surface questions the user hasn't asked but should:

- Assumptions the work rests on that were never verified.
- Claims taken as true that are checkable but unchecked.
- Scope edges no one has drawn — what's in, what's deliberately out.
- The strongest counter-thesis to the current direction: if this approach is wrong, why would it be?
- Decisions made under earlier context that the time away is a chance to revisit.

Keep these genuinely open — questions, not recommendations dressed as questions. Don't resurface anything already CLOSED in `tested_hypotheses`. If the work is clean and you find nothing real, say so; don't manufacture doubt to fill the section.
