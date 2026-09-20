# System overview

## Runtime topology

The web app is the browser-facing boundary. It serves public pages, protected buyer pages, and seller pages. Its catch-all route handler forwards requests to Express, keeping browser traffic on a same-origin `/api/marketplace/*` path. Express owns the versioned application contract and uses Supabase for identity, PostgreSQL data, and object storage.

```mermaid
flowchart TB
  U[Buyer or seller] --> N[Next.js web application :3000]
  N -->|public routes| P[Public catalogue and information pages]
  N -->|protected route check| M[Next.js proxy]
  N -->|/api/marketplace/*| R[Route handler / API gateway]
  R -->|Bearer token injected from HttpOnly cookie| E[Express API :4000]
  E --> SA[Supabase Auth]
  E --> DB[(Supabase PostgreSQL)]
  E --> ST[Supabase Storage]
```

## Request path

1. The browser calls the Next.js same-origin gateway, never the configured upstream API URL directly.
2. For unsafe requests, the gateway checks that `Origin` matches the current host and protocol.
3. When a request declares authentication is required, the gateway reads the HttpOnly access cookie and can refresh it with the HttpOnly refresh cookie.
4. The gateway forwards selected request headers and an upstream bearer token to `/api/v1` on Express.
5. Express authenticates protected endpoints, applies rate limiting and security headers, invokes service-layer logic, and returns a normalized result.
6. Supabase enforces database constraints, row-level security, and storage policy beneath the API.

## API layering

```mermaid
flowchart LR
  RT[Route] --> CT[Controller]
  CT --> SV[Service]
  SV --> SB[Supabase clients]
  CT --> VA[Validator]
  RT --> AU[Authentication middleware]
  RT --> UP[Upload middleware]
  APP[app.js] --> MW[Context · logging · headers · rate limit · JSON]
  APP --> RT
  APP --> EH[Not-found and error handlers]
```

Routes compose HTTP concerns; controllers translate requests and responses; services contain domain operations and data access. `app.js` mounts `/api/v1` and the temporary compatibility endpoints, while `server.js` loads environment configuration and starts the listener.

## Web route groups

The App Router uses route groups to organize layouts without changing URLs:

| Group | URL examples | Purpose |
| --- | --- | --- |
| `(public)` | `/`, `/categories`, `/products/:id`, `/search` | Catalogue and static information |
| `(auth)` | `/login`, `/signup`, `/forgot-password` | Account recovery and entry |
| `(marketplace)` | `/cart`, `/favourites`, `/messages`, `/orders`, `/profile` | Signed-in buyer surfaces |
| `(seller)` | `/seller`, `/seller/products`, `/seller/orders` | Seller workspace |

The proxy protects signed-in routes and additionally checks `profile.role === "seller"` before seller routes are served. Responses for protected pages are marked `private, no-store` to reduce stale back-navigation and BFCache exposure.
