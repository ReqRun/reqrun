# ReqRun

ReqRun is the reliability layer between your app and OpenAI-compatible models.

It makes sure important requests complete or fail visibly instead of disappearing behind a timeout, retry storm, or process restart.

## Why ReqRun exists

Direct model calls are easy until they start failing in production.

ReqRun gives each request a durable lifecycle:

- accept work through an OpenAI-compatible API
- queue work instead of losing it
- retry transient failures
- deduplicate repeated submissions with idempotency keys
- return fast with `wait=true` when the result is ready
- expose status later by durable request id

## What ReqRun is for

Use ReqRun when:

- one failed request creates cleanup work
- an agent task must not stop halfway through
- retries must not produce duplicate side effects
- you need visibility into queued or retried work
- your app should survive restarts without losing request state

## What ReqRun is not

- not a provider marketplace
- not a workflow builder
- not an analytics suite
- not a model-routing platform

## Hosted API quickstart

Today, the most stable public integration path is the hosted API at `https://api.reqrun.com`.

```ts
const response = await fetch("https://api.reqrun.com/v1/chat/completions", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "Authorization": "Bearer REQRUN_LIVE_YOUR_PROJECT_KEY_HERE",
  },
  body: JSON.stringify({
    model: "gpt-5-nano",
    messages: [{ role: "user", content: "Hello from ReqRun" }],
    wait: true,
    idempotency_key: "hello-from-readme-001",
  }),
})

const data = await response.json()
console.log(data)
```

## Async request flow

```ts
const response = await fetch("https://api.reqrun.com/v1/chat/completions", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "Authorization": "Bearer REQRUN_LIVE_YOUR_PROJECT_KEY_HERE",
  },
  body: JSON.stringify({
    model: "gpt-5-nano",
    messages: [{ role: "user", content: "Run this in the background." }],
    wait: false,
    idempotency_key: "job-123",
  }),
}).then((res) => res.json())

if (response.object === "chat.completion.async") {
  const request = await fetch(`https://api.reqrun.com/v1/requests/${response.id}`, {
    headers: {
      "Authorization": "Bearer REQRUN_LIVE_YOUR_PROJECT_KEY_HERE",
    },
  }).then((res) => res.json())

  console.log(request.status)
}
```

## SDK status

The JavaScript SDK source lives in [`packages/sdk-js`](./packages/sdk-js).

Planned public package names:

- `@reqrun/sdk` as the canonical SDK
- `reqrun` as the reserved alias

Until the npm package is published, use the hosted API directly or build the SDK from source in this repo.

## Public surfaces

- Marketing site: [www.reqrun.com](https://www.reqrun.com)
- Dashboard: [app.reqrun.com](https://app.reqrun.com)
- API: [api.reqrun.com](https://api.reqrun.com)
- Docs: [www.reqrun.com/docs](https://www.reqrun.com/docs)
- TypeScript SDK docs: [www.reqrun.com/docs/sdk/typescript](https://www.reqrun.com/docs/sdk/typescript)

## Machine-readable surfaces

- Repo `llms.txt`: [`./llms.txt`](./llms.txt)
- Repo `llms-full.txt`: [`./llms-full.txt`](./llms-full.txt)
- Hosted `llms.txt`: [www.reqrun.com/llms.txt](https://www.reqrun.com/llms.txt)
- Hosted `llms-full.txt`: [www.reqrun.com/llms-full.txt](https://www.reqrun.com/llms-full.txt)

## Example starters

Ten example starters live in [`examples/`](./examples/README.md). Each one is written to be split into its own public GitHub repo later.

1. [Next.js route handler](./examples/nextjs-route-handler/README.md)
2. [Express API](./examples/express-api/README.md)
3. [FastAPI service](./examples/fastapi-service/README.md)
4. [Cloudflare Worker](./examples/cloudflare-worker/README.md)
5. [Vercel function](./examples/vercel-function/README.md)
6. [Netlify function](./examples/netlify-function/README.md)
7. [Supabase edge function](./examples/supabase-edge-function/README.md)
8. [AWS Lambda](./examples/aws-lambda/README.md)
9. [GitHub Actions](./examples/github-actions/README.md)
10. [Cron poller](./examples/cron-poller/README.md)

## Repo structure

This repo currently contains:

- hosted backend and worker
- hosted dashboard
- marketing site
- JavaScript SDK source
- example starters

The hosted product stack stays private. The public developer surface is the API, docs, and example starters.

## Public repo plan

The intended ReqRun GitHub org structure lives in [`PUBLIC_REPOS.md`](./PUBLIC_REPOS.md).
