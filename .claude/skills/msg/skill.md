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

# Message Queue Auto-Execute (v3)

## Invocation

This skill is invoked manually. Type one of:
- `msg`
- `check messages`
- `Read .claude/skills/msg/skill.md and execute it`

Note: The UserPromptSubmit hook is disabled due to Claude Code bugs.

---

**CRITICAL: This is FULLY AUTONOMOUS - NO CONFIRMATIONS**

Execute ALL pending tasks in priority order until queue is empty.

## V4 File Naming Rules

**Valid file prefixes for `.claude/handoff/todo/`:**

| Prefix | Usage | Example |
|--------|-------|---------|
| `TASK-` | Tasks from PM or broker | `TASK-2081.md` |
| `CC2CC-` | CC-to-CC messages | `CC2CC-framework-n8n-2081.md` |
| `REPORT-` | Reports/responses from other CCs | `REPORT-framework-2081.md` |

**NO OTHER PREFIXES are valid.** Do not invent new prefixes based on task descriptions.

**CC2CC naming convention:**
```
CC2CC-{sender}-{recipient}-{identifier}.md
```

## Execution Steps

### 1. Connect to Broker (Optional)

Register with broker and set status to active:

```bash
BROKER_URL="${BROKER_URL:-http://localhost:9500}"
ENTITY_ID="CC-n8n"
REPO=$(git remote get-url origin 2>/dev/null | sed -e 's#.*github.com[:/]##' -e 's#\.git$##')

curl -s -X POST "${BROKER_URL}/api/sessions/${ENTITY_ID}/connect" \
  -H "Content-Type: application/json" \
  -d "{\"directory\":\"$(pwd)\",\"pid\":$$,\"repo\":\"${REPO}\"}" > /dev/null 2>&1 || true

curl -s -X POST "${BROKER_URL}/api/sessions/${ENTITY_ID}/status" \
  -H "Content-Type: application/json" \
  -d '{"status":"active"}' > /dev/null 2>&1 || true
```

### 2. ALWAYS Git Pull First

**CRITICAL: This must happen BEFORE checking for tasks**

```bash
git pull
```

This prevents working on stale local copy and missing tasks queued by PM.

### 3. Process ALL Tasks (Auto-Execute Loop)

Check both `in-process/` and `todo/` directories for tasks.

**Priority order:** critical > high > normal > low (then by task number)

For EACH task found, execute these steps:

#### a. Move to in-process/ (if from todo/)

If task is in todo/, move it to in-process/:

```bash
git mv .claude/handoff/todo/TASK-XXX-description.md .claude/handoff/in-process/TASK-XXX-description.md
git commit -m "Start TASK-XXX: {brief description}"
git push
```

#### b. Execute the task

- Read the task file
- Execute ALL requirements
- NO confirmations - fully autonomous
- Follow acceptance criteria exactly

#### c. Create response folder and move task

```bash
mkdir -p .claude/handoff/complete/TASK-XXX
git mv .claude/handoff/in-process/TASK-XXX-description.md .claude/handoff/complete/TASK-XXX/task.md
```

#### d. Write response

Create `.claude/handoff/complete/TASK-XXX/RESPONSE-XXX.md` following the template.

Update task status in task.md to COMPLETE.

#### e. Commit and push

```bash
git add .claude/handoff/complete/TASK-XXX/
git commit -m "Complete TASK-XXX: {brief description}

- {Changes made summary}
- See RESPONSE-XXX.md for details

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude Sonnet 4.5 (1M context) <noreply@anthropic.com>"
git push
```

#### f. POST response to broker (V4 protocol)

```bash
TASK_ID="TASK-XXX"
SUMMARY="Brief 1-2 sentence summary"
LOCATION="dougkimmerly/homelab-n8n/blob/main/.claude/handoff/complete/${TASK_ID}/RESPONSE-XXX.md"

curl -s -X POST "http://localhost:9500/api/responses" \
  -H "Content-Type: application/json" \
  -d "{\"from\":\"${ENTITY_ID}\",\"to\":\"PM-web-001\",\"task_id\":\"${TASK_ID}\",\"summary\":\"${SUMMARY}\",\"location\":\"${LOCATION}\"}" > /dev/null 2>&1 || true
```

#### g. Loop back

Check for more tasks and repeat until queue is empty.

### 4. When Queue Empty

Set broker status to idle:

```bash
curl -s -X POST "http://localhost:9500/api/sessions/${ENTITY_ID}/status" \
  -H "Content-Type: application/json" \
  -d '{"status":"idle"}' > /dev/null 2>&1 || true
```

Report to user: "All tasks complete. Queue empty."

## Response Template

Every task response must use the Executive Summary + Full Report format:

```markdown
# RESPONSE-XXX: {Task Title}

**Task:** TASK-XXX
**Status:** COMPLETE | BLOCKED | NEEDS_INFO
**Completed:** {ISO 8601 timestamp}
**Commit:** {git commit hash}

## Executive Summary

- {What was done - one line}
- {Key files changed - one line}
- {Key outcome/result - one line}

---

## Full Report

### Summary
{2-3 sentences}

### Changes Made
- `file.js` - What changed and why

### Verification
- ✅ Criterion 1 verified

### Issues Encountered
{Any blockers or "None"}

### Next Steps
{Follow-up tasks if needed}
```

## Key Rules

1. **FULLY AUTONOMOUS** - Execute all tasks without asking for confirmation
2. **ALWAYS git pull first** - Before checking for tasks
3. **Process ALL tasks** - Loop until queue is empty
4. **Priority order** - Process: critical > high > normal > low
5. **Set broker status** - Active at start, idle when done
6. **Follow response template** - Executive Summary + Full Report
7. **POST to broker** - Every completed task
8. **Check in-process/ first** - Complete claimed tasks before new ones
9. **Commit after each task** - Don't batch
10. **Use git mv** - Moves files between directories

## Error Handling

- If broker connection fails: Continue anyway (use `|| true`)
- If git pull fails: Report error and stop
- If task execution fails: Create BLOCKED response, continue
- If POST to broker fails: Continue silently
- If git push fails: Report error and stop
