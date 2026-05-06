# Supabase Edge Function

Suggested repo name: `reqrun-example-supabase-edge-function`

Use this when a Supabase edge function should submit durable AI work and return a request id without keeping the full model call open.

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
    ...await buildSignedReqRunHeaders(Deno.env.get("REQRUN_API_KEY")!, Deno.env.get("REQRUN_SIGNING_SECRET")!, "POST", path, bodyString),
  },
  body: bodyString,
});
```

## Why ReqRun fits here

Supabase handles the edge entry point. ReqRun handles the durable execution of the LLM task itself.
