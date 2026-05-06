# ReqRun TypeScript SDK

ReqRun is an LLM request reliability layer that durably runs OpenAI-compatible chat completion requests and lets you check their status.

Canonical package: `@reqrun/sdk`

If you searched for `reqrun`, the supported SDK and docs still live under `@reqrun/sdk`.

License: MIT for the published SDK package only. ReqRun's hosted backend, worker, dashboard, billing logic, and deployment stack are not included in this package.

## Install

```bash
npm install @reqrun/sdk
```

Docs: [https://www.reqrun.com/docs/sdk/typescript](https://www.reqrun.com/docs/sdk/typescript)

## Quickstart

```ts
import { ReqRun } from "@reqrun/sdk";

const reqrun = new ReqRun({
  apiKey: process.env.REQRUN_API_KEY!,
  signingSecret: process.env.REQRUN_SIGNING_SECRET!,
  baseURL: "https://api.reqrun.com",
});

const response = await reqrun.chat.completions.create({
  model: "gpt-5-nano",
  messages: [{ role: "user", content: "Hello" }],
});
```

If you are moving from the OpenAI SDK, keep the call shape familiar and swap the client:

```ts
// before
await openai.chat.completions.create(params);

// after
await reqrun.chat.completions.create(params);
```

## `wait=true`

Use `wait: true` when you want ReqRun to return the completed OpenAI-style response if it finishes within the wait timeout.

```ts
const response = await reqrun.chat.completions.create({
  model: "gpt-5-nano",
  messages: [{ role: "user", content: "Summarize this in one sentence." }],
  wait: true,
  idempotency_key: "summary-001",
});
```

## Async Fallback

If the request is accepted but not completed yet, ReqRun returns an async response with the ReqRun request id.

```ts
const response = await reqrun.chat.completions.create({
  model: "gpt-5-nano",
  messages: [{ role: "user", content: "Run a slower task." }],
  wait: false,
});

if (response.object === "chat.completion.async") {
  console.log(response.id, response.status);
}
```

## Request Status

Poll a request later with `requests.get()`.

```ts
const request = await reqrun.requests.get("rr_abc123");

console.log(request.status, request.attempts);

if (request.result) {
  console.log(request.result.choices[0]?.message.content);
}
```

## API Surface

- `new ReqRun({ apiKey, signingSecret?, baseURL? })`
- `reqrun.chat.completions.create(params)`
- `reqrun.requests.get(id)`

Project ownership is resolved from the API key. Hosted keys can also use `signingSecret` so a stolen bearer key alone is not enough to authorize requests. The SDK does not include dashboard/admin actions such as project creation or API key generation.
