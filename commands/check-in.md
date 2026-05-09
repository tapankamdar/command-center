---
description: Mid-day pulse check — wins so far, new messages, and your updated to-do list
argument-hint: [optional progress updates, e.g. "I finished X and made progress on Y"]
---

Load the `check-in` skill and follow its workflow.

If the user provided arguments, treat $ARGUMENTS as progress updates the user is reporting. Parse them for task state changes (finished, in progress, dropping) before running the inbox and calendar fetch. Apply those state updates to the to-do file as part of the check-in.

If no arguments were provided, run the check-in immediately with no task state changes.
