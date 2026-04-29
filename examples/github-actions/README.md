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
        run: |
          curl -X POST https://api.reqrun.com/v1/chat/completions \
            -H "Content-Type: application/json" \
            -H "Authorization: Bearer ${REQRUN_API_KEY}" \
            -d '{
              "model": "gpt-5-nano",
              "messages": [{"role": "user", "content": "Summarize the latest changelog."}],
              "wait": false,
              "idempotency_key": "github-actions-example"
            }'
```

## Why ReqRun fits here

CI jobs and automation runs should not hide uncertain model execution behind one step. ReqRun gives the workflow a durable request handle it can inspect later.
