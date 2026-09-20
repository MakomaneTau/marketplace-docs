# Errors and integrations

## Error handling

HTTP status identifies the outcome class; the JSON error `code` is the stable programmatic contract. User interfaces should show safe, actionable messages and retain the request ID for support. Do not branch application logic on human-readable error text.

| Situation | Typical response | Client behavior |
| --- | --- | --- |
| Invalid request | `400` or `422` with validation code | Keep user input, highlight fields, correct and retry. |
| No valid session | `401` | Gateway refreshes when possible; otherwise redirect to login. |
| Authenticated but not permitted | `403` | Explain the role/ownership boundary without exposing data. |
| Resource absent/not visible | `404` | Present not-found state; do not infer resource existence. |
| State conflict | `409` | Refresh server state before retrying checkout or transition. |
| Rate limit | `429` plus retry headers | Back off using `Retry-After` where supplied. |
| Upstream unavailable | `503 MARKETPLACE_SERVICE_UNAVAILABLE` from gateway | Show retryable service state; retain unsent form data when safe. |

## Frontend integration contract

The browser communicates with `/api/marketplace/<api-v1-path>`. The Next.js route handler:

- forwards GET, POST, PUT, PATCH, DELETE, and request query strings;
- forwards selected `accept`, `content-type`, and `x-request-id` headers;
- preserves `X-Request-Id` and rate-limit headers from Express;
- applies `private, no-store` caching;
- injects session-backed authorization only when the request is marked as requiring authentication;
- handles login/signup session cookie issuance and logout/expired-session cleanup.

## Integration checklist

- Call the central API helper, not `fetch` with hard-coded upstream URLs.
- Use `/api/v1` endpoint names for API consumers; legacy aliases are transitional.
- Keep server-owned fields out of seller product creation payloads, including `shop_id` and managed `image_urls`.
- Respect stock ceilings in quantity controls; disabled checkout controls indicate availability rather than a client-side error.
- Keep buyer and seller affordances separate: buyers should not see seller-only actions, but both parties use the same conversation interface.
- For a new authenticated surface, add proxy protection and `no-store` behavior consistent with existing protected routes.
