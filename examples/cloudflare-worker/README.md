# Cloudflare Worker

Suggested repo name: `reqrun-example-cloudflare-worker`

Use this when you want an edge worker to hand off durable execution without keeping the request open.

## What this starter shows

- accept input at the edge
- submit the LLM call to ReqRun
- return quickly instead of waiting on the model provider

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
REQRUN_SIGNING_SECRET=REQRUN_SIGNING_SECRET_HERE
```

## Example

```ts
export default {
  async fetch(request: Request, env: { REQRUN_API_KEY: string }) {
    const body = await request.json();

    const response = await fetch("https://api.reqrun.com/v1/chat/completions", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "Authorization": `Bearer ${env.REQRUN_API_KEY}`,
      },
      body: JSON.stringify({
        model: "gpt-5-nano",
        messages: [{ role: "user", content: body.prompt }],
        wait: false,
        idempotency_key: body.jobId,
      }),
    });

    return new Response(await response.text(), {
      status: response.status,
      headers: { "Content-Type": "application/json" },
    });
  },
};
```

## Why ReqRun fits here

Edge runtimes are great for receiving traffic quickly. ReqRun takes over once the work needs durable execution and retry behavior.
