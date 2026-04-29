# Developer Quickstart

ReqRun is the reliability layer between your app and OpenAI-compatible models.

Use it when a request must complete or fail visibly instead of disappearing
behind a timeout, process restart, or retry storm.

## Canonical integration path

- Base URL: `https://api.reqrun.com`
- Auth: `Authorization: Bearer REQRUN_LIVE_YOUR_PROJECT_KEY_HERE`
- Submit endpoint: `POST /v1/chat/completions`
- Status endpoint: `GET /v1/requests/{id}`

## First request

```bash
curl https://api.reqrun.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer REQRUN_LIVE_YOUR_PROJECT_KEY_HERE" \
  -d '{
    "model": "gpt-5-nano",
    "messages": [
      { "role": "user", "content": "Hello from ReqRun" }
    ],
    "wait": true,
    "idempotency_key": "quickstart-001"
  }'
```

## Async flow

For background jobs, submit with `wait=false`:

```bash
curl https://api.reqrun.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer REQRUN_LIVE_YOUR_PROJECT_KEY_HERE" \
  -d '{
    "model": "gpt-5-nano",
    "messages": [
      { "role": "user", "content": "Run in background" }
    ],
    "wait": false,
    "idempotency_key": "background-001"
  }'
```

If the response object is `chat.completion.async`, store the returned `rr_...`
request id and fetch its status later:

```bash
curl https://api.reqrun.com/v1/requests/rr_your_request_id_here \
  -H "Authorization: Bearer REQRUN_LIVE_YOUR_PROJECT_KEY_HERE"
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
