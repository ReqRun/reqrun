# Vercel Function

Suggested repo name: `reqrun-example-vercel-function`

Use this when a serverless function should hand long-running LLM work off to ReqRun and return durable status instead of waiting inside one request.

## Status

This folder is a docs-only reference.

For a working signed implementation, use:
- [ReqRun/reqrun-example-vercel-function](https://github.com/ReqRun/reqrun-example-vercel-function)

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
REQRUN_SIGNING_SECRET=REQRUN_SIGNING_SECRET_HERE
REQRUN_BASE_URL=https://api.reqrun.com
```

## Request shape

```js
const path = "/v1/chat/completions";
const bodyString = JSON.stringify({
  model: "gpt-5-nano",
  messages: [{ role: "user", content: req.body.prompt }],
  wait: false,
  idempotency_key: req.body.jobId,
});

const response = await fetch(`https://api.reqrun.com${path}`, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    ...getSignedReqRunHeaders(process.env.REQRUN_API_KEY!, process.env.REQRUN_SIGNING_SECRET!, "POST", path, bodyString),
  },
  body: bodyString,
});
```

## Why ReqRun fits here

Vercel functions are good entry points. ReqRun becomes the durable execution layer once the AI task matters.
