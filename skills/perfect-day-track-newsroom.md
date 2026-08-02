---
name: Track the Perfect Day newsroom
description: >-
  Pull Perfect Day's newsroom items (press coverage, partner launches, regulatory milestones) from
  the site's WordPress REST API, filter them by date and news category, and resolve each item's
  outbound publication link. Use this to monitor an alternative-protein company's announcements
  without scraping HTML.
api: openapi/perfect-day-news-openapi.yml
operations:
- getNews
- getNewsCategory
- getNewsCategoryId
- getMediaId
generated: '2026-08-02'
method: generated
source: openapi/perfect-day-news-openapi.yml + conventions/perfect-day-conventions.yml
---

# Track the Perfect Day newsroom

Perfect Day publishes no developer API. The one machine-readable surface it operates is the
WordPress REST API behind `https://perfectday.com/wp-json`. Newsroom items live in a custom post
type called `news` — 103 items as of 2026-08-02.

## Authentication

None required for reading. Every operation in this skill was verified to respond anonymously on
2026-08-02. Do **not** send credentials. Write operations exist in the spec but require a WordPress
application password and are out of scope here.

See `authentication/perfect-day-authentication.yml`.

## Steps

1. **List the news categories** — `getNewsCategory` (`GET /wp/v2/news_category`).
   Returns the taxonomy terms that classify newsroom items, each with a `count`. Observed:
   `Media Coverage` (slug `media-coverage`, 62 items).

2. **List newsroom items** — `getNews` (`GET /wp/v2/news`).
   Useful query parameters, all declared in the spec:
   - `per_page` (default `10`) and `page` (default `1`)
   - `after` / `before` — ISO 8601 date-time bounds; use `after` to pull only what is new since your
     last run
   - `news_category` — restrict to term IDs returned in step 1
   - `orderby` + `order` — sort by `date` `desc` for newest first
   - `_fields` — request only the fields you need, e.g.
     `_fields=id,date,slug,title,link,news_category`

3. **Page through the full set.** Read `X-WP-Total` and `X-WP-TotalPages` from the response headers
   to size the job, and follow the RFC 8288 `Link: …; rel="next"` header rather than incrementing
   `page` blindly. Both headers are exposed via CORS.

4. **Resolve the outbound link.** On `news` items the `link` field commonly points at the *external
   publication* that ran the story, not at perfectday.com. Treat it as the canonical source URL.
   The `title.rendered` and `content.rendered` fields carry HTML — unescape entities before use.

5. **Fetch the item's image if needed** — `getMediaId` (`GET /wp/v2/media/{id}`) using the item's
   `featured_media` ID. A value of `0` means no image is attached; skip the call.

6. **Fetch a single item** — `getNewsId` (`GET /wp/v2/news/{id}`) when you already hold an ID.

## Conventions that apply

- **Pagination**: page-number (`page` / `per_page`), `X-WP-Total`, `X-WP-TotalPages`, `Link` header.
- **Errors**: flat WordPress envelope `{code, message, data.status}` — **not** RFC 9457. A `400`
  means `rest_invalid_param`; check the parameter's declared enum/type in the spec. See
  `errors/perfect-day-problem-types.yml`.
- **Idempotency**: not supported. These are all safe `GET`s, so retry freely.
- **Rate limits**: none published and no `RateLimit` headers observed. `robots.txt` declares
  `Crawl-delay: 10` for the HTML site — mirror that politeness and keep concurrency low; the origin
  sits behind Cloudflare and a WP Engine cache.
- **Caching**: responses carry `x-cacheable` / `x-cache` edge headers; a poll interval measured in
  hours is more than enough for a newsroom.

## Do not

- Do not call `/wp/v2/users` — it is not registered on this site and returns `404 rest_no_route`.
  Use the denormalized `the_author` string on the item instead.
- Do not attempt writes. Anonymous `POST`/`PATCH`/`DELETE` return `401 rest_forbidden`.
