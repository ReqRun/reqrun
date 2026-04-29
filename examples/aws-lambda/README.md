# AWS Lambda

Suggested repo name: `reqrun-example-aws-lambda`

Use this when a Lambda should hand off LLM work durably and avoid retrying OpenAI directly inside the function.

## What this starter shows

- accept Lambda input
- submit the request to ReqRun
- return the accepted async result instead of hiding retries in Lambda

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
```

## Example

```js
export const handler = async (event) => {
  const body = JSON.parse(event.body ?? "{}");

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
      idempotency_key: body.jobId,
    }),
  });

  return {
    statusCode: response.status,
    headers: { "Content-Type": "application/json" },
    body: await response.text(),
  };
};
```

## Why ReqRun fits here

Lambda is a poor place to guess whether the upstream model call will finish, retry, or be interrupted. ReqRun gives the function a durable reliability boundary.
