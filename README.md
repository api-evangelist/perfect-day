# Perfect Day

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
