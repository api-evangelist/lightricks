---
name: Generate a video from a text prompt with the LTX API
description: Submit an asynchronous text-to-video job to the Lightricks LTX API, poll it to a terminal
  state, and download the rendered video before the 24-hour retention window closes.
api: openapi/lightricks-ltx-openapi.yml
base_url: https://api.ltx.video
operations:
- submit-text-to-video
- get-job-status
- text-to-video
generated: '2026-07-19'
method: generated
source: openapi/lightricks-ltx-openapi.yml, conventions/lightricks-conventions.yml, errors/lightricks-error-codes.yml
---

# Generate a video from a text prompt

## Before you start

- Get an API key from the developer console at https://console.ltx.io/ and read it from the
  `LTXV_API_KEY` environment variable. Never inline it, and never send it from client-side code.
- Every request carries `Authorization: Bearer $LTXV_API_KEY`.
- Prefer the asynchronous v2 surface (`submit-text-to-video`). The synchronous `text-to-video`
  operation returns `application/octet-stream` directly but can hit connection timeouts on long
  renders.

## Steps

1. **Choose a model and geometry.** Set `model` to `ltx-2-3-fast` for iteration or `ltx-2-3-pro` for a
   final render. `ltx-2-fast` and `ltx-2-pro` are deprecated — do not use them for new work. Pick a
   `resolution` / `fps` / `duration` combination the model actually supports; an unsupported
   combination fails with `invalid_request_error` (400), not a silent downgrade.

2. **Submit the job.** Call `submit-text-to-video` (`POST /v2/text-to-video`) with `prompt`, `model`,
   `duration`, `resolution` and optional `fps`. A success is `202 Accepted` with a body containing
   `id` and `created_at`. Persist that `id` — it is the only handle to the render.

3. **Poll for completion.** Call `get-job-status` (`GET /v2/text-to-video/{id}`) every **5 seconds**.
   Shorter intervals add load without reducing latency. `status` moves `pending` → `processing` →
   `completed` or `failed`; stop polling at either terminal state. Treat transient 5xx and network
   failures during polling as retryable — back off, do not abandon the job.

4. **Handle failure.** When `status` is `failed`, read `error.type` and `error.message`. Job failures
   use the same error types as HTTP errors, so one handler covers both — see
   `errors/lightricks-error-codes.yml`.

5. **Download the output.** When `status` is `completed`, `result.video_url` holds a signed URL.
   Download and re-host it immediately: job status and output URLs are retained for **24 hours** after
   the terminal state, after which `get-job-status` returns 404 `not_found_error`.

## Rules

- **No idempotency key exists.** Re-submitting is a new billable job with a new `id`. If a submit call
  times out, do not blindly retry — correlate your own request key with the returned `id` first.
- **Respect concurrency.** The default account limit is 2 simultaneous generations. Exceeding it
  returns 429 `concurrency_limit_error`. Queue submissions rather than fanning out.
- **Back off correctly.** On 429 honor the `Retry-After` header. For `api_error` (500),
  `service_unavailable` (503) and `overloaded_error` (529), use exponential backoff with jitter of up
  to 50% of the interval. `invalid_request_error`, `authentication_error`, `insufficient_funds_error`
  and `content_filtered_error` are **not** retryable — fix the request instead.
- **Log `x-request-id`** from every response; support needs it to trace a request.
- **Watch credits.** 402 `insufficient_funds_error` means the account is out of credits; enable auto
  top-up at https://docs.ltx.io/auto-top-up rather than failing renders in production.
- Billing is per second of generated video, and scales with resolution — a 4K second costs 4x a 1080p
  second on the same model.
