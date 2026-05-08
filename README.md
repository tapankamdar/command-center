# Command Center

A morning brief plugin for [Claude Cowork](https://claude.ai). Pulls from your inbox, calendar, and meeting notes to tell you exactly what needs your attention — before the day gets away from you.

---

## What it does

Command Center runs four modules each morning and delivers a structured brief in chat, plus a saved to-do file.

**Who needs something from you** — Scans Gmail and Slack for the last 24 hours. Cuts through automated noise and surfaces the messages where a real person is waiting on a response or decision, with a short drafted reply for each one.

**Today's calendar** — Pulls every meeting on today's calendar. For each one: who confirmed, who declined, who has not responded. If Notion is connected, fetches and summarizes linked agendas or meeting docs so you know what you are walking into.

**Commitments from yesterday** — Reads Granola and/or Grain meeting notes from yesterday and extracts every action item you are responsible for. These go straight into your to-do file.

**High-stakes meetings this week** — Scans the next 7 days for meetings with VPs, your manager, or external executives. Lists a specific prep action for each one so nothing sneaks up on you.

---

## Installation

### Claude Cowork (recommended)

**Option A — Install from the .plugin file:**
1. Download `command-center.plugin` from the [Releases](https://github.com/tapankamdar/command-center/releases) page
2. Open **Customize** in Cowork (bottom-left corner)
3. Go to **Browse plugins → Personal → +**
4. Select the downloaded `.plugin` file

**Option B — Install from GitHub:**
1. Open **Customize** in Cowork (bottom-left corner)
2. Go to **Browse plugins → Personal → +**
3. Select **Add marketplace from GitHub**
4. Enter: `tapankamdar/command-center`

### Claude Code (CLI)

```bash
# Step 1: Add the marketplace
claude plugin marketplace add tapankamdar/command-center

# Step 2: Install the plugin
claude plugin install command-center@command-center
```

---

## Connectors

See [CONNECTORS.md](./CONNECTORS.md) for the full reference including alternatives.

**Required** — the brief flags a missing connection and continues with what it has:

| Connector | What it powers |
|-----------|---------------|
| Gmail | Inbox signals, drafted replies, email digest delivery |
| Slack | DMs and @mentions |
| Google Calendar | Today's meeting breakdown and week-ahead senior leader scan |

**Optional** — silently skipped if not connected:

| Connector | What it powers |
|-----------|---------------|
| Granola | Action items from yesterday's meetings |
| Grain | Action items from yesterday's meetings (alternative or complement to Granola) |
| Notion | Agenda and doc summaries pulled from calendar invite links |

Connect or disconnect any of these in **Cowork Settings → Connectors**.

---

## Configuration

### Output directory

To-do files are saved to `~/Documents/Command Center/` by default. To change this:

```bash
export COMMAND_CENTER_OUTPUT_DIR="/path/to/your/folder"
```

The folder is created automatically on first run if it does not exist.

### Email digest (opt-in)

To receive your brief as an email each morning, sent to your own Gmail address:

```bash
export COMMAND_CENTER_EMAIL_DIGEST=true
```

When enabled, the brief is sent immediately after generation. Subject: `Command Center — Day, Date`.

---

## Skills

Command Center has two skills with distinct purposes. The morning brief runs once at the start of the day. The check-in can run any number of times after that.

---

### Morning Brief

Runs a full pull from all connected sources and sets up your day. Saves a fresh to-do file and records the run timestamp so the check-in knows where to pick up.

**Natural language triggers:**
- "run my morning brief"
- "Command Center"
- "start my day"
- "what do I need to know today"
- "daily brief"

**Examples:**

> *"Run my morning brief."*

Claude pulls your inbox, calendar, and meeting notes and returns a four-section brief with a to-do file saved to your output folder.

> *"Catch me up — I've been out sick for two days."*

Works the same way. If you need a longer lookback window, just say so and Claude adjusts the data fetch accordingly.

> *"Command Center, but focus on anything related to the Accenture deal."*

Runs the full brief and weights the synthesis toward messages, meetings, and commitments connected to that topic.

> *"Run my morning brief and email it to me too."*

Runs the brief, saves the to-do file, and sends it to your own Gmail address — even if `COMMAND_CENTER_EMAIL_DIGEST` is not set as an env var.

---

### Check-In

Runs at any point after the morning brief. Shows what you have already completed, surfaces only new messages and calendar changes since your last run, and hands you an updated to-do list for the rest of the day.

**Natural language triggers:**
- "check in"
- "what's new since this morning"
- "what have I missed"
- "any updates"
- "how am I doing today"
- "what's left on my list"
- "catch me up"

**Examples:**

> *"Check in."*

Claude reads your to-do file to find what you have completed, fetches only what arrived since your last run, and returns a three-section update: wins so far, new messages, and your remaining open items.

> *"What's new since this morning?"*

Same as above. Scoped to only what has arrived since the morning brief ran.

> *"How am I doing — and what still needs my attention?"*

Shows completed items first, then the updated open list with any new items appended.

> *"Check in and email me the update."*

Delivers the check-in brief to your own Gmail inbox in addition to showing it in chat.

---

## Scheduling

To run the morning brief automatically each day:

> *"Schedule my morning brief to run every day at 8am"*

The schedule skill sets up a recurring task that calls `morning-brief` and delivers the brief inline. The check-in skill does not need to be scheduled — run it on demand whenever you want a mid-day pulse check.

You can adjust the time or pause the schedule any time.

---

## Customization

**What counts as a senior leader:** VP, SVP, EVP, Chief, President, Director, CTO, CEO, CFO, COO, CPO — or any attendee from outside your organization's email domain, or your direct manager. Edit these criteria in `skills/morning-brief/references/output-format.md`.

**To-do file format and reply style:** Also in `skills/morning-brief/references/output-format.md`. Adjust the checklist template, drafted reply tone, or section names.

**Adding more connectors:** Edit the `morning-brief` skill body to add new data sources. See [CONNECTORS.md](./CONNECTORS.md) for the list of alternatives not yet supported.

---

## License

MIT — see [LICENSE](./LICENSE).

---

## About the author

Tapan Kamdar is a product leader with 15+ years at Meta, Mozilla, GoDaddy, eBay, and Yahoo. He writes the *Building Blocks* newsletter at [linkedin.com/newsletters/7167660111300157440](https://www.linkedin.com/newsletters/7167660111300157440/) and is the author of *Level Up: From Good Product Manager to Great Product Manager* ([a.co/d/0dxmUyJJ](https://a.co/d/0dxmUyJJ)).
