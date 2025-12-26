# Via Rail/Ferry Workflows

**Instance:** Mac
**Status:** All Active
**Category:** Data

## Overview

Set of workflows that automate Via Rail fare checking and calendar scanning via a local API.

---

## via check for new dates

**ID:** fteteEsP5V6khd2X

### Trigger
- Daily at 7:00 AM

### Action
Calls local API to scan calendar for new dates (default direction).

```json
{ "command": "calendar", "args": ["--from-latest"] }
```

---

## via Get new dates Reverse

**ID:** O8fUOAjjFSTqh808

### Trigger
- Daily at 7:15 AM

### Action
Calls local API to scan calendar for new dates (VT direction).

```json
{ "command": "calendar", "args": ["--from-latest", "--direction", "vt"] }
```

---

## Check missing fares

**ID:** Yii7AvSUQt4bkCwE

### Trigger
- 4x Daily: 7:30 AM, 10:15 AM, 1:00 PM, 3:00 PM
- Random delay 0-45 minutes after trigger

### Action
Checks for missing fare data (default direction).

```json
{ "command": "check-missing", "args": ["--limit", "8"] }
```

---

## via get fares reverse

**ID:** tD5VTVQucObZIGoF

### Trigger
- 4x Daily: 8:00 AM, 10:45 AM, 1:30 PM, 3:30 PM
- Random delay 0-45 minutes after trigger

### Action
Checks for missing fare data (VT direction).

```json
{ "command": "check-missing", "args": ["--limit", "8", "--direction", "vt"] }
```

---

## Common Details

### Target API
Local service at `127.0.0.1:5001/api/run-command`

### Credentials
None (localhost)

### Notes
- Random delays help avoid rate limiting on external services
- Two directions: default and "vt" (reverse)
- Limit of 8 fares per check
