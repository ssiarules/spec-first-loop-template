# Bug Log

Every defect found on this project, how it was found, and who found it. The Owner, the Architect Agent, and the Implementer Agent all write here. Append, never rewrite.

## Why this file exists

Bugs hide behind plausible error messages and silent successes. A list of what broke, and more importantly how it was caught, is the only way the catching method spreads instead of being re-invented.

## How to add an entry

Append to the table. Never edit a closed row. If something turns out wrong, add a new row that supersedes it.

Columns:

- **Sev:** `CRIT` for data loss, security, or silent money. `HIGH` for a feature dead in normal use. `MED` for wrong but recoverable. `LOW` for cosmetic.
- **Found by:** a person or an agent. Say which.
- **How found:** the method, not the symptom. This is the most valuable column in the file. Example: "Probed the live database", "drove it on a phone", "compared the commit diffstat to the stated intent".
- **Date found:** YYYY-MM-DD. Fill in when logging.
- **Date closed:** YYYY-MM-DD. Fill in when marking FIXED. Leave blank while open.
- **Status:** `FIXED <commit>` or `OPEN` or `NEEDS OWNER`.

## Open

| # | Sev | Title | Found by | How found | Date found | Date closed | Status |
|---|---|---|---|---|---|---|---|
| 1 | | Example: login button does nothing on mobile | | | | | OPEN |

Delete the example row when you log your first real bug. Keep the numbering. If a number is ever used twice, renumber the later row and note the change here.
