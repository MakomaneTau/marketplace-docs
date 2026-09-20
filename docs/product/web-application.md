# Web application

## Technology

- Next.js 16 App Router and React 19
- TypeScript in strict mode
- Tailwind CSS 4 with shared global/theme styles
- ESLint with the Next.js configuration
- Server-side gateway to the Express API

## Route inventory

| Area | Routes |
| --- | --- |
| Public marketplace | `/`, `/categories`, `/categories/:slug`, `/products/:id`, `/search` |
| Information | `/contact`, `/guidelines`, `/help`, `/privacy`, `/report`, `/safety`, `/terms` |
| Authentication | `/login`, `/signup`, `/signup/verification`, `/forgot-password`, `/reset-password` |
| Buyer account | `/cart`, `/favourites`, `/messages`, `/orders`, `/profile` |
| Seller workspace | `/seller`, `/seller/products`, `/seller/products/new`, `/seller/products/:id/edit`, `/seller/orders`, `/seller/messages`, `/seller/analytics`, `/seller/settings`, `/seller/shop` |

## Main code areas

| Path | Role |
| --- | --- |
| `app/components/` | Route-level reusable UI by domain: auth, catalogue, product, messaging, orders, seller, verification |
| `app/libs/api.ts` | API client and request/error handling |
| `app/libs/session-cookies.ts` | Session cookie names and secure cookie options |
| `app/hooks/use-auth.ts` | Session-aware client state |
| `app/data/` | Categories, products, and university data access helpers |
| `app/api/marketplace/[...path]/route.ts` | Same-origin API gateway |
| `proxy.ts` | Protected-route session refresh and seller-role authorization |

## Session boundary

On successful login or signup, the gateway converts upstream session data into HttpOnly access and refresh cookies, then removes the session payload from the browser response. Browser code calls `/api/marketplace`, while server configuration retains `MARKETPLACE_API_URL` without a `NEXT_PUBLIC_` prefix. Explicit bearer authorization remains possible for trusted non-browser consumers.

The gateway returns a controlled `503 MARKETPLACE_SERVICE_UNAVAILABLE` response when the upstream cannot be reached, and clears cookies after logout or an unrecoverable authenticated `401`.
