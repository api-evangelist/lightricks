---
name: Upscale a video to HDR and reframe it with the LTX API
description: Run the async-only LTX post-production endpoints — HDR upscale and aspect-ratio reframe —
  and collect their differently-shaped job results.
api: openapi/lightricks-ltx-openapi.yml
base_url: https://api.ltx.video
operations:
- create-upload
- submit-video-to-video-hdr
- submit-video-to-video-reframe
- get-job-status
generated: '2026-07-19'
method: generated
source: openapi/lightricks-ltx-openapi.yml, https://docs.ltx.io/models, https://docs.ltx.io/pricing
---

# Upscale to HDR and reframe

## Before you start

- Authenticate with `Authorization: Bearer $LTXV_API_KEY`.
- **These two endpoints exist only on the async v2 surface** — there is no v1 synchronous equivalent.
- Both are Pro-only (`ltx-2-3-pro`).

## Steps

1. **Upload the source video** with `create-upload` (`POST /v1/upload`) and keep the `storage_uri`, or
   pass a public video URL.

2. **Upscale to HDR.** Call `submit-video-to-video-hdr` (`POST /v2/video-to-video-hdr`) to convert an
   SDR video to HDR. Billing is tiered by **input pixel count**, snapping up to the smallest tier that
   contains the input: $0.20/sec up to 1080p, $0.40/sec up to 1440p, $0.80/sec up to 4K.

3. **Or reframe.** Call `submit-video-to-video-reframe` (`POST /v2/video-to-video-reframe`) to retarget
   a video to a new aspect ratio, generating the missing areas. Reframe does **not** use the model
   resolution matrix — it takes a fixed output resolution whose short side is 720p or 1080p, in aspect
   ratios 1:1, 4:5, 5:4, 16:9 or 9:16. Billing is $0.10/sec at 720p and $0.20/sec at 1080p, on the
   **input** duration.

4. **Poll, then read the right result key.** `get-job-status` (`GET /v2/video-to-video-hdr/{id}` or
   `GET /v2/video-to-video-reframe/{id}`) every 5 seconds. `result` is a map of output label to URL and
   **the keys differ per endpoint**: reframe returns `video_url`, while HDR upscale returns
   `exr_frames_url`. Do not hardcode `video_url` across both — iterate the `result` map or branch on
   the endpoint.

## Rules

- **Never assume the result key.** `V2JobResult` is `additionalProperties: string/uri`; the available
  keys are endpoint-specific and documented per endpoint.
- Download within the 24-hour retention window or the job 404s.
- No idempotency key — a retried submit is a new billable job.
- Both endpoints count against the default concurrency limit of 2.
- Log `x-request-id` on every call.
