---
name: Search Perfect Day site content
description: >-
  Run a cross-content-type search over perfectday.com — blog posts, pages, newsroom items and
  success stories — through the site's WordPress REST API, then hydrate the lightweight search hits
  into full records. Use this to answer questions about Perfect Day's ProFerm protein, process,
  applications or partners from primary source content.
api: openapi/perfect-day-search-openapi.yml
operations:
- getSearch
- getPosts
- getPostsId
- getPages
- getPagesId
- getNewsId
- getSuccessStoryId
generated: '2026-08-02'
method: generated
source: openapi/perfect-day-search-openapi.yml + conventions/perfect-day-conventions.yml
---

# Search Perfect Day site content

`https://perfectday.com/wp-json/wp/v2/search` is a single anonymous endpoint that searches across
every registered content type on perfectday.com. It returns a deliberately thin projection, so the
pattern is **search, then hydrate**.

## Authentication

None. Verified anonymous on 2026-08-02.

## Steps

1. **Search** — `getSearch` (`GET /wp/v2/search`).
   Parameters declared in the spec:
   - `search` — the query string (required in practice; an empty query returns everything)
   - `type` — `post` (all post-type content) or `term` (taxonomy terms)
   - `subtype` — narrow to a specific post type, e.g. `post`, `page`, `news`, `success_story`
   - `page` / `per_page` — standard pagination
   - `_fields=id,title,url,type,subtype` for the minimum useful payload

   A hit looks like `{id, title, url, type, subtype, _links}` — **title is a plain string here**, not
   the `{rendered: …}` object the full records use.

2. **Hydrate by subtype.** The search result gives you an `id` and a `subtype`; call the matching
   single-item operation:
   - `subtype: post` → `getPostsId` (`GET /wp/v2/posts/{id}`)
   - `subtype: page` → `getPagesId` (`GET /wp/v2/pages/{id}`)
   - `subtype: news` → `getNewsId` (`GET /wp/v2/news/{id}`)
   - `subtype: success_story` → `getSuccessStoryId` (`GET /wp/v2/success_story/{id}`)

   Alternatively follow the `self` entry inside the hit's `_links` object, which already carries the
   correct route.

3. **Prefer a filtered collection call when you know the type.** `getSearch` has no date filtering.
   If you want "posts about ProFerm since January", call `getPosts`
   (`GET /wp/v2/posts?search=ProFerm&after=2026-01-01T00:00:00`) instead — the collection routes
   accept `search`, `after`, `before`, `categories`, `orderby` and `order`.

4. **Read the body.** On hydrated records, `title.rendered`, `excerpt.rendered` and
   `content.rendered` are HTML. Strip tags and unescape entities before feeding them to a model.
   Ignore `yoast_head` (a full SEO `<head>` blob) unless you specifically want the meta description.

## Conventions that apply

- **Pagination**: `page` / `per_page`, `X-WP-Total`, `X-WP-TotalPages`, RFC 8288 `Link` header.
- **Field selection**: `_fields` for sparse responses, `_embed` to inline author/terms/featured
  media instead of making follow-up calls, `context=embed` for a smaller default projection.
- **Errors**: `{code, message, data.status}` — not RFC 9457. See
  `errors/perfect-day-problem-types.yml`.
- **Idempotency**: not supported; every step here is a safe `GET`.
- **Rate limits**: none published; no `RateLimit` headers observed. Respect the `Crawl-delay: 10`
  politeness signal in `robots.txt` and keep concurrency low.

## Scope note

This searches Perfect Day's *website content*. It is not a product, order, ingredient-spec or
supply-chain API — Perfect Day publishes none of those. Do not present content search results as if
they were a commercial API.
