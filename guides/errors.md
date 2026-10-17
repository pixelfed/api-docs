---
title: Errors
description: Pixelfed uses standard HTTP status codes and returns errors as JSON when you ask for JSON.
icon: exclamation-triangle
order: 4
---

::docs-section{id="errors"}
# Errors

Pixelfed uses standard HTTP status codes. Errors come back as JSON with an `error` string you can log or show, and validation failures add an `errors` object keyed by field.

:::docs-callout{icon="exclamation-triangle"}
Send `Accept: application/json` on every request. Without it, errors render as HTML pages, and a missing or expired token can redirect to the sign in page instead of returning `401`.
:::

| Status | What happened |
| --- | --- |
| `400` Bad request | The request could not be used. Some lookups also return this for unknown accounts. |
| `401` Unauthorized | The token is missing, expired or revoked. Send the person through authorization again. |
| `403` Forbidden | The token lacks the scope for this endpoint, the account is not allowed to do this, or the feature is turned off on this server. |
| `404` Not found | No such record, or it is not visible to this account. |
| `422` Unprocessable | Validation failed. `errors` lists the fields to fix. |
| `429` Too many requests | Slow down. Retry after the number of seconds in the `Retry-After` header. |
| `5xx` Server error | Something broke on the server. The body says `Server Error`. Retry with backoff. |

Some endpoints abort without a message, so `error` can be an empty string. Rely on the status code, not the text.

#code
:::response-panel
---
responses:
  - status: 401
    body:
      error: Unauthenticated.
  - status: 404
    body:
      error: Record not found
  - status: 422
    body:
      message: The status field is required.
      errors:
        status: [The status field is required.]
  - status: 429
    body:
      error: Too Many Attempts.
---
:::
::
