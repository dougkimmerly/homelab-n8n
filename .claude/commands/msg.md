# /msg - Execute Tasks from Central Hub

**This repo:** `homelab-n8n`  
**Tag to filter:** `[homelab-n8n]`  
**Hub:** `~/dkSRC/infrastructure/homelab/.claude/hub/`

---

## ⚠️ IMPORTANT: Do NOT ask permission - just execute the tasks!

When user says "msg", "check messages", or "any tasks?" - follow this protocol completely.

---

## Step 1: Pull the central hub

```bash
cd ~/dkSRC/infrastructure/homelab && git pull && cd -
```

## Step 2: Read task queue and filter for this repo

```bash
cat ~/dkSRC/infrastructure/homelab/.claude/hub/task-queue.md
```

- Only pick up tasks tagged `[homelab-n8n]`
- Ignore all other tags

## Step 3: Process tasks in priority order

**Priority order:** critical → high → normal → low  
**Within same priority:** lowest task number first (TASK-001 before TASK-002)

If no tasks for this repo: Report "No pending tasks for [homelab-n8n]." and stop.

## Step 4: For EACH task - Execute immediately (don't ask!)

1. **DO THE WORK** - Complete the task requirements in this repo
2. **Commit changes** to this repo
3. **Archive the task** - Prepend to `homelab/.claude/hub/task-archive.md` with timestamp
4. **Remove from queue** - Delete task from `homelab/.claude/hub/task-queue.md`
5. **Write response** - Prepend to `homelab/.claude/hub/response-queue.md` (format below)
6. **Push this repo:**
   ```bash
   git add . && git commit -m "Complete TASK-XXX: brief description" && git push
   ```
7. **Push hub updates:**
   ```bash
   cd ~/dkSRC/infrastructure/homelab && git add . && git commit -m "RESPONSE-XXX [homelab-n8n]" && git push && cd -
   ```

## Step 5: Final confirmation (REQUIRED)

After ALL tasks are complete and pushed, your LAST line of output MUST be:

```
✅ RESPONSE-XXX pushed to hub. Ready for PM to review with /resp
```

---

## Response Format

Prepend this to `homelab/.claude/hub/response-queue.md`:

```markdown
## RESPONSE-XXX [homelab-n8n]
**Task:** TASK-XXX
**Status:** ✅ Completed | ⚠️ Partial | ❌ Blocked
**Commit:** abc1234

### What Was Done
Summary of completed work.

### Files Modified
- `path/to/file` - what changed

### Notes
Any issues, blockers, or follow-up needed.

---
```

---

## Key Rules

- **Never ask permission** - just execute the tasks
- **Process in priority order** - critical first, then by task number
- **Always push both repos** - your changes AND the hub updates
- **Always end with confirmation** - PM needs to know response is ready
