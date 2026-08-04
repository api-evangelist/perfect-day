# Perfect Day

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

Perfect Day is a Berkeley, California food-technology company, founded in 2014, that makes
animal-free dairy proteins with precision fermentation. It ferments microflora carrying milk's
protein-coding genes to produce **ProFerm**, a non-animal whey protein with no lactose, cholesterol,
hormones or antibiotics, and sells it business-to-business to consumer brands making ice cream,
cream cheese, milk, yogurt, protein powders and bakery products.

- Website: https://perfectday.com/
- ProFerm: https://perfectday.com/proferm/
- Newsroom: https://perfectday.com/newsroom/ · Blog: https://perfectday.com/blog/
- Secondary-market listing (harvest source): https://forgeglobal.com/perfect-day_stock/

## API surface

**Perfect Day publishes no product or developer API.** No `api.`, `developer.` or `docs.`
subdomain resolves, no GitHub organization exists, no client libraries are published on npm, PyPI or
RubyGems, and no `/.well-known/` discovery document is served.

The one machine-readable surface Perfect Day operates is the **WordPress REST API (`wp/v2`)** behind
`https://perfectday.com/wp-json` — 19 namespaces, 260 routes, of which 124 are `wp/v2`. Anonymous
read access was verified across 16 resources on 2026-08-02, including two Perfect Day-specific
custom post types:

| Resource | Items | Notes |
|---|---|---|
| `news` | 103 | Newsroom — press coverage and announcements |
| `posts` | 71 | Blog |
| `pages` | 59 | Marketing and information pages |
| `success_story` | 4 | Partner and brand case studies |

A WordPress **MCP Adapter** endpoint is also live at `/wp-json/mcp/mcp-adapter-default-server`, but
anonymous `initialize` and `tools/list` both return `401 rest_forbidden` — it is platform
infrastructure, not a documented product MCP server, and no tool list is asserted here.

## Artifacts

| Directory | What it holds |
|---|---|
| `openapi/` | 16 OpenAPI 3.1.0 documents, 111 operations, **derived** from the site's own route metadata (verbatim source in `openapi/_original/`) |
| `overlays/` | OpenAPI Overlay 1.0.0 files carrying our enrichment metadata — the originals are never mutated |
| `authentication/` | Auth profile (HTTP Basic / WordPress application passwords) |
| `conventions/` | Pagination, filtering, field selection, error envelope, caching, versioning |
| `errors/` | Error catalog — the flat WordPress envelope, not RFC 9457 |
| `data-model/` | Entity-relationship graph derived from live payloads |
| `lifecycle/` | Versioning posture; no deprecation policy, SLA or status page exists |
| `conformance/` | Standards conformance, recorded honestly including the misses |
| `well-known/` | `/.well-known/` probe results (all 404) plus the alternate discovery that does work |
| `mcp/` | MCP server record (auth-gated) and the REST/MCP tool crosswalk |
| `skills/` | Three packaged Agent Skills grounded in real operationIds |
| `llms/` | Generated `llms.txt` (Perfect Day publishes none — `/llms.txt` returns 404) |
| `security/` | Domain security probe (TLS 1.3, no HSTS, no DNSSEC, SPF + DMARC `reject`) |

No `a2a/` agent card is present: `/.well-known/agent-card.json` and `/.well-known/agent.json` both
return 404, and agent cards are never authored on a provider's behalf.

Everything in this repo was either fetched from Perfect Day's live surface or derived from artifacts
that were. Nothing was fabricated.
