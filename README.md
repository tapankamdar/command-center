# Command Center

A morning brief plugin for Claude Cowork. Pulls from your inbox, calendar, and meeting notes to tell you exactly what needs your attention — before the day gets away from you.

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

## Skills & Commands

Two skills with distinct purposes. Run the morning brief once at the start of your day. Use the check-in any number of times after that.

---

### 1. Morning Brief (`/morning-brief`)

Pulls from Gmail, Slack, Google Calendar, and your meeting notes to set up your day. Returns four sections: who needs something from you, what you are walking into today, what you committed to yesterday, and which meetings this week need prep. Saves a to-do file you can open and check off.

**Natural language triggers:**
- "run my morning brief"
- "Command Center"
- "start my day"
- "what do I need to know today"
- "daily brief"
- "catch me up — I've been out"

**Examples:**

> *"Run my morning brief."*

Pulls your inbox, calendar, and meeting notes. Returns a four-section brief with a to-do file saved to your output folder.

> *"Catch me up — I've been out sick for two days."*

Works the same way. Say how far back you need to look and it adjusts the fetch window accordingly.

> *"Command Center, but focus on anything related to the Accenture deal."*

Runs the full brief and weights the output toward messages, meetings, and commitments connected to that topic.

> *"Run my morning brief and email it to me too."*

Runs the brief, saves the to-do file, and sends it to your Gmail address — even if `COMMAND_CENTER_EMAIL_DIGEST` is not set.

> `/morning-brief`

Runs the full brief immediately.

> `/morning-brief focus on anything urgent from the VP of Sales`

Runs the full brief with extra weight on messages from that person.

---

### 2. Check-In (`/check-in`)

Runs at any point after the morning brief. Shows what you have already knocked out, surfaces only new messages and calendar changes since your last run, and hands you an updated to-do list for the rest of the day. Does not repeat the full morning pull.

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

Reads your to-do file to find what you have completed, fetches only what arrived since your last run, and returns three sections: wins so far, new messages, and your remaining open items.

> *"What's new since this morning?"*

Same as above. Scoped to only what has arrived since the morning brief ran.

> *"How am I doing — and what still needs my attention?"*

Shows completed items first, then the updated open list with any new items appended.

> *"Check in and email me the update."*

Delivers the check-in brief to your Gmail inbox in addition to showing it in chat.

> `/check-in`

Runs the check-in immediately.

> `/check-in any new messages from the engineering team?`

Runs the check-in with extra attention to messages from that group.

---

## Scheduling

To run the morning brief automatically each day, say:

> *"Schedule my morning brief to run every day at 8am."*

Cowork's schedule skill sets up a recurring task that calls `/morning-brief` at that time and delivers the brief inline. You will see it in chat each morning without having to ask.

To change or pause the schedule:

> *"Change my morning brief to 7:30am."*

> *"Pause my morning brief schedule for this week."*

> *"Cancel my morning brief schedule."*

The check-in does not need to be scheduled. Run it on demand throughout the day whenever you want a pulse check.

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
