# Express API

Suggested repo name: `reqrun-example-express-api`

Use this when a Node API should accept input, submit durable LLM work, and return quickly instead of hiding retries in-process.

## Status

This folder is a docs-only reference.

For a working signed implementation, use:
- [ReqRun/reqrun-example-express-api](https://github.com/ReqRun/reqrun-example-express-api)

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
  messages: [{ role: "user", content: prompt }],
  wait: false,
  idempotency_key: req.body.jobId,
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

Express stays small and predictable while ReqRun owns durable request execution, retries, and status visibility.
