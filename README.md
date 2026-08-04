# Lightricks

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
