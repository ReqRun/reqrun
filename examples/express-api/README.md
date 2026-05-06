# Express API

Suggested repo name: `reqrun-example-express-api`

Use this when an Express service should accept work and immediately return a ReqRun request id.

## What this starter shows

- accept application input in Express
- submit durable LLM work through ReqRun
- return quickly with the async request object

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
REQRUN_SIGNING_SECRET=REQRUN_SIGNING_SECRET_HERE
REQRUN_BASE_URL=https://api.reqrun.com
```

## Example

```ts
import express from "express";

const app = express();
app.use(express.json());

app.post("/run", async (req, res) => {
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
        messages: [{ role: "user", content: req.body.prompt }],
        wait: false,
        idempotency_key: req.body.idempotencyKey,
      }),
    }
  );

  const result = await response.json();
  res.status(response.status).json(result);
});

app.listen(3000);
```

## Why ReqRun fits here

Express stays responsible for your app API. ReqRun becomes the reliable execution layer for the model request only.
