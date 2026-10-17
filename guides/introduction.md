---
title: Introduction
description: Read timelines, upload photos, publish posts and build full clients on any Pixelfed server.
icon: book-open
order: 1
display: true
---

::docs-section{id="introduction"}
# Pixelfed API

Read timelines, upload photos, publish posts and build full clients on any Pixelfed server. The API follows the Mastodon client API and adds endpoints for the things only Pixelfed does, like collections, stories and direct messages.

#### One API, many servers

There is no central Pixelfed API. Every server hosts its own, and you send requests to the server the account lives on. Type your server into the **Your server** panel and every sample in these docs will use it.

#### Requests and responses

Send parameters as JSON or form data, and files as `multipart/form-data`. Responses are JSON. IDs are strings, even when they look like numbers, and timestamps are ISO 8601 in UTC. Sample responses are trimmed to the fields you are most likely to need.

#### Two kinds of endpoints

:docs-chip{kind="mastodon"} Behaves like the Mastodon endpoint of the same name, so existing client libraries work.

:docs-chip{kind="pixelfed"} Specific to Pixelfed. Admins can switch features off, so check `/api/v2.1/config` before relying on one.

:docs-chip{kind="pe"} Add `_pe=1` to get Pixelfed's own schema instead of the Mastodon one. See [Pixelfed entities](/api/pixelfed-entities).

#code
:::server-panel
:::

:::request-panel
---
path: /api/v2.1/config
auth: false
result: instance
---
:::

:::response-panel
---
responses:
  - status: 200
    body:
      version: 0.15.0
      oauth: true
      signup:
        curated: false
        mobile_registration: true
      features:
        stories: true
        video: true
        timelines:
          local: true
          network: true
      limits:
        account:
          max_avatar_size: 5000
          max_bio_length: 250
          max_name_length: 30
          min_password_length: 8
        collections:
          max_collection_length: 100
        direct_messages:
          max_characters: 2000
          max_media_attachments: 4
          group_chats:
            enabled: true
            max_participants: 10
        media:
          image_size_limit: 15360000
          max_album_limit: 20
          max_alt_text_length: 1000
          supported_mime_types: [image/jpeg, image/png, image/webp, video/mp4]
          video_size_limit: 15360000
        statuses:
          max_characters: 2000
---
:::
::