# Developer Quickstart

ReqRun is the reliability layer between your app and OpenAI-compatible models.

Use it when a request must complete or fail visibly instead of disappearing
behind a timeout, process restart, or retry storm.

## Canonical integration path

- Base URL: `https://api.reqrun.com`
- Credentials: `REQRUN_API_KEY` plus `REQRUN_SIGNING_SECRET`
- Submit endpoint: `POST /v1/chat/completions`
- Status endpoint: `GET /v1/requests/{id}`
- New hosted keys require signed requests on every call

## First request

```ts
const encoder = new TextEncoder()

function toHex(buffer: ArrayBuffer) {
  return [...new Uint8Array(buffer)].map((byte) => byte.toString(16).padStart(2, "0")).join("")
}

async function sha256Hex(value: string) {
  return toHex(await crypto.subtle.digest("SHA-256", encoder.encode(value)))
}

async function hmacHex(secret: string, value: string) {
  const key = await crypto.subtle.importKey("raw", encoder.encode(secret), { name: "HMAC", hash: "SHA-256" }, false, ["sign"])
  return toHex(await crypto.subtle.sign("HMAC", key, encoder.encode(value)))
}

async function buildSignedReqRunHeaders({
  apiKey,
  signingSecret,
  method,
  path,
  bodyString,
}: {
  apiKey: string
  signingSecret: string
  method: "GET" | "POST"
  path: string
  bodyString: string
}) {
  const timestamp = new Date().toISOString()
  const nonce = crypto.randomUUID().replace(/-/g, "")
  const bodyHash = await sha256Hex(bodyString)
  const signaturePayload = [method, path, timestamp, nonce, bodyHash].join("\n")
  const signature = await hmacHex(signingSecret, signaturePayload)

  return {
    "Content-Type": "application/json",
    "Authorization": `Bearer ${apiKey}`,
    "X-ReqRun-Timestamp": timestamp,
    "X-ReqRun-Nonce": nonce,
    "X-ReqRun-Signature": `v1=${signature}`,
  }
}

const path = "/v1/chat/completions"
const body = {
  model: "gpt-5-nano",
  messages: [{ role: "user", content: "Hello from ReqRun" }],
  wait: true,
  idempotency_key: "quickstart-001",
}

const bodyString = JSON.stringify(body)

const response = await fetch(`https://api.reqrun.com${path}`, {
  method: "POST",
  headers: await buildSignedReqRunHeaders({
    apiKey: process.env.REQRUN_API_KEY!,
    signingSecret: process.env.REQRUN_SIGNING_SECRET!,
    method: "POST",
    path,
    bodyString,
  }),
  body: bodyString,
})
```

## Async flow

For background jobs, submit with `wait=false`:

```ts
const asyncBody = {
  model: "gpt-5-nano",
  messages: [{ role: "user", content: "Run in background" }],
  wait: false,
  idempotency_key: "background-001",
}

const asyncBodyString = JSON.stringify(asyncBody)

const response = await fetch("https://api.reqrun.com/v1/chat/completions", {
  method: "POST",
  headers: await buildSignedReqRunHeaders({
    apiKey: process.env.REQRUN_API_KEY!,
    signingSecret: process.env.REQRUN_SIGNING_SECRET!,
    method: "POST",
    path: "/v1/chat/completions",
    bodyString: asyncBodyString,
  }),
  body: asyncBodyString,
}).then((res) => res.json())
```

If the response object is `chat.completion.async`, store the returned `rr_...`
request id and fetch its status later:

```ts
const request = await fetch("https://api.reqrun.com/v1/requests/rr_your_request_id_here", {
  headers: await buildSignedReqRunHeaders({
    apiKey: process.env.REQRUN_API_KEY!,
    signingSecret: process.env.REQRUN_SIGNING_SECRET!,
    method: "GET",
    path: "/v1/requests/rr_your_request_id_here",
    bodyString: "",
  }),
}).then((res) => res.json())
```

## Choose a starter repo

- Next.js: [ReqRun/reqrun-example-nextjs-route-handler](https://github.com/ReqRun/reqrun-example-nextjs-route-handler)
- Express: [ReqRun/reqrun-example-express-api](https://github.com/ReqRun/reqrun-example-express-api)
- FastAPI: [ReqRun/reqrun-example-fastapi-service](https://github.com/ReqRun/reqrun-example-fastapi-service)

## Product boundary

ReqRun does:

- queue requests durably
- retry transient failures
- deduplicate repeated logical work by idempotency key
- expose request ids and later status

ReqRun does not do:

- streaming in v1
- provider marketplace routing
- workflow orchestration
- analytics product surfaces
