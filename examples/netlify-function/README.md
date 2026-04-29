# Netlify Function

Suggested repo name: `reqrun-example-netlify-function`

Use this when a Netlify function needs a simple fetch-based handoff to ReqRun.

## What this starter shows

- accept a request in a Netlify function
- send durable LLM work to ReqRun
- return without embedding retry logic in the function

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
```

## Example

```js
export default async (request) => {
  const body = await request.json();

  const response = await fetch("https://api.reqrun.com/v1/chat/completions", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "Authorization": `Bearer ${process.env.REQRUN_API_KEY}`,
    },
    body: JSON.stringify({
      model: "gpt-5-nano",
      messages: [{ role: "user", content: body.prompt }],
      wait: false,
    }),
  });

  return new Response(await response.text(), {
    status: response.status,
    headers: { "Content-Type": "application/json" },
  });
};
```

## Why ReqRun fits here

The function stays small and predictable while ReqRun handles the durability layer around the model request.
