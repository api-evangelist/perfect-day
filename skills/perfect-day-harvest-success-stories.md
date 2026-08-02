---
name: Harvest Perfect Day partner success stories
description: >-
  Pull Perfect Day's published partner and customer case studies from the site's WordPress REST API,
  resolve their category taxonomy, and attach their imagery. Use this to build a picture of which
  consumer brands are shipping products made with Perfect Day's ProFerm protein.
api: openapi/perfect-day-success-stories-openapi.yml
operations:
- getSuccessStory
- getSuccessStoryId
- getSuccessStoryCategory
- getSuccessStoryCategoryId
- getMediaId
generated: '2026-08-02'
method: generated
source: openapi/perfect-day-success-stories-openapi.yml + conventions/perfect-day-conventions.yml
---

# Harvest Perfect Day partner success stories

Perfect Day sells its non-animal whey protein (ProFerm) business-to-business, and publishes the
resulting brand stories as a `success_story` custom post type on `https://perfectday.com/wp-json`.
Four items were live on 2026-08-02, across three taxonomy terms.

## Authentication

None. All read operations below were verified anonymous on 2026-08-02. Writes require a WordPress
application password and are out of scope.

## Steps

1. **List the taxonomy** — `getSuccessStoryCategory` (`GET /wp/v2/success_story_category`).
   Each term returns `id`, `name`, `slug`, `count`. Keep the `id → name` map for step 3.

2. **List the stories** — `getSuccessStory` (`GET /wp/v2/success_story`).
   - `per_page` up to `100`, `page` for the rest
   - `success_story_category` to restrict to a term ID
   - `orderby=date&order=desc` for newest first
   - `_fields=id,slug,title,content,success_story_category,featured_media,formatted_date` keeps the
     payload small; without it each item also carries large `acf`, `eight29_custom` and `yoast_head`
     blobs

3. **Join to the taxonomy.** Each item's `success_story_category` is an **array of term IDs**.
   Resolve them against step 1, or call `getSuccessStoryCategoryId`
   (`GET /wp/v2/success_story_category/{id}`) for a single term.

4. **Read the story body.** `title.rendered` and `content.rendered` are HTML strings. The `link`
   field may be `null` on this post type — the story is not always given a public permalink, so do
   not assume one exists.

5. **Attach imagery** — `getMediaId` (`GET /wp/v2/media/{id}`) with the item's `featured_media` ID.
   `featured_media: 0` means no image; the `featured_image` convenience field is `false` in that
   case. Skip the call rather than requesting media ID 0.

6. **Fetch one story** — `getSuccessStoryId` (`GET /wp/v2/success_story/{id}`).

## Conventions that apply

- **Pagination**: `page` / `per_page`, plus `X-WP-Total` and `X-WP-TotalPages` headers and an RFC
  8288 `Link` header. With four items you will not need to page, but read the total rather than
  assuming.
- **Errors**: `{code, message, data.status}`; not RFC 9457. `404 rest_post_invalid_id` for a bad ID.
  See `errors/perfect-day-problem-types.yml`.
- **Idempotency**: not supported; all steps here are safe `GET`s.
- **Rate limits**: none published. Keep concurrency low.

## Related

Cross-reference the brands you find here against the public partner page at
`https://perfectday.com/made-with-perfect-day/` — that page is served as HTML, and its content is
also addressable through `getPages` in `openapi/perfect-day-pages-openapi.yml`.
