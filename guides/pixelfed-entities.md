---
title: Pixelfed entities
description: Add _pe to a Mastodon compatible endpoint to get Pixelfed's own Account, Status and Media schema back.
icon: arrows-right-left
order: 5
---

::docs-section{id="pixelfed-entities"}
# Pixelfed entities

Mastodon compatible endpoints return Mastodon shaped JSON by default, so existing client libraries parse them without changes. Pixelfed knows more about a post than Mastodon's schema has room for: the plain text caption, the place it was taken, tagged people, the media license, filters, and whether comments are turned off.

Add `_pe` (short for **Pixelfed entity**) to the request and the same endpoint returns Pixelfed's own schema instead. The URL, parameters and pagination stay the same. Only the shape of the Account, Status and Media objects in the response changes.

#### Sending it

The server only checks that the key is present, so any value works. `_pe=1` is the convention. Send it in the query string, including on `POST` requests like [Like a post](/api/statuses#like-post).

Endpoints that honour it carry a :docs-chip{kind="pe"} chip and list `_pe` under their query parameters.

:::docs-callout{icon="information-circle"}
Most Pixelfed only endpoints (`/api/v1.1`, `/api/v1.2`, `/api/v2.1`) already return Pixelfed entities and ignore `_pe`. The exceptions are aliases of Mastodon endpoints, like the [v1.1 home timeline](/api/timelines#home-timeline-v1-1).
:::

#code
```bash [Mastodon entity (default)]
curl https://pixelfed.social/api/v1/statuses/728341901556629504 \
  -H "Accept: application/json" \
  -H "Authorization: Bearer $TOKEN"
```

```bash [Pixelfed entity]
curl "https://pixelfed.social/api/v1/statuses/728341901556629504?_pe=1" \
  -H "Accept: application/json" \
  -H "Authorization: Bearer $TOKEN"
```
::

::docs-section{id="schema-differences"}
## What changes

Every field not listed here is the same in both shapes.

#### Account

| Field | Mastodon | Pixelfed (`_pe`) |
| --- | --- | --- |
| `note_text` | removed | Bio as plain text. |
| `website` `location` `pronouns` | removed | Profile fields Pixelfed stores natively. |
| `local` | removed | `true` when the account lives on this server. |
| `is_admin` | removed | `true` for admins of this server. |
| `has_story` | removed | `true` when the account has an active [story](/api/stories). |
| `header_bg` `last_fetched_at` | removed | Header colour, and when a remote account was last refreshed. |
| `avatar_static` `header` `header_static` | filled in | not sent |
| `bot` `emojis` `fields` `last_status_at` | `false`, `[]`, `[]`, `null` | not sent |

[Verify credentials](/api/accounts#verify-credentials) with `_pe` also adds a `settings` object with the account's display and compose preferences.

#### Status

| Field | Mastodon | Pixelfed (`_pe`) |
| --- | --- | --- |
| `replies_count` | Number of comments. | not sent, see `reply_count` |
| `reply_count` | removed | Number of comments. |
| `content_text` | removed | The caption as typed, without HTML. |
| `pf_type` | removed | `photo`, `photo:album`, `video`, `video:album`, `photo:video:album`, `text`, `reply`, `share` and a few story types. |
| `shortcode` | removed | Short ID used in legacy `/p/` URLs. |
| `place` | removed | The location attached to the post, or `null`. |
| `taggedPeople` | removed | Accounts tagged in the media. |
| `comments_disabled` | removed | `true` when the author turned comments off. |
| `liked_by` | removed | A preview of who liked the post. |
| `local` | removed | `true` when the post was made on this server. |
| `_v` `thread` `replies` `parent` | removed | Internal fields used by the web client. |

#### Media attachment

| Field | Mastodon | Pixelfed (`_pe`) |
| --- | --- | --- |
| `type` | From the MIME type: `image`, `video`, `audio`. | From the media kind, also `image`, `video` or `audio` for most files. |
| `mime` | removed | The full MIME type, like `image/jpeg`. |
| `optimized_url` | removed | URL of the server's optimized copy, when one exists. |
| `license` | removed | The license the author picked, or `null` for the default (all rights reserved). |
| `is_nsfw` | removed | Sensitive flag for this file. |
| `orientation` | removed | `portrait`, `landscape` or `square`. |
| `filter_name` `filter_class` | removed | The photo filter applied in the composer. |
| `hls_manifest` | removed | HLS playlist URL for transcoded video, when HLS is on. |

#code
```json [Mastodon entity]
{
  "id": "728341901556629504",
  "url": "https://pixelfed.social/p/dansup/728341901556629504",
  "content": "Golden hour at the pier <a href=\"https://pixelfed.social/discover/tags/film\">#film</a>",
  "replies_count": 3,
  "favourites_count": 41,
  "visibility": "public",
  "media_attachments": [
    {
      "id": "728341067224219648",
      "type": "image",
      "url": "https://pixelfed.social/storage/m/_v2/2/pier.jpg",
      "description": "Wooden pier at sunset, two people fishing at the far end"
    }
  ],
  "account": {
    "id": "2",
    "username": "dansup",
    "avatar_static": "https://pixelfed.social/storage/avatars/412/760/avatar.jpg",
    "bot": false,
    "fields": [],
    "emojis": []
  }
}
```

```json [Pixelfed entity]
{
  "id": "728341901556629504",
  "shortcode": "Bq3xW9pLm",
  "url": "https://pixelfed.social/p/dansup/728341901556629504",
  "content": "Golden hour at the pier <a href=\"https://pixelfed.social/discover/tags/film\">#film</a>",
  "content_text": "Golden hour at the pier #film",
  "pf_type": "photo",
  "reply_count": 3,
  "favourites_count": 41,
  "comments_disabled": false,
  "visibility": "public",
  "place": { "id": 2317, "slug": "tallinn", "name": "Tallinn", "country": "Estonia" },
  "taggedPeople": [],
  "local": true,
  "media_attachments": [
    {
      "id": "728341067224219648",
      "type": "image",
      "mime": "image/jpeg",
      "url": "https://pixelfed.social/storage/m/_v2/2/pier.jpg",
      "description": "Wooden pier at sunset, two people fishing at the far end",
      "license": null,
      "orientation": "portrait",
      "filter_name": null
    }
  ],
  "account": {
    "id": "2",
    "username": "dansup",
    "note_text": "Pixelfed developer",
    "pronouns": [],
    "website": "https://dansup.example",
    "local": true,
    "has_story": false
  }
}
```
::

::docs-section{id="pe-behaviour"}
## Things to know

#### Pagination keeps it

On timelines and bookmarks the `next` and `prev` URLs in the `Link` header already include `_pe=1`, so following them keeps the Pixelfed shape. See [Pagination](/api/pagination).

#### Shares are flattened

Some older Pixelfed clients do not read the nested `reblog` object, so with `_pe` a share has the shared post's `content`, `content_text`, `emojis`, `media_attachments`, `sensitive` and `spoiler_text` copied to the top level. `account` stays the person who shared it, and `reblog` is still there. Send `include_reblogs` alongside `_pe` to turn the copying off.

#### Other effects

[Search](/api/search#search) returns Pixelfed entities in `accounts` and `statuses`. [Get a hashtag](/api/search#get-hashtag) adds a `count` of posts with the tag. [Verify credentials](/api/accounts#verify-credentials) adds `settings`.

[Notifications](/api/notifications) accepts `_pe` without an error but ignores it, so notifications are always Mastodon shaped.

#### Endpoints that support it

| Area | Endpoints |
| --- | --- |
| [Accounts](/api/accounts) | `verify_credentials`, `update_credentials`, `accounts/{id}`, `lookup`, `{id}/statuses`, `{id}/followers`, `{id}/following`, `relationships` |
| [Statuses](/api/statuses) | `statuses/{id}`, `{id}/context`, create, `favourite`, `unfavourite`, `favourited_by`, `reblogged_by`, `bookmarks` |
| [Timelines](/api/timelines) | home (both paths), public, hashtag |
| [Search & hashtags](/api/search) | `v2/search`, `tags/{id}` |
| [Direct messages](/api/direct) | `direct/lookup` |

:::docs-callout{icon="server"}
Server admins can turn off the Mastodon shaping with `EXP_EMC=false`. On those servers the default response is already the Pixelfed entity (plus `replies_count`), so always read fields defensively.
:::

#code
```http [Response headers]
HTTP/1.1 200 OK
Content-Type: application/json
Link: <https://pixelfed.social/api/v1/timelines/home?limit=20&_pe=1&max_id=728341067224219648>; rel="next",
      <https://pixelfed.social/api/v1/timelines/home?limit=20&_pe=1&min_id=728341901556629504>; rel="prev"
```
::
