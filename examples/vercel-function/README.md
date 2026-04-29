# Vercel Function

Suggested repo name: `reqrun-example-vercel-function`

Use this when you want a serverless function to submit work to ReqRun and return the async response immediately.

## What this starter shows

- accept input in a serverless function
- send the model request to ReqRun
- return the durable async response immediately

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
```

## Example

```ts
export async function POST(request: Request) {
  const body = await request.json();

  const response = await fetch("https://api.reqrun.com/v1/chat/completions", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "Authorization": `Bearer ${process.env.REQRUN_API_KEY!}`,
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
}
```

## Why ReqRun fits here

Serverless functions should not own complicated model retry behavior. ReqRun turns that into a durable request record instead.
