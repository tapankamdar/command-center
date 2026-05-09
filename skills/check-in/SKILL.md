---
name: check-in
description: >
  This skill should be used when the user says "check in", "what's new since this morning",
  "what have I missed", "catch me up", "any updates", "how am I doing today",
  "what's left on my list", or any time they want a mid-day or afternoon pulse check
  after the morning brief has already run. It shows what the user has accomplished
  since the morning run, surfaces only new activity since the last run, and delivers
  an updated to-do list for the rest of the day.
metadata:
  version: "0.4.0"
---

# Check-In

The check-in is not a repeat of the morning brief. It answers three questions: What have you already done? What is new since you last checked? What still needs your attention?

It reads your existing to-do file to find completed and in-progress items, fetches only what has arrived since the last run, and hands you back an updated list to work from for the rest of the day.

## Configuration

Resolve the output folder using the same logic as the morning-brief skill:
1. Use the `COMMAND_CENTER_OUTPUT_DIR` environment variable if set.
2. Otherwise default to `~/Documents/Command Center/`.

Set `BRIEF_DATE` to today's date in YYYY-MM-DD format.
Set `TODO_FILE` to `{OUTPUT_DIR}/todos-{BRIEF_DATE}.md`.
Set `LAST_RUN_FILE` to `{OUTPUT_DIR}/last-run.txt`.

Set `SEND_EMAIL` to true if the `COMMAND_CENTER_EMAIL_DIGEST` environment variable equals `true`, otherwise false.

**Determine the lookback window.**
Read `LAST_RUN_FILE`. If it contains a valid UTC timestamp, set `SINCE` to that time. If the file does not exist or cannot be read, fall back to 4 hours ago. This window scopes all data fetches to only what is genuinely new.

After the check-in completes, write the current UTC timestamp to `LAST_RUN_FILE`.

**If `TODO_FILE` does not exist**, the morning brief has not run yet today. Let the user know and offer to run the morning-brief skill instead. Do not proceed with the check-in.

## Accepting progress updates

The user may tell you about progress they have made during the check-in. Listen for any of these patterns:

- "I made progress on X"
- "I started working on X"
- "I'm mid-way through X"
- "I finished X"
- "I haven't started X yet"
- "I can drop X, it's no longer relevant"

When the user reports progress on a task, update its state in `TODO_FILE`:

| What the user says | New state |
|--------------------|-----------|
| Made progress, started, mid-way through | `- [>]` in progress |
| Finished, completed, done | `- [x]` done |
| No longer relevant, dropping, skip it | Remove the item |
| Haven't started, still pending | `- [ ]` open (no change) |

Match the task by keyword or description — do not require an exact string match. If the task is ambiguous, show the user what you found and confirm before updating.

After updating states, rewrite `TODO_FILE` with the current state of all items and confirm the changes with one short sentence.

## Fetch data

Make all of these calls at once in a single message:

**Existing to-do file**
Read `TODO_FILE`. Separate items into:
- `DONE_ITEMS`: lines matching `- [x]` -- things the user has completed
- `INPROGRESS_ITEMS`: lines matching `- [>]` -- things actively being worked on
- `OPEN_ITEMS`: lines matching `- [ ]` -- things still pending

**Gmail**
Search `in:inbox category:primary` for messages newer than `SINCE`. Cap at 30 threads. For each keep: sender, subject, first 500 characters, timestamp, whether the user has replied. If Gmail is not connected, note it and continue.

**Slack**
Fetch DMs and @mentions newer than `SINCE`. Cap at 30 messages. For each keep: sender, channel or DM name, message text (up to 500 characters), timestamp. Apply the same bot and noise filters as the morning brief. If Slack is not connected, note it and continue.

**Google Calendar -- today**
Re-fetch today's events. Focus on changes since `SINCE`: new invites added, RSVP status changes (new declines or acceptances), meeting time changes. If nothing has changed, return nothing from this source.

## The three check-in sections

Write each section as plain prose with short paragraphs. Direct and warm, but not cheerleading. Lead with the facts.

---

**What you have knocked out**

Open with what the user has already done. This is the wins section and it comes first -- before the to-do list, before the new items.

List each item from `DONE_ITEMS`. If there are `INPROGRESS_ITEMS`, list those separately with a brief note that they are underway. Then write one direct sentence acknowledging the progress and stating how many open items remain.

Good examples:
- "Four things done since this morning. Two more in progress. Three still to start."
- "You have cleared the inbox action items and you are mid-way through the Q3 forecast. Two items still open."

Not this:
- "Amazing work! You are crushing it!"

If `DONE_ITEMS` is empty and `INPROGRESS_ITEMS` is empty, skip this section entirely. Do not manufacture praise for nothing completed.

---

**New since your last check**

Show only what has arrived since `SINCE`. Two subsections:

*Inbox* -- messages in Gmail and Slack where someone needs a response. Same format as the morning brief: who is waiting, what they need, a short drafted reply. Cap at 5 items. If there is nothing new that requires the user's attention, write: "Inbox quiet since your last check."

*Calendar* -- any RSVP changes, new meetings added, or schedule changes since this morning. For each: what changed, who is involved, whether it requires action from the user. If nothing has changed, omit this subsection.

---

**What still needs your attention**

Show items in this order:
1. `INPROGRESS_ITEMS` marked with `[>]` -- already underway, highest priority to finish
2. `OPEN_ITEMS` -- not yet started
3. Any new action items surfaced in the inbox section above

Format as a markdown checklist using the three-state format (`- [>]`, `- [ ]`). Keep it short -- if the list is long, note how many items there are and surface only the top ones by priority.

---

## Save and deliver

Rewrite `TODO_FILE` with the full updated state:
- `DONE_ITEMS` marked `- [x]` at the top
- `INPROGRESS_ITEMS` marked `- [>]` next
- `OPEN_ITEMS` marked `- [ ]` last, including any new ones

Preserve the section headers from the original file format. In-progress and open items will be picked up by the morning brief the following day.

If `SEND_EMAIL` is true, send the check-in brief to the user's own Gmail address. Subject: `Command Center Check-In -- {Day}, {Date} {HH:MM}`. Body: clean HTML. Send immediately and confirm with one line.

Output all three sections in chat as plain markdown.

Close with: `Updated {HH:MM} · {n} new messages · {n} calendar changes · {BRIEF_DATE}`

Link the updated to-do file.
