---
name: Retake and extend an existing video with the LTX API
description: Regenerate a section of an existing video or extend its duration using the LTX Pro-only
  editing endpoints, then poll the job and download the result.
api: openapi/lightricks-ltx-openapi.yml
base_url: https://api.ltx.video
operations:
- create-upload
- submit-retake
- submit-extend
- get-job-status
- retake
- extend
generated: '2026-07-19'
method: generated
source: openapi/lightricks-ltx-openapi.yml, https://docs.ltx.io/models, https://docs.ltx.io/pricing
---

# Retake and extend an existing video

## Before you start

- Authenticate with `Authorization: Bearer $LTXV_API_KEY`.
- **Both editing endpoints are Pro-only.** `retake` and `extend` accept `ltx-2-3-pro` (or the
  deprecated `ltx-2-pro`) — a Fast model is rejected with `invalid_request_error`.
- Supply the source video as a public URL or, preferably, via the upload flow.

## Steps

1. **Upload the source video.** Call `create-upload` (`POST /v1/upload`), `PUT` the video bytes to the
   returned `upload_url` with the `required_headers`, and keep the `storage_uri`. The URL expires in
   1 hour; the stored file lives 24 hours.

2. **Retake a section.** Call `submit-retake` (`POST /v2/retake`) with the source `video_uri`, the
   section to regenerate, a `prompt` describing the replacement, and `model: ltx-2-3-pro`. The `mode`
   field selects the edit behaviour — read the `EditVideoRequestMode` enum in the spec rather than
   guessing a value.

3. **Or extend the duration.** Call `submit-extend` (`POST /v2/extend`) with the source `video_uri` and
   `model: ltx-2-3-pro` to generate additional frames at the beginning or end.

4. **Poll to a terminal state.** `get-job-status` (`GET /v2/retake/{id}` or `GET /v2/extend/{id}`)
   every 5 seconds until `completed` or `failed`, then download `result.video_url` within 24 hours.

## Rules

- **Know what you are billed for.** Retake bills on the **duration of the input video**, not the
  regenerated section. Extend bills on the extended portion **plus context frames** from the input,
  capped at 505 billed frames total (~21 seconds at 24 fps). Both are $0.10 per second at 1080p.
- **Poll under the same endpoint you submitted to.** A retake job id is not addressable under
  `/v2/extend/{id}`.
- **No idempotency key.** A retried submit is a second billable edit — reconcile against the returned
  `id` before resubmitting.
- Concurrency counts editing jobs the same as generation jobs against the default limit of 2.
- On 429 honor `Retry-After`; retry only `rate_limit_error`, `concurrency_limit_error`, `api_error`,
  `service_unavailable` and `overloaded_error`.
