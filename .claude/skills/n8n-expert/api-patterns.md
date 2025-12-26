# n8n API Patterns

Common patterns for working with APIs in n8n workflows.

## HTTP Request Node Basics

### GET Request
```json
{
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "method": "GET",
    "url": "https://api.example.com/resource",
    "authentication": "predefinedCredentialType",
    "nodeCredentialType": "httpHeaderAuth"
  }
}
```

### POST with JSON Body
```json
{
  "parameters": {
    "method": "POST",
    "url": "https://api.example.com/resource",
    "sendBody": true,
    "bodyParameters": {
      "parameters": [
        { "name": "key", "value": "={{ $json.value }}" }
      ]
    },
    "contentType": "json"
  }
}
```

### POST with Raw JSON
```json
{
  "parameters": {
    "method": "POST",
    "url": "https://api.example.com/resource",
    "sendBody": true,
    "specifyBody": "json",
    "jsonBody": "={{ JSON.stringify($json) }}"
  }
}
```

---

## Authentication Patterns

### Header Auth (API Key)
```json
{
  "parameters": {
    "authentication": "genericCredentialType",
    "genericAuthType": "httpHeaderAuth",
    "sendHeaders": true,
    "headerParameters": {
      "parameters": [
        { "name": "X-API-Key", "value": "={{ $credentials.apiKey }}" }
      ]
    }
  }
}
```

### Bearer Token
```json
{
  "parameters": {
    "authentication": "genericCredentialType",
    "genericAuthType": "httpHeaderAuth",
    "sendHeaders": true,
    "headerParameters": {
      "parameters": [
        { "name": "Authorization", "value": "Bearer {{ $credentials.token }}" }
      ]
    }
  }
}
```

### Basic Auth
```json
{
  "parameters": {
    "authentication": "genericCredentialType",
    "genericAuthType": "httpBasicAuth"
  }
}
```

### Query Parameter Auth
```json
{
  "parameters": {
    "authentication": "none",
    "sendQuery": true,
    "queryParameters": {
      "parameters": [
        { "name": "api_key", "value": "={{ $credentials.apiKey }}" }
      ]
    }
  }
}
```

---

## Pagination Patterns

### Offset-Based Pagination
Use a Loop node or Code node to paginate:

```javascript
// Code node for offset pagination
const baseUrl = 'https://api.example.com/items';
const limit = 100;
let offset = 0;
let allItems = [];
let hasMore = true;

while (hasMore) {
  const response = await this.helpers.httpRequest({
    method: 'GET',
    url: `${baseUrl}?limit=${limit}&offset=${offset}`,
    headers: { 'Authorization': `Bearer ${$credentials.token}` }
  });

  allItems = allItems.concat(response.items);
  offset += limit;
  hasMore = response.items.length === limit;
}

return allItems.map(item => ({ json: item }));
```

### Cursor-Based Pagination
```javascript
// Code node for cursor pagination
const baseUrl = 'https://api.example.com/items';
let cursor = null;
let allItems = [];

do {
  const url = cursor ? `${baseUrl}?cursor=${cursor}` : baseUrl;
  const response = await this.helpers.httpRequest({
    method: 'GET',
    url: url
  });

  allItems = allItems.concat(response.data);
  cursor = response.next_cursor;
} while (cursor);

return allItems.map(item => ({ json: item }));
```

### Link Header Pagination (GitHub style)
```javascript
// Parse Link header for next page
const linkHeader = $json.headers?.link || '';
const nextMatch = linkHeader.match(/<([^>]+)>;\s*rel="next"/);
const nextUrl = nextMatch ? nextMatch[1] : null;

return [{ json: { ...items, nextUrl } }];
```

---

## Rate Limiting Patterns

### Simple Delay Between Requests
Use **Wait** node between HTTP requests:
```json
{
  "type": "n8n-nodes-base.wait",
  "parameters": {
    "amount": 1,
    "unit": "seconds"
  }
}
```

### Batch Processing with Delays
Use **Split In Batches** + **Wait**:
```
[Split In Batches (size: 10)] → [HTTP Request] → [Wait 1s] → [Loop back]
```

### Respect Rate Limit Headers
```javascript
// Code node to handle rate limits
const response = $input.first().json;
const remaining = parseInt(response.headers['x-ratelimit-remaining'] || '100');
const resetTime = parseInt(response.headers['x-ratelimit-reset'] || '0');

if (remaining < 5) {
  const waitMs = (resetTime * 1000) - Date.now();
  if (waitMs > 0) {
    await new Promise(resolve => setTimeout(resolve, waitMs));
  }
}

return $input.all();
```

### Retry with Exponential Backoff
```javascript
// Code node with retry logic
async function fetchWithRetry(url, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await this.helpers.httpRequest({ method: 'GET', url });
    } catch (error) {
      if (error.response?.status === 429 && i < maxRetries - 1) {
        const delay = Math.pow(2, i) * 1000; // 1s, 2s, 4s
        await new Promise(resolve => setTimeout(resolve, delay));
      } else {
        throw error;
      }
    }
  }
}
```

---

## Error Handling Patterns

### Continue On Fail + Check Error
1. Enable "Continue On Fail" on HTTP Request node
2. Add IF node to check for errors:

```javascript
// IF node condition
{{ $json.error !== undefined }}
```

### Try/Catch with Error Branch
```
[HTTP Request (continueOnFail: true)]
    → [IF: Has Error?]
        → True: [Error Handler / Notification]
        → False: [Continue Processing]
```

### Global Error Workflow
Create a separate workflow with Error Trigger:
```json
{
  "type": "n8n-nodes-base.errorTrigger",
  "parameters": {}
}
```
Then set workflows to use this error workflow in Settings.

### Validate Response Structure
```javascript
// Code node to validate API response
const response = $json;

if (!response.data || !Array.isArray(response.data)) {
  throw new Error('Invalid API response: missing data array');
}

if (response.error) {
  throw new Error(`API Error: ${response.error.message}`);
}

return $input.all();
```

---

## Webhook Patterns

### Incoming Webhook (Receive Data)
```json
{
  "type": "n8n-nodes-base.webhook",
  "parameters": {
    "path": "my-webhook-endpoint",
    "httpMethod": "POST",
    "responseMode": "onReceived",
    "responseData": "allEntries"
  }
}
```

### Webhook with Immediate Response + Async Processing
```json
{
  "type": "n8n-nodes-base.webhook",
  "parameters": {
    "path": "async-process",
    "responseMode": "responseNode",
    "options": {}
  }
}
```
Then use **Respond to Webhook** node immediately, continue processing after.

### Webhook with Validation
```javascript
// Code node after webhook to validate payload
const payload = $json.body;
const secret = $json.headers['x-webhook-secret'];

if (secret !== 'expected-secret') {
  throw new Error('Invalid webhook secret');
}

if (!payload.event || !payload.data) {
  throw new Error('Malformed webhook payload');
}

return [{ json: payload }];
```

### Outgoing Webhook (Send Data)
Just use HTTP Request node with POST method to the target webhook URL.

---

## Common Homelab API Patterns

### Portainer API
```javascript
// Get containers
const response = await this.helpers.httpRequest({
  method: 'GET',
  url: 'http://192.168.20.19:9000/api/endpoints/1/docker/containers/json',
  headers: { 'X-API-Key': $credentials.portainerApiKey }
});
```

### Cloudflare API
```javascript
// List DNS records
const response = await this.helpers.httpRequest({
  method: 'GET',
  url: `https://api.cloudflare.com/client/v4/zones/${zoneId}/dns_records`,
  headers: {
    'Authorization': `Bearer ${$credentials.cloudflareToken}`,
    'Content-Type': 'application/json'
  }
});
```

### GitHub API
```javascript
// Get repository contents
const response = await this.helpers.httpRequest({
  method: 'GET',
  url: 'https://api.github.com/repos/owner/repo/contents/path',
  headers: {
    'Authorization': `token ${$credentials.githubToken}`,
    'Accept': 'application/vnd.github.v3+json'
  }
});
```

### Pushover Notifications
```javascript
// Send notification
await this.helpers.httpRequest({
  method: 'POST',
  url: 'https://api.pushover.net/1/messages.json',
  body: {
    token: $credentials.pushoverAppToken,
    user: $credentials.pushoverUserKey,
    message: 'Alert from n8n workflow',
    title: 'Homelab Alert'
  },
  json: true
});
```

---

## Response Handling

### Extract Nested Data
```javascript
// Code node to flatten nested response
const items = $json.data.results.map(item => ({
  id: item.id,
  name: item.attributes.name,
  status: item.attributes.status
}));

return items.map(item => ({ json: item }));
```

### Handle Empty Responses
```javascript
// Code node to handle empty/null responses
const data = $json.data || [];

if (data.length === 0) {
  // Return empty but valid result
  return [{ json: { empty: true, message: 'No data returned' } }];
}

return data.map(item => ({ json: item }));
```

### Transform Response Format
```javascript
// Convert API response to standard format
const input = $json;

return [{
  json: {
    success: input.status === 'ok',
    data: input.payload || input.data || input.result,
    timestamp: new Date().toISOString(),
    source: 'api-name'
  }
}];
```

---

## Best Practices

1. **Always set timeouts** - Default is often too long; set reasonable limits
2. **Log API calls** - Use Set node to capture request/response for debugging
3. **Use credentials** - Never hardcode API keys in workflows
4. **Handle all error codes** - 4xx and 5xx need different handling
5. **Validate before sending** - Check required fields exist before API call
6. **Cache when possible** - Store infrequently changing data locally
7. **Use environment variables** - For base URLs that might change between instances
