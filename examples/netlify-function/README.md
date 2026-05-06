# Netlify Function

Suggested repo name: `reqrun-example-netlify-function`

Use this when a Netlify function should submit durable LLM work and return quickly instead of owning retry logic itself.

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
const path = "/v1/chat/completions";
const bodyString = JSON.stringify({
  model: "gpt-5-nano",
  messages: [{ role: "user", content: body.prompt }],
  wait: false,
});

const response = await fetch(`https://api.reqrun.com${path}`, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    ...await buildSignedReqRunHeaders(process.env.REQRUN_API_KEY, process.env.REQRUN_SIGNING_SECRET, "POST", path, bodyString),
  },
  body: bodyString,
});
```

## Why ReqRun fits here

The function stays small and predictable while ReqRun handles the durability layer around the model request.
