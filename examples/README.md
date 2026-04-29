# ReqRun Example Starters

These starters show where ReqRun fits into real apps and services.

The first runnable standalone repos now live in the ReqRun GitHub org:

- [ReqRun/reqrun-example-nextjs-route-handler](https://github.com/ReqRun/reqrun-example-nextjs-route-handler)
- [ReqRun/reqrun-example-express-api](https://github.com/ReqRun/reqrun-example-express-api)
- [ReqRun/reqrun-example-fastapi-service](https://github.com/ReqRun/reqrun-example-fastapi-service)

The rest of this index stays as the roadmap for more split-out example repos.

## Suggested public repo lineup

1. `reqrun-example-nextjs-route-handler`
2. `reqrun-example-express-api`
3. `reqrun-example-fastapi-service`
4. `reqrun-example-cloudflare-worker`
5. `reqrun-example-vercel-function`
6. `reqrun-example-netlify-function`
7. `reqrun-example-supabase-edge-function`
8. `reqrun-example-aws-lambda`
9. `reqrun-example-github-actions`
10. `reqrun-example-cron-poller`

## Included starters

| Starter | Best fit | Public repo name |
| --- | --- | --- |
| [nextjs-route-handler](./nextjs-route-handler/README.md) | Durable handoff from a Next.js API route | `reqrun-example-nextjs-route-handler` |
| [express-api](./express-api/README.md) | Node API that should return quickly and poll later | `reqrun-example-express-api` |
| [fastapi-service](./fastapi-service/README.md) | Python backend using raw HTTP calls | `reqrun-example-fastapi-service` |
| [cloudflare-worker](./cloudflare-worker/README.md) | Edge worker that should not keep model calls open | `reqrun-example-cloudflare-worker` |
| [vercel-function](./vercel-function/README.md) | Serverless function that submits async work | `reqrun-example-vercel-function` |
| [netlify-function](./netlify-function/README.md) | Netlify function using fetch-based handoff | `reqrun-example-netlify-function` |
| [supabase-edge-function](./supabase-edge-function/README.md) | Edge function with durable job submission | `reqrun-example-supabase-edge-function` |
| [aws-lambda](./aws-lambda/README.md) | Lambda that should avoid direct model retries | `reqrun-example-aws-lambda` |
| [github-actions](./github-actions/README.md) | Workflow-triggered jobs with a durable request id | `reqrun-example-github-actions` |
| [cron-poller](./cron-poller/README.md) | Scheduled submit-and-poll flow | `reqrun-example-cron-poller` |

## Shared environment variables

Most starters assume:

```env
REQRUN_API_KEY=REQRUN_LIVE_YOUR_PROJECT_KEY_HERE
REQRUN_BASE_URL=https://api.reqrun.com
```

## How to split these into separate repos later

Each folder is meant to become its own repo without dragging the hosted product code along.

The clean split plan is:

1. copy the example folder into a new repo
2. add runtime-specific package files or deployment config
3. keep the README focused on that one platform
4. link back to ReqRun docs and the hosted API
