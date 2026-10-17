---
title: Pagination
description: Lists come back newest first and page by ID, not by offset.
icon: square-3-stack-3d
order: 3
---

::docs-section{id="pagination"}
# Pagination

Lists come back newest first and page by ID, not by offset, so new posts arriving mid-scroll never shift your results.

:::docs-param-list
---
title: Query parameters
params:
  - name: max_id
    type: string
    desc: Return results older than this ID. Use it to load the next page.
  - name: min_id
    type: string
    desc: Return results newer than this ID, closest first. Use it to fill a gap.
  - name: since_id
    type: string
    desc: Return results newer than this ID, newest first. Use it for pull to refresh.
  - name: limit
    type: integer
    desc: How many results to return per page.
---
:::

Every paginated response carries a `Link` header with ready made `next` and `prev` URLs. Follow those rather than building your own, and stop when `next` is missing.

#code
```http [Response headers]
HTTP/1.1 200 OK
Content-Type: application/json
Link: <https://pixelfed.social/api/v1/timelines/home?max_id=728341067224219648>; rel="next",
      <https://pixelfed.social/api/v1/timelines/home?min_id=728341901556629504>; rel="prev"
```
::
