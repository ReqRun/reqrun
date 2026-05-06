# Cron Poller

Suggested repo name: `reqrun-example-cron-poller`

Use this when a scheduled process should submit durable work, store the async request id, and poll later instead of blocking the whole cron run.

## Status

This folder is a docs-only reference.

Use the developer quickstart for the exact signing helper:
- [`../docs/dev-quickstart.md`](../../docs/dev-quickstart.md)

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
REQRUN_SIGNING_SECRET=REQRUN_SIGNING_SECRET_HERE
REQRUN_BASE_URL=https://api.reqrun.com
```

## Request shape

```js
const baseURL = process.env.REQRUN_BASE_URL ?? "https://api.reqrun.com";
const path = "/v1/chat/completions";
const bodyString = JSON.stringify({
  model: "gpt-5-nano",
  messages: [{ role: "user", content: "Generate the daily summary." }],
  wait: false,
  idempotency_key: `daily-summary-${new Date().toISOString().slice(0, 10)}`,
});

const response = await fetch(`${baseURL}${path}`, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    ...await buildSignedReqRunHeaders(process.env.REQRUN_API_KEY, process.env.REQRUN_SIGNING_SECRET, "POST", path, bodyString),
  },
  body: bodyString,
}).then((res) => res.json());

if (response.object === "chat.completion.async") {
  const requestPath = `/v1/requests/${response.id}`;
  const result = await fetch(`${baseURL}${requestPath}`, {
    headers: await buildSignedReqRunHeaders(process.env.REQRUN_API_KEY, process.env.REQRUN_SIGNING_SECRET, "GET", requestPath, ""),
  }).then((res) => res.json());
}
```

## Why ReqRun fits here

Cron jobs are a poor place to hide retries and uncertain upstream outcomes. ReqRun gives the job a durable request record it can revisit safely.
