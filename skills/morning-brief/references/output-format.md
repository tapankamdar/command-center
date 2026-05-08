# Output Format Reference

## To-Do File

Save to `{OUTPUT_DIR}/todos-{BRIEF_DATE}.md`. If that file already exists, use `todos-{BRIEF_DATE}-{HHMM}.md`.

`OUTPUT_DIR` is resolved at runtime from the `COMMAND_CENTER_OUTPUT_DIR` environment variable, falling back to `~/Documents/Command Center/`.

```markdown
# To-Dos -- [Day], [Date]

## Replies needed
- [ ] [Name] is waiting on [topic] -- [what you need to say or decide]

## Commitments from yesterday
- [ ] [Verb-first action] for [person or team] (from: [meeting name]) [due date if known]

## Prep this week
- [ ] [Specific prep action] before [Meeting name] on [date]
```

Omit any section that has no items.

## How to write a drafted reply

The goal is a reply the user can send as-is or with minimal editing. Write in first person. Lead with the answer or decision, not setup or pleasantries.

Good: "Yes, go ahead with option B -- let me know if questions come up."
Good: "Thursday at 2pm works. Sending an invite now."
Bad: "Thanks for reaching out. I wanted to let you know that I've had a chance to review your message..."

Keep it to one or two sentences. If the situation is more complex, write the opening sentence and note what else needs to be addressed.

## Senior leader detection

Flag a meeting attendee as a senior leader if any of these apply:
- Their title contains VP, SVP, EVP, Chief, President, Director, CTO, CEO, CFO, COO, or CPO
- Their email domain is different from the majority of other attendees in the meeting (external)
- They are the user's direct manager

When email domains are ambiguous, look at the group: if most attendees share a domain and one does not, that person is external.

## Writing style

Direct. Short paragraphs. One idea per sentence. No em dashes. Lead with the point. If a section has nothing to report, say so in one line.
