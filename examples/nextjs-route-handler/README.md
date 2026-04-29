# Next.js Route Handler

Suggested repo name: `reqrun-example-nextjs-route-handler`

Use this when your Next.js app needs to accept a request and hand off durable LLM work through ReqRun.

## What this starter shows

- accept a request in a Next.js route handler
- submit the LLM call to ReqRun instead of OpenAI directly
- return the durable async response immediately

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
REQRUN_BASE_URL=https://api.reqrun.com
```

## Example

```ts
import { NextRequest, NextResponse } from "next/server";

export async function POST(request: NextRequest) {
  const body = await request.json();

  const response = await fetch(
    `${process.env.REQRUN_BASE_URL ?? "https://api.reqrun.com"}/v1/chat/completions`,
    {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "Authorization": `Bearer ${process.env.REQRUN_API_KEY!}`,
      },
      body: JSON.stringify({
        model: "gpt-5-nano",
        messages: [
          { role: "system", content: "Return concise JSON." },
          { role: "user", content: body.prompt },
        ],
        wait: false,
        idempotency_key: body.jobId,
      }),
    }
  );

  const result = await response.json();
  return NextResponse.json(result, { status: response.status });
}
```

## Why ReqRun fits here

If the request is slow, retried, or interrupted, your app still gets a stable `rr_` request id instead of losing the operation inside the route handler.
