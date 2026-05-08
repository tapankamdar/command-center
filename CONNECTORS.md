# Connectors

Command Center works with a mix of required and optional connectors. Required connectors must be set up before running the brief. Optional ones are loaded if available and silently skipped if not — the brief always runs regardless.

## Required Connectors

| Connector | What it powers |
|-----------|---------------|
| Gmail | Inbox signals, action item detection, email digest delivery |
| Slack | DMs and @mentions for the last 24 hours |
| Google Calendar | Today's meeting breakdown and week-ahead senior leader scan |

If a required connector is missing, the brief surfaces a single error line for that source and continues with what it has.

## Optional Connectors

| Connector | What it powers | Fallback if missing |
|-----------|---------------|---------------------|
| Granola | Yesterday's meeting action items | Section 3 is omitted silently |
| Grain | Yesterday's meeting action items (alternative or complement to Granola) | Merged with Granola if both connected; omitted if neither |
| Notion | Meeting doc and agenda summaries pulled from invite links | Falls back to summarizing invite description text |

## Adding Connectors

Connect or disconnect any of the above in **Cowork Settings -> Connectors**.

## Alternatives

If you use a different tool in one of these categories, the plugin will not automatically work with it. The connectors listed above are the specific integrations this plugin was built for. Support for alternatives can be added by editing the `morning-brief` skill.

| Category | Currently supported | Common alternatives |
|----------|--------------------|--------------------|
| Email | Gmail | Outlook (not yet supported) |
| Chat | Slack | Microsoft Teams (not yet supported) |
| Calendar | Google Calendar | Outlook Calendar (not yet supported) |
| Meeting notes | Granola, Grain | Fireflies, Otter.ai (not yet supported) |
| Docs | Notion | Confluence, Google Drive (not yet supported) |
