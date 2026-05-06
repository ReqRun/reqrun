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

## License status

This public repository is not published under MIT or another open-source license.

It is a public product and documentation surface for ReqRun. If specific example
repos or a future SDK package are published under their own license, that
license will be stated in those artifacts directly.

## Hosted API quickstart

Today, the most stable public integration path is the hosted API at `https://api.reqrun.com`.

```ts
const body = {
  model: "gpt-5-nano",
  messages: [{ role: "user", content: "Hello from ReqRun" }],
  wait: true,
  idempotency_key: "hello-from-readme-001",
}

const bodyString = JSON.stringify(body)

const response = await fetch("https://api.reqrun.com/v1/chat/completions", {
  method: "POST",
  headers: await buildSignedReqRunHeaders({
    apiKey: process.env.REQRUN_API_KEY!,
    signingSecret: process.env.REQRUN_SIGNING_SECRET!,
    method: "POST",
    path: "/v1/chat/completions",
    bodyString,
  }),
  body: bodyString,
})

const data = await response.json()
console.log(data)
```

Hosted ReqRun keys now come with a signing secret. The bearer key and signing secret are both required for new hosted integrations.

## Async request flow

```ts
const response = await fetch("https://api.reqrun.com/v1/chat/completions", {
  method: "POST",
  headers: await buildSignedReqRunHeaders({
    apiKey: process.env.REQRUN_API_KEY!,
    signingSecret: process.env.REQRUN_SIGNING_SECRET!,
    method: "POST",
    path: "/v1/chat/completions",
    bodyString: JSON.stringify({
      model: "gpt-5-nano",
      messages: [{ role: "user", content: "Run this in the background." }],
      wait: false,
      idempotency_key: "job-123",
    }),
  }),
  body: JSON.stringify({
    model: "gpt-5-nano",
    messages: [{ role: "user", content: "Run this in the background." }],
    wait: false,
    idempotency_key: "job-123",
  }),
}).then((res) => res.json())

if (response.object === "chat.completion.async") {
  const request = await fetch(`https://api.reqrun.com/v1/requests/${response.id}`, {
    headers: await buildSignedReqRunHeaders({
      apiKey: process.env.REQRUN_API_KEY!,
      signingSecret: process.env.REQRUN_SIGNING_SECRET!,
      method: "GET",
      path: `/v1/requests/${response.id}`,
      bodyString: "",
    }),
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

For full signed helper implementations today, clone one of the runnable example repos or use the developer quickstart in [`./docs/dev-quickstart.md`](./docs/dev-quickstart.md).

## Public surfaces

- Marketing site: [www.reqrun.com](https://www.reqrun.com)
- Dashboard: [app.reqrun.com](https://app.reqrun.com)
- API: [api.reqrun.com](https://api.reqrun.com)
- Docs: [www.reqrun.com/docs](https://www.reqrun.com/docs)
- TypeScript SDK docs: [www.reqrun.com/docs/sdk/typescript](https://www.reqrun.com/docs/sdk/typescript)

## Repo docs for developers and agents

- Developer quickstart: [`./docs/dev-quickstart.md`](./docs/dev-quickstart.md)
- Agent integration guide: [`./docs/agent-guide.md`](./docs/agent-guide.md)
- OpenAPI contract: [`./openapi.yaml`](./openapi.yaml)

## Machine-readable surfaces

- Repo `llms.txt`: [`./llms.txt`](./llms.txt)
- Repo `llms-full.txt`: [`./llms-full.txt`](./llms-full.txt)
- Hosted `llms.txt`: [www.reqrun.com/llms.txt](https://www.reqrun.com/llms.txt)
- Hosted `llms-full.txt`: [www.reqrun.com/llms-full.txt](https://www.reqrun.com/llms-full.txt)

## Runnable example repos

These standalone repos are the best starting points today:

1. [ReqRun/reqrun-example-nextjs-route-handler](https://github.com/ReqRun/reqrun-example-nextjs-route-handler)
2. [ReqRun/reqrun-example-express-api](https://github.com/ReqRun/reqrun-example-express-api)
3. [ReqRun/reqrun-example-fastapi-service](https://github.com/ReqRun/reqrun-example-fastapi-service)
4. [ReqRun/reqrun-example-cloudflare-worker](https://github.com/ReqRun/reqrun-example-cloudflare-worker)
5. [ReqRun/reqrun-example-aws-lambda](https://github.com/ReqRun/reqrun-example-aws-lambda)
6. [ReqRun/reqrun-example-vercel-function](https://github.com/ReqRun/reqrun-example-vercel-function)

The broader examples index still lives in [`examples/`](./examples/README.md).

## Repo structure

This public repo currently contains:

- public product overview and quickstarts
- machine-readable `llms.txt` surfaces
- SDK shape and API usage documentation
- example starter references for future public repos

The hosted ReqRun backend, worker, dashboard, billing logic, and deployment
stack stay private. The public developer surface is the API, docs, and example
starters.

## Public repo plan

The intended ReqRun GitHub org structure lives in [`PUBLIC_REPOS.md`](./PUBLIC_REPOS.md).
