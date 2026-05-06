# Cloudflare Worker

Suggested repo name: `reqrun-example-cloudflare-worker`

Use this when you want an edge worker to accept traffic quickly and hand durable LLM execution off to ReqRun.

## Status

This folder is a docs-only reference.

For a working signed implementation, use:
- [ReqRun/reqrun-example-cloudflare-worker](https://github.com/ReqRun/reqrun-example-cloudflare-worker)

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
    ...await buildSignedReqRunHeaders(env.REQRUN_API_KEY, env.REQRUN_SIGNING_SECRET, "POST", path, bodyString),
  },
  body: bodyString,
});
```

## Why ReqRun fits here

Edge runtimes are great for receiving traffic quickly. ReqRun takes over once the work needs durable execution and retry behavior.
