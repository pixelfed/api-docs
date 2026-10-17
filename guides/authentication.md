---
title: Authentication
description: Pixelfed uses OAuth 2.0 with the authorization code flow.
icon: key
order: 2
endpoints: oauth
---

::docs-section{id="authentication"}
# Authentication

Pixelfed uses OAuth 2.0 with the authorization code flow. Your app never sees a password, and people can revoke access from their settings at any time.

1. [Register your app](#register-app) on the server to get a client ID and secret.
2. Send the person to `/oauth/authorize` on their server. After they approve, they are redirected back to you with a `code`.
3. [Exchange the code](#get-token) for an access token, then send it as `Authorization: Bearer <token>` on every request.

:::docs-callout{icon="server"}
Client credentials belong to one server. Register your app again for each new domain and store the credentials keyed by that domain.
:::

#### Scopes

Ask only for what you need. The approval screen lists every scope you request. If you leave `scope` out, you get `read write follow push`.

| Scope | Allows |
| --- | --- |
| `read` | Read the account, timelines, posts and notifications. |
| `write` | Publish, edit and delete posts, upload media, like and comment. |
| `follow` | Follow, unfollow, block and mute accounts. |
| `push` | Receive push notifications. |
| `security:read` | See which apps and devices have access to the account. Not granted by default, request it explicitly. |
| `security:write` | Change the password, revoke other apps and delete the account. Not granted by default. |
| `admin:read` `admin:write` | Moderation and server settings. Only granted to admin accounts. |

#code
```url [Authorization URL]
https://pixelfed.social/oauth/authorize
  ?client_id=1842
  &redirect_uri=tintype://oauth-callback
  &response_type=code
  &scope=read+write+follow
```

```url [Redirect back to your app]
tintype://oauth-callback
  ?code=def50200a91c4e7f3b
```
::
