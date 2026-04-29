# FastAPI Service

Suggested repo name: `reqrun-example-fastapi-service`

Use this when your Python backend wants to call ReqRun directly with `httpx`.

## What this starter shows

- accept application input in FastAPI
- submit durable LLM work through ReqRun
- return the accepted or completed result cleanly

## What you need

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
REQRUN_BASE_URL=https://api.reqrun.com
```

## Example

```python
import os
import httpx
from fastapi import FastAPI

app = FastAPI()

REQRUN_BASE_URL = os.environ.get("REQRUN_BASE_URL", "https://api.reqrun.com")
REQRUN_API_KEY = os.environ["REQRUN_API_KEY"]

@app.post("/run")
async def run(prompt: str):
    async with httpx.AsyncClient(timeout=30.0) as client:
        response = await client.post(
            f"{REQRUN_BASE_URL}/v1/chat/completions",
            headers={"Authorization": f"Bearer {REQRUN_API_KEY}"},
            json={
                "model": "gpt-5-nano",
                "messages": [{"role": "user", "content": prompt}],
                "wait": False,
            },
        )
        response.raise_for_status()
        return response.json()
```

## Why ReqRun fits here

FastAPI can stay focused on your application logic while ReqRun handles queueing, retries, and durable request status for the model call.
