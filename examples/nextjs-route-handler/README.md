# Next.js Route Handler

Suggested repo name: `reqrun-example-nextjs-route-handler`

Use this when a Next.js app should accept browser input, forward LLM work through server-side route handlers, and return durable request ids.

## Status

This folder is a docs-only reference.

For a working signed implementation, use:
- [ReqRun/reqrun-example-nextjs-route-handler](https://github.com/ReqRun/reqrun-example-nextjs-route-handler)

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
REQRUN_SIGNING_SECRET=REQRUN_SIGNING_SECRET_HERE
REQRUN_BASE_URL=https://api.reqrun.com
```

## Request shape

```ts
const path = "/v1/chat/completions";
const bodyString = JSON.stringify({
  model: "gpt-5-nano",
  messages: [{ role: "user", content: body.prompt }],
  wait: false,
  idempotency_key: body.jobId,
});

const response = await fetch(`https://api.reqrun.com${path}`, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    ...await buildSignedReqRunHeaders(process.env.REQRUN_API_KEY!, process.env.REQRUN_SIGNING_SECRET!, "POST", path, bodyString),
  },
  body: bodyString,
});
```

## Why ReqRun fits here

Next.js route handlers stay focused on app-side orchestration while ReqRun owns durable execution and retry behavior.
