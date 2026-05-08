---
name: morning-brief
description: >
  This skill should be used when the user says "run my morning brief", "Command Center",
  "start my day", "what do I need to know today", "morning update", or "daily brief",
  or when the scheduled morning Command Center task fires. It runs a full pull from Gmail,
  Slack, Google Calendar, and optionally Granola, Grain, and Notion to produce the day's
  starting brief and to-do file. For mid-day updates after the brief has already run,
  use the check-in skill instead.
metadata:
  version: "0.3.0"
---

# Morning Brief

The morning brief answers four questions: Who needs something from you? What are you walking into today? What did you commit to yesterday? Where do you need to show up prepared this week?

Run this once at the start of the day. For subsequent updates, the check-in skill takes over.

## Configuration

Resolve the output folder:
1. Use the `COMMAND_CENTER_OUTPUT_DIR` environment variable if set.
2. Otherwise default to `~/Documents/Command Center/`.

Expand `~` to the user's home directory. Create the folder if it does not exist.

Set `BRIEF_DATE` to today's date in YYYY-MM-DD format.
Set `TODO_FILE` to `{OUTPUT_DIR}/todos-{BRIEF_DATE}.md`.
Set `LAST_RUN_FILE` to `{OUTPUT_DIR}/last-run.txt`.

Set `SEND_EMAIL` to true if the `COMMAND_CENTER_EMAIL_DIGEST` environment variable equals `true`, otherwise false.

After completing the brief, write the current UTC timestamp to `LAST_RUN_FILE`, overwriting any existing content. The check-in skill reads this to know how far back to look.

## Fetch data

Make all of these calls at once in a single message:

**Gmail (required)**
Search primary inbox: `in:inbox category:primary newer_than:1d`. Cap at 50 threads.
For each thread keep: sender, subject, first 500 characters of the latest message, timestamp, whether the user has replied.
If Gmail is not connected, show one line saying so and continue.

**Slack (required)**
Fetch DMs and @mentions from the last 24 hours. Cap at 50 messages.
For each keep: sender, channel or DM name, message text (up to 500 characters), timestamp.
Skip bot messages and automated integrations. Skip the user's own messages unless they asked a question with no reply.
If Slack is not connected, show one line saying so and continue.

**Google Calendar -- today (required)**
List every event on today's date. For each keep: title, start and end time, attendee list with RSVP status, and any URLs or text in the description.
If Calendar is not connected, show one line saying so and continue.

**Google Calendar -- this week (required)**
List events for the next 7 days. Keep only events where at least one attendee holds a title containing VP, SVP, EVP, Chief, President, Director, or C-suite -- OR appears external (different email domain from most other attendees) -- OR is the user's direct manager.
For each qualifying event keep: title, date and time, qualifying attendees with titles.

**Granola (optional)**
Fetch meeting notes from yesterday. For each keep: title, attendees, summary, action items where the user is responsible. Skip silently if not connected or no data.

**Grain (optional)**
Fetch meeting notes from yesterday. For each keep: title, attendees, summary, next steps assigned to the user. Skip silently if not connected or no data.

**Notion (optional)**
For each calendar event today with a notion.so URL in its description, fetch that page. Also search Notion using each meeting title as the query. For each result keep: page title, last edited time, key content (up to 300 characters). Skip silently if not connected or nothing relevant found.

## What to keep, what to skip

For Gmail: keep threads where a human is waiting on the user or where new information arrived that affects their work. Skip automated digests, receipts, and marketing.

For Slack: keep real conversations -- DMs, questions addressed to the user, threads where the user was mentioned and has not replied. Drop bot notifications and pipeline alerts.

## The four sections

Write each section as plain prose with short paragraphs. Direct. No em dashes. Lead with the point.

---

**Who needs something from you**

Summarize the messages and threads where the user owes someone a response or a decision. Open with a brief paragraph on the overall inbox picture. Then list each pending item:

- Who is waiting and what they need
- A short reply the user could send, written in first person (see `references/output-format.md` for reply style guidance)

Show at most 5 items, prioritized by seniority and how long they have been waiting. If the inbox is clear, say so in one sentence.

---

**Today's calendar**

For each meeting today, write a short paragraph covering what it is about, who confirmed and who has not responded or declined, and anything the user should know before walking in. If Notion returned a relevant page, summarize the key content in two to three sentences labeled as the agenda or meeting doc.

Order by start time.

---

**Commitments from yesterday**

Merge action items from Granola and Grain, removing duplicates. For each:

- The action (verb first)
- Who it goes to
- Which meeting it came from
- Due date if mentioned

If neither Granola nor Grain returned anything, leave this section out entirely.

---

**High-stakes meetings this week**

List meetings in the next 7 days with a VP or above, the user's manager, or an external executive. For each: title, date and time, who the senior attendee is and their title, one specific prep action.

---

## Save and deliver

Compile all action items from the first and third sections into a markdown checklist. Write to `TODO_FILE` using the format in `references/output-format.md`.

If `SEND_EMAIL` is true, compose an email to the user's own Gmail address. Subject: `Command Center -- {Day}, {Date}`. Body: the full brief as clean HTML. Send immediately and confirm with one line.

Output all four sections in chat as plain markdown.

Close with: `Checked: Gmail · Slack · Calendar · {any optional sources that returned data} -- {BRIEF_DATE}`

Link the to-do file: `[Open your to-dos](computer://{full absolute path to TODO_FILE})`
