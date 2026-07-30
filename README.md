# Lightricks

Lightricks is a Jerusalem-based creative-technology company behind the Facetune, Photoleap and Videoleap
consumer apps and the LTX generative video platform. Its developer-facing product is the **LTX API** at
`api.ltx.video` — a video-generation API covering text-to-video, image-to-video, audio-to-video, retake,
extend, HDR upscale and reframe, offered in synchronous (v1) and asynchronous job-based (v2) forms and
billed per second of output. Lightricks also publishes the LTX-2 and LTX-Video open-weight models, a
ComfyUI node set and a LoRA trainer on GitHub under Apache-2.0.

- Website: https://www.lightricks.com/
- Developer docs: https://docs.ltx.io/
- API reference: https://docs.ltx.io/api-documentation/api-reference
- Console / sign up: https://console.ltx.io/
- Status: https://status.ltx.video/
- Trust center: https://trust.lightricks.com/
- GitHub: https://github.com/Lightricks

Backed by: insight-partners

## Artifacts

| Directory | What it holds |
|---|---|
| `openapi/` | The provider's OpenAPI 3.1 spec, harvested verbatim (14 operations, 33 schemas) |
| `overlays/` | API Evangelist Overlay 1.0.0 of our enhancements over the harvested spec |
| `llms/` | The provider's published `llms.txt`, verbatim |
| `well-known/` | `/.well-known/` probe index plus the RFC 9727 API catalog linkset |
| `mcp/` | The provider's live remote MCP documentation server |
| `packages/` | First-party PyPI packages and open-source repositories |
| `authentication/` | Auth profile derived from the spec's security schemes |
| `conventions/` | Cross-cutting runtime semantics (auth, async polling, tracing, errors, rate limits) |
| `errors/` | Error-type registry from the docs plus problem types derived from the spec |
| `rate-limits/` | Concurrency and request limits, and their 429 signaling |
| `lifecycle/` | Versioning, model deprecation policy, status page, retention windows |
| `changelog/` | Dated LTX model and ComfyUI plugin release history |
| `conformance/` | Standards conformance, asserted and derived, plus the published compliance program |
| `security/` | Domain security probe and the trust center with named certifications |
| `data-model/` | Entity graph derived from the spec (Job, JobResult, Upload, …) |
| `agentic-access/` | Recommended `x-agentic-access` execution contracts per operation |
| `skills/` | Packaged Agent Skills for the marquee LTX flows |

## Notes

- **No event surface.** LTX has no webhooks, streaming or AsyncAPI — job completion is discovered by
  polling `GET /v2/{endpoint}/{id}`. No AsyncAPI artifact is fabricated.
- **No OAuth.** Authentication is a bearer API key only, so there is no `scopes/` artifact.
- **No idempotency contract.** LTX documents no idempotency key; retried submits are new billable jobs.
- **No sandbox, CLI or embeddable components** are published, so those artifacts are absent by design.
- **No `security.txt` or vulnerability-disclosure program** was verifiable on any Lightricks/LTX host.
