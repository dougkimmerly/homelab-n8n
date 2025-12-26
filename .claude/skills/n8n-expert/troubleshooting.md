# n8n Troubleshooting Guide

## Common Issues

### Workflow Won't Activate

**Symptoms:** Activation fails or workflow deactivates immediately

**Causes:**
1. No trigger node (or only Manual Trigger)
2. Trigger node misconfigured
3. Required credential missing or invalid
4. Syntax error in expression

**Fix:**
- Check for Schedule, Webhook, or other auto-trigger
- Verify credentials are configured and working
- Check node parameters for errors

### Webhook Not Receiving Data

**Symptoms:** Webhook URL doesn't respond or no execution triggered

**Causes:**
1. Workflow not active
2. Wrong HTTP method (GET vs POST)
3. Firewall/network blocking
4. Wrong webhook path

**Fix:**
- Verify workflow is active (green toggle)
- Check webhook URL in node (Test vs Production URL)
- Test with curl from same network

### HTTP Request Failing

**Symptoms:** HTTP Request node errors out

**Causes:**
1. Network unreachable
2. Authentication failed
3. SSL certificate issues
4. Timeout

**Fix:**
- Test URL manually first
- Check credentials
- Try "Ignore SSL Issues" option
- Increase timeout in node settings

### Expression Errors

**Symptoms:** `{{ }}` expressions return undefined or error

**Causes:**
1. Wrong path to data
2. Data doesn't exist yet (node hasn't run)
3. Typo in property name

**Fix:**
- Use expression editor to browse available data
- Check previous node output
- Use `$json.property` for current item
- Use `$('Node Name').item.json.property` for other nodes

### Credential Issues

**Symptoms:** "Credentials not found" or auth failures

**Causes:**
1. Credential deleted or renamed
2. Token expired
3. Wrong credential type selected

**Fix:**
- Re-select credential in node
- Test credential via n8n UI
- Recreate if expired/invalid

## Debugging Steps

### 1. Check Execution History
```
Use: list_executions with status: error
Then: get_execution with includeData: true
```

### 2. Examine Node Output
In n8n UI:
- Click on node after execution
- Check "Output" tab
- Look for unexpected data shape

### 3. Test Nodes Individually
- Execute single nodes during development
- Verify each step before connecting

### 4. Check Logs
```bash
# CasaOS
ssh casaos@192.168.20.19
docker logs n8n --tail 100

# Mac (if running as service)
tail -f ~/Library/Logs/n8n.log
```

## Instance-Specific Issues

### CasaOS (192.168.20.19)
- May need Docker restart: `docker restart n8n`
- Check container health: `docker ps | grep n8n`
- Volume permissions: data in `/DATA/AppData/n8n`

### Mac (localhost)
- Check if running: `pgrep -f n8n`
- Start manually: `n8n start`
- Check launchd if service: `launchctl list | grep n8n`

### Synology (192.168.20.16)
- Different port: 5679
- Docker path: `/volume1/docker/n8n`
- Use full docker path: `sudo /usr/local/bin/docker`
