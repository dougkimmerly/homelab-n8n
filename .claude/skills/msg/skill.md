---
name: msg
description: Check and execute all pending tasks from message queue (v3 autonomous protocol)
triggers:
  - msg
  - check messages
  - any tasks
  - check tasks
location: project
---

# msg - Process Tasks

**FULLY AUTONOMOUS - NO CONFIRMATIONS**

## Quick Reference

| Location | Purpose |
|----------|---------|
| `.claude/handoff/inbox/` | Tasks FOR YOU arrive here |
| `.claude/handoff/in-process/` | Currently working on |
| `.claude/handoff/complete/` | Done - put responses here |

## CRITICAL: Where Files Go

```
RECEIVING tasks:      Check YOUR repo's inbox/
SENDING to a CC:      Write to THEIR repo's inbox/
RESPONDING to CC2CC:  Write REPORT to SENDER's inbox/
```

**The pattern is always: write to the RECIPIENT's inbox, not your own!**

**Example - CC-dashboard wants to send task to CC-brain:**
```bash
# WRONG - putting in your own inbox
/homelab-dashboard/.claude/handoff/inbox/CC2CC-brain-xxx.md  # NO!

# RIGHT - put in the RECIPIENT's repo inbox
/homelab-brain/.claude/handoff/inbox/CC2CC-dashboard-xxx.md  # YES!
```

**Find CC repo location via broker API:**
```bash
# Get directory for any CC
curl -s http://localhost:9500/api/entities/CC-brain | jq -r '.directory'

# List all CCs with their directories
curl -s http://localhost:9500/api/entities | jq -r '.entities[] | select(.type=="cc") | "\(.id): \(.directory)"'
```

## Steps

### 1. Pull + Set Active

```bash
git pull
curl -s -X POST "http://localhost:9500/api/sessions/${ENTITY_ID}/status" \
  -H "Content-Type: application/json" -d '{"status":"active"}' > /dev/null 2>&1
```

**Note:** Use YOUR entity ID (from `.claude/expert-profile.json`) in place of `${ENTITY_ID}`.

### 2. Check for Tasks

```bash
# List all task files (works in both bash and zsh)
ls .claude/handoff/inbox/ 2>/dev/null | grep -E "^(TASK-|CC2CC-|REPORT-|RESPONSE-).*\.md$"
```

If empty, also check `in-process/` for incomplete work.

### 3. For Each Task

**a) Claim it:**
```bash
git mv .claude/handoff/inbox/TASK-XXX.md .claude/handoff/in-process/
git commit -m "Claim TASK-XXX" && git push
```

**b) Execute it** - Do the work. No confirmations needed.

**c) Complete it:**
```bash
mkdir -p .claude/handoff/complete/TASK-XXX
# Write RESPONSE-XXX.md (see template below)
git mv .claude/handoff/in-process/TASK-XXX.md .claude/handoff/complete/TASK-XXX/task.md
git add -A && git commit -m "Complete TASK-XXX" && git push
```

**d) Notify broker:**
```bash
curl -s -X POST "http://localhost:9500/api/responses" \
  -H "Content-Type: application/json" \
  -d '{"from":"${ENTITY_ID}","to":"PM-web-001","task_id":"TASK-XXX","summary":"Brief summary"}'
```

**e) Loop** - Check for more tasks. Repeat until inbox is empty.

### 4. When Done

```bash
curl -s -X POST "http://localhost:9500/api/sessions/${ENTITY_ID}/status" \
  -H "Content-Type: application/json" -d '{"status":"idle"}' > /dev/null 2>&1
```

## Response Template

```markdown
# RESPONSE-XXX: Title

**Task:** TASK-XXX
**Status:** COMPLETE
**Completed:** 2026-01-04T00:00:00Z

## Summary
What was done in 2-3 sentences.

## Changes Made
- `file.js` - What changed

## Verification
- Acceptance criteria met
```

## Key Rules

1. **inbox/** has incoming tasks - NOT for your own todos
2. **Always git pull first**
3. **No confirmations** - just execute
4. **One task at a time** - claim, complete, then next
5. **POST to broker** after each completion

---

## Sending CC2CC Tasks

When you need another CC to do something:

### 1. Find their repo directory

```bash
# Get the target CC's directory from broker
TARGET_DIR=$(curl -s http://localhost:9500/api/entities/CC-brain | jq -r '.directory')
echo $TARGET_DIR
```

### 2. Write task to THEIR repo's inbox

```bash
# You are CC-dashboard, sending to CC-brain
cat > "${TARGET_DIR}/.claude/handoff/inbox/CC2CC-dashboard-description.md" << 'EOF'
# CC2CC Task: [Title]

**From:** CC-dashboard
**To:** CC-brain
**Date:** 2026-01-04

## Request
What you need them to do...

## Context
Why you need it...

## Expected Response
What you want back...
EOF
```

### 3. Commit and push THEIR repo

```bash
cd "${TARGET_DIR}"
git add .claude/handoff/inbox/CC2CC-*.md
git commit -m "CC2CC: Request from $(basename $(pwd))"
git push
```

### 4. Return to your repo

```bash
cd -  # or cd back to your repo
```

**Remember:** You're writing to THEIR inbox, not yours!

---

## Responding to CC2CC Tasks

When you complete a CC2CC task from another CC, you must send a REPORT back to the SENDER's inbox.

### 1. Complete the task in YOUR repo

```bash
# Move task to complete folder (in YOUR repo)
mkdir -p .claude/handoff/complete/CC2CC-sender-description
git mv .claude/handoff/in-process/CC2CC-sender-description.md .claude/handoff/complete/CC2CC-sender-description/task.md
# Create your response
cat > .claude/handoff/complete/CC2CC-sender-description/RESPONSE.md << 'EOF'
# Response: [Title]
**Status:** COMPLETE
## Summary
What you did...
EOF
git add -A && git commit -m "Complete CC2CC task" && git push
```

### 2. Find the SENDER's repo directory

```bash
# The sender is in the task's "From:" field
# Get their directory from broker
SENDER_DIR=$(curl -s http://localhost:9500/api/entities/CC-dashboard | jq -r '.directory')
echo $SENDER_DIR
```

### 3. Write REPORT to SENDER's inbox

```bash
# You are CC-brain, responding to CC-dashboard
cat > "${SENDER_DIR}/.claude/handoff/inbox/REPORT-brain-description.md" << 'EOF'
# REPORT: [Title]

**From:** CC-brain
**To:** CC-dashboard
**Original Task:** CC2CC-dashboard-description
**Status:** COMPLETE
**Completed:** 2026-01-04T05:00:00Z

## Summary
What was done...

## Details
Key information they need...
EOF
```

### 4. Commit and push SENDER's repo

```bash
cd "${SENDER_DIR}"
git add .claude/handoff/inbox/REPORT-*.md
git commit -m "REPORT: Response from CC-brain"
git push
cd -  # return to your repo
```

### Key Points

- REPORT goes to **SENDER's inbox**, not your complete folder
- SENDER's entity ID is in the task's **From:** field
- Use broker API to find their directory: `curl -s http://localhost:9500/api/entities/CC-xxx | jq -r '.directory'`
- File naming: `REPORT-{your-entity}-{description}.md`

---

## Processing REPORT Files (rpt command)

When you receive `rpt` command, another CC has sent you a REPORT in response to a CC2CC task you sent them.

### 1. Check inbox for REPORT/RESPONSE files

```bash
# Accept both REPORT-* and RESPONSE-* (either naming works)
ls .claude/handoff/inbox/ 2>/dev/null | grep -E "^(REPORT-|RESPONSE-).*\.md$"
```

### 2. Claim the REPORT (move to in-process)

```bash
git mv .claude/handoff/inbox/REPORT-xxx.md .claude/handoff/in-process/
git commit -m "Claim REPORT-xxx" && git push
```

**Important:** If you don't move it to in-process, the broker will keep reminding you it's there!

### 3. Read and process the report

```bash
cat .claude/handoff/in-process/REPORT-xxx.md
```

- Review what they completed
- Take any follow-up actions needed
- Use the information provided

### 4. Move to complete when done

```bash
mkdir -p .claude/handoff/complete/REPORT-xxx
git mv .claude/handoff/in-process/REPORT-xxx.md .claude/handoff/complete/REPORT-xxx/report.md
git add -A && git commit -m "Process REPORT-xxx: [what you did with it]" && git push
```

### Key Points

- REPORT files arrive in YOUR **inbox/** (not complete/)
- Treat them like tasks: claim → process → complete
- No need to respond back - REPORT is the final message in the CC2CC exchange
- Move to in-process to stop broker reminders
