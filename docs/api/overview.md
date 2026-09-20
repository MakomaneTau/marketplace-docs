# API conventions

## Base paths

| Path | Status | Use |
| --- | --- | --- |
| `/api/health` | Current | Lightweight Express process health check. |
| `/api/v1` | Current | Versioned application contract. |
| `/api/auth`, `/api/products` | Temporary | Compatibility aliases; new consumers should not adopt them. |

The web app normally consumes the same-origin proxy at `/api/marketplace/*`; it forwards to the API's `/api/v1/*` paths. Direct API consumers use the Express base URL followed by `/api/v1`.

## Response envelope

Successful versioned responses place the domain payload in `data`:

```json
{ "data": { "product": { "id": "…", "title": "Calculator" } } }
```

Errors use a stable error object and include a request ID:

```json
{
  "error": {
    "code": "PRODUCT_NOT_FOUND",
    "message": "The requested product could not be found.",
    "requestId": "…"
  }
}
```

The API also emits `X-Request-Id`. Capture that value when reporting failures.

## Authentication

Protected endpoints require:

```http
Authorization: Bearer <Supabase access token>
```

For browser requests via the Next.js gateway, use the client API helper's authenticated mode rather than reading or managing tokens in JavaScript. The gateway injects the HttpOnly cookie-backed bearer token and refreshes a session when appropriate.

## Pagination and filtering

Public product discovery supports search, category/filter, sort, and pagination parameters. Clients should preserve query strings when navigating pages and treat availability, ownership, and visibility as server-authoritative.

## Upload requests

Use `multipart/form-data` for product, shop, and seller-verification image endpoints. Do not set a manual multipart boundary. Verification image submissions are private; API responses do not expose object paths.
