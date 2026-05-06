# FastAPI Service

Suggested repo name: `reqrun-example-fastapi-service`

Use this when a Python backend should submit durable LLM work through ReqRun instead of calling the model provider directly.

## Status

This folder is a docs-only reference.

For a working signed implementation, use:
- [ReqRun/reqrun-example-fastapi-service](https://github.com/ReqRun/reqrun-example-fastapi-service)

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
REQRUN_SIGNING_SECRET=REQRUN_SIGNING_SECRET_HERE
REQRUN_BASE_URL=https://api.reqrun.com
```

## Request shape

```py
body = {
    "model": "gpt-5-nano",
    "messages": [{"role": "user", "content": prompt}],
    "wait": False,
    "idempotency_key": job_id,
}

body_string = json.dumps(body, separators=(",", ":"), ensure_ascii=False)
path = "/v1/chat/completions"

response = await client.post(
    f"https://api.reqrun.com{path}",
    headers=build_signed_reqrun_headers(REQRUN_API_KEY, REQRUN_SIGNING_SECRET, "POST", path, body_string),
    content=body_string,
)
```

## Why ReqRun fits here

ReqRun keeps the Python service focused on app logic while the durable execution layer handles retries and result visibility.
