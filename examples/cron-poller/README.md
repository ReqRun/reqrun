# Cron Poller

Suggested repo name: `reqrun-example-cron-poller`

Use this when you want a scheduled process to submit a request, store the async id, and poll for completion later.

## What this starter shows

- submit one scheduled request to ReqRun
- store the `rr_` request id
- poll for status later instead of blocking the cron job

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
REQRUN_BASE_URL=https://api.reqrun.com
```

## Example

```js
const baseURL = process.env.REQRUN_BASE_URL ?? "https://api.reqrun.com";

const response = await fetch(`${baseURL}/v1/chat/completions`, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "Authorization": `Bearer ${process.env.REQRUN_API_KEY}`,
  },
  body: JSON.stringify({
    model: "gpt-5-nano",
    messages: [{ role: "user", content: "Generate the daily summary." }],
    wait: false,
    idempotency_key: `daily-summary-${new Date().toISOString().slice(0, 10)}`,
  }),
}).then((res) => res.json());

if (response.object === "chat.completion.async") {
  console.log("Queued request:", response.id);
  const result = await fetch(`${baseURL}/v1/requests/${response.id}`, {
    headers: {
      "Authorization": `Bearer ${process.env.REQRUN_API_KEY}`,
    },
  }).then((res) => res.json());

  console.log("Current status:", result.status);
}
```

## Why ReqRun fits here

Cron jobs are a poor place to hide retries and uncertain upstream outcomes. ReqRun gives the job a durable request record it can revisit safely.
