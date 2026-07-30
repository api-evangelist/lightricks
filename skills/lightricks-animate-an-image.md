---
name: Animate an image into video with the LTX API
description: Upload a source image through the LTX pre-signed upload flow, submit an asynchronous
  image-to-video job referencing it, and retrieve the rendered clip.
api: openapi/lightricks-ltx-openapi.yml
base_url: https://api.ltx.video
operations:
- create-upload
- submit-image-to-video
- get-job-status
- image-to-video
generated: '2026-07-19'
method: generated
source: openapi/lightricks-ltx-openapi.yml, conventions/lightricks-conventions.yml, https://docs.ltx.io/input-formats
---

# Animate an image into video

## Before you start

- Authenticate with `Authorization: Bearer $LTXV_API_KEY`.
- An image input can be supplied either as a publicly reachable URL or as a `storage_uri` obtained from
  the LTX upload flow. Use the upload flow for anything private or not already on a public host.

## Steps

1. **Create an upload slot.** Call `create-upload` (`POST /v1/upload`). The response carries
   `upload_url` (a pre-signed URL), `storage_uri`, `expires_at` and `required_headers`.

2. **PUT the file.** Upload the image bytes to `upload_url` with an HTTP `PUT`, sending every header
   listed in `required_headers`. The pre-signed URL is valid for **1 hour** — generate it immediately
   before the upload, not ahead of a queue. The uploaded file is retrievable for 24 hours.

3. **Submit the generation.** Call `submit-image-to-video` (`POST /v2/image-to-video`) with the
   `storage_uri` from step 1 as the image input, plus `prompt`, `model`, `duration` and `resolution`.
   Optional `camera_motion` steers the movement. Success is `202` with a job `id`.

4. **Poll and download.** Call `get-job-status` (`GET /v2/image-to-video/{id}`) every 5 seconds until
   `status` is `completed` or `failed`. On success take `result.video_url` and download within the
   24-hour retention window.

## Rules

- **The endpoint in the poll path must match the submit path.** A job id created on
  `/v2/image-to-video` is only addressable at `GET /v2/image-to-video/{id}`; polling it under another
  endpoint returns 404.
- **Use LTX-2.3 models.** `ltx-2-3-fast` for iteration, `ltx-2-3-pro` for final output. Only the
  LTX-2.3 models support portrait geometry (`1080x1920`, `1440x2560`, `2160x3840`).
- **Handle 422 `content_filtered_error` as terminal.** The source image or prompt was rejected by
  safety filters; retrying the identical input will fail again.
- **Do not retry submits blindly** — there is no idempotency key, so every retry is a new billable job.
- Log `x-request-id` from each response for support traceability.
