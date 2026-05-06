# Agent Integration Guide

This guide is for coding agents, LLM-powered tools, and automated integrators.

## Use ReqRun when

- a model request must not disappear on retry or restart
- a caller needs a durable request id
- you need to poll completion later
- the same logical operation may be retried and should not duplicate upstream work

## Canonical ReqRun facts

- Hosted base URL: `https://api.reqrun.com`
- Auth: Bearer ReqRun project API key plus `X-ReqRun-Timestamp`, `X-ReqRun-Nonce`, and `X-ReqRun-Signature`
- Submit: `POST /v1/chat/completions`
- Status: `GET /v1/requests/{id}`
- Async request ids start with `rr_`
- `wait=true` tries fast-path completion
- `wait=false` returns async request state immediately
- `idempotency_key` deduplicates requests within the same project
- Signature source: the signing secret shown once when the project API key is created

## Request shape

Supported request fields:

- `model`
- `messages`
- `temperature`
- `max_tokens`
- `top_p`
- `presence_penalty`
- `frequency_penalty`
- `wait`
- `idempotency_key`

Not supported in v1:

- `stream=true`
- dashboard/admin actions through the public API
- provider routing features

## Integration rule

Treat ReqRun as the execution and reliability layer only.

Your application should still own:

- user auth
- app-specific persistence
- business state transitions
- UI polling or callbacks

## Minimal async example

```json
POST /v1/chat/completions
{
  "model": "gpt-5-nano",
  "messages": [
    { "role": "user", "content": "Run this durable task." }
  ],
  "wait": false,
  "idempotency_key": "agent-task-001"
}
```

If the response object is `chat.completion.async`, store `id` and later call:

```text
GET /v1/requests/{id}
```

Use the same signed headers for both calls.

## Best starter repos

- Next.js route handler: [ReqRun/reqrun-example-nextjs-route-handler](https://github.com/ReqRun/reqrun-example-nextjs-route-handler)
- Express API: [ReqRun/reqrun-example-express-api](https://github.com/ReqRun/reqrun-example-express-api)
- FastAPI service: [ReqRun/reqrun-example-fastapi-service](https://github.com/ReqRun/reqrun-example-fastapi-service)

## Source of truth

- Hosted docs: [https://www.reqrun.com/docs](https://www.reqrun.com/docs)
- API reference: [https://www.reqrun.com/docs/api](https://www.reqrun.com/docs/api)
- OpenAPI in this repo: [`../openapi.yaml`](../openapi.yaml)
