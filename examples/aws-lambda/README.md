# AWS Lambda

Suggested repo name: `reqrun-example-aws-lambda`

Use this when a Lambda function should accept an event, submit durable LLM work through ReqRun, and return the async response or completed result.

## Status

This folder is a docs-only reference.

For a working signed implementation, use:
- [ReqRun/reqrun-example-aws-lambda](https://github.com/ReqRun/reqrun-example-aws-lambda)

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
  messages: [{ role: "user", content: body.prompt }],
  wait: false,
  idempotency_key: body.jobId,
});

const response = await fetch(`https://api.reqrun.com${path}`, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    ...getSignedReqRunHeaders(process.env.REQRUN_API_KEY, process.env.REQRUN_SIGNING_SECRET, "POST", path, bodyString),
  },
  body: bodyString,
});
```

## Why ReqRun fits here

ReqRun gives Lambda a durable request lifecycle instead of hiding uncertain model execution inside one function run.
