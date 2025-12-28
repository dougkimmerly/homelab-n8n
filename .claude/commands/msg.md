# /msg - Check Central Hub for Tasks

**This repo:** `homelab-n8n`
**Hub location:** `~/dkSRC/infrastructure/homelab/.claude/hub/`

## When user says "msg", "check messages", or "any tasks?"

### Step 1: Pull the central hub
```bash
cd ~/dkSRC/infrastructure/homelab && git pull && cd -
```

### Step 2: Read and filter task queue
```bash
cat ~/dkSRC/infrastructure/homelab/.claude/hub/task-queue.md
```
Look for tasks tagged `[homelab-n8n]` - ignore all other tags.

### Step 3: If task exists for this repo

1. **Execute the task** in this repo
2. **Commit your changes** to this repo
3. **Archive the task** - Copy to `homelab/.claude/hub/task-archive.md` with timestamp
4. **Remove from queue** - Edit `homelab/.claude/hub/task-queue.md`
5. **Write response** to `homelab/.claude/hub/response-queue.md`
6. **Push both repos:**
   ```bash
   git add . && git commit -m "Complete TASK-XXX" && git push
   cd ~/dkSRC/infrastructure/homelab && git add . && git commit -m "RESPONSE-XXX [homelab-n8n]" && git push
   ```

### Step 4: If no task for this repo
Report "No pending tasks for [homelab-n8n]."

## Response Format

```markdown
## RESPONSE-XXX [homelab-n8n]
**Task:** TASK-XXX
**Status:** ✅ Completed | ⚠️ Partial | ❌ Blocked
**Commit:** abc1234

### What Was Done
Summary.

### Files Modified
- `path/file` - description

### Notes
Any issues or follow-ups needed.
```
