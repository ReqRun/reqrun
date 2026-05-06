# GitHub Actions

Suggested repo name: `reqrun-example-github-actions`

Use this when a workflow should trigger an AI task durably and store the resulting request id for later steps.

## What this starter shows

- submit AI work from GitHub Actions
- send the request through ReqRun instead of the model provider directly
- keep a durable request id for later polling or follow-up steps

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
REQRUN_SIGNING_SECRET=REQRUN_SIGNING_SECRET_HERE
```

Store the key as a repository secret before running the workflow.

## Example workflow

```yaml
name: reqrun-task

on:
  workflow_dispatch:

jobs:
  run-task:
    runs-on: ubuntu-latest
    steps:
      - name: Submit request to ReqRun
        env:
          REQRUN_API_KEY: ${{ secrets.REQRUN_API_KEY }}
          REQRUN_SIGNING_SECRET: ${{ secrets.REQRUN_SIGNING_SECRET }}
        run: |
          node -e "const crypto = require('node:crypto'); const body = JSON.stringify({ model: 'gpt-5-nano', messages: [{ role: 'user', content: 'Summarize the latest changelog.' }], wait: false, idempotency_key: 'github-actions-example' }); const method = 'POST'; const path = '/v1/chat/completions'; const timestamp = new Date().toISOString(); const nonce = crypto.randomUUID().replace(/-/g, ''); const bodyHash = crypto.createHash('sha256').update(body).digest('hex'); const payload = [method, path, timestamp, nonce, bodyHash].join('\n'); const signature = crypto.createHmac('sha256', process.env.REQRUN_SIGNING_SECRET).update(payload).digest('hex'); fetch('https://api.reqrun.com' + path, { method, headers: { 'Content-Type': 'application/json', 'Authorization': 'Bearer ' + process.env.REQRUN_API_KEY, 'X-ReqRun-Timestamp': timestamp, 'X-ReqRun-Nonce': nonce, 'X-ReqRun-Signature': 'v1=' + signature }, body }).then(async (res) => { console.log(await res.text()); process.exit(res.ok ? 0 : 1); }).catch((error) => { console.error(error); process.exit(1); });"
```

## Why ReqRun fits here

CI jobs and automation runs should not hide uncertain model execution behind one step. ReqRun gives the workflow a durable request handle it can inspect later.
