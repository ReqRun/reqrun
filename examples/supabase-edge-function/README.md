# Supabase Edge Function

Suggested repo name: `reqrun-example-supabase-edge-function`

Use this when a Supabase edge function should submit durable AI work and return a request id.

## What this starter shows

- accept edge input in Supabase
- send the model request to ReqRun
- return a durable request object for later inspection

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
```

## Example

```ts
Deno.serve(async (request) => {
  const body = await request.json();

  const response = await fetch("https://api.reqrun.com/v1/chat/completions", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "Authorization": `Bearer ${Deno.env.get("REQRUN_API_KEY")!}`,
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
});
```

## Why ReqRun fits here

Supabase handles the edge entry point. ReqRun handles the durable execution of the LLM task itself.
