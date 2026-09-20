# Endpoint reference

`🔒` requires an authenticated bearer token. Seller endpoints also validate seller role and ownership.

## Authentication and profile

| Method | Endpoint | Purpose |
| --- | --- | --- |
| POST | `/auth/signup` | Create buyer or seller account. |
| POST | `/auth/login` | Exchange credentials for a session. |
| POST | `/auth/refresh` | Rotate refresh token. |
| POST | `/auth/forgot-password` | Request recovery without account enumeration. |
| POST | `/auth/reset-password` 🔒 | Set password with recovery access token. |
| POST | `/auth/logout` 🔒 | Revoke current user's sessions. |
| GET | `/auth/me` 🔒 | Read current user and marketplace profile. |
| GET / PATCH | `/profile` 🔒 | Read or update user-managed profile fields. |
| GET / POST | `/verifications/seller` 🔒 | Read latest seller verification or submit private documents. |

## Catalogue and reference data

| Method | Endpoint | Purpose |
| --- | --- | --- |
| GET | `/products` | Search/filter/sort/paginate active products from open shops. |
| GET | `/products/:id` | Read one public product. |
| POST | `/products` 🔒 | Create a product for authenticated seller's shop. |
| PATCH / DELETE | `/products/:id` 🔒 | Update or delete authenticated seller-owned product. |
| GET | `/categories` | List categories with public active-product counts. |
| GET | `/categories/:slug` | Read category by slug. |
| GET | `/universities` | Searchable universities with campus counts. |
| GET | `/universities/:slug` | University and campuses. |
| GET | `/universities/:slug/campuses` | Campuses for a university. |
| GET | `/shops/:slug` | Public open shop and its pickup areas. |
| GET | `/products/:id/reviews` | Public product reviews. |
| GET | `/shops/:slug/reviews` | Public shop reviews. |

## Buyer commerce

| Method | Endpoint | Purpose |
| --- | --- | --- |
| GET | `/favourites` 🔒 | List saved, currently public products. |
| PUT / DELETE | `/favourites/:productId` 🔒 | Idempotently save or remove a product. |
| GET / POST | `/orders` 🔒 | List buyer orders or atomically create one single-shop order. |
| GET | `/orders/:id` 🔒 | Read an order as participant. |
| PATCH | `/orders/:id/status` 🔒 | Buyer cancellation or valid seller transition. |
| POST | `/orders/:id/reviews` 🔒 | Create verified review after completed order. |

## Messaging and notifications

| Method | Endpoint | Purpose |
| --- | --- | --- |
| GET / POST | `/conversations` 🔒 | List conversations or start idempotent product conversation. |
| POST | `/conversations/orders/:orderId` 🔒 | Start/find an active-order delivery conversation. |
| GET | `/conversations/unread-count` 🔒 | Read unread message count. |
| GET | `/conversations/:id` 🔒 | Read participant-scoped conversation and messages. |
| POST | `/conversations/:id/messages` 🔒 | Send message and create recipient notification. |
| PATCH | `/conversations/:id/read` 🔒 | Mark incoming messages read. |
| GET | `/notifications` 🔒 | List user notifications, including unread filtering. |
| PATCH | `/notifications/:id/read` 🔒 | Mark one notification read. |
| PATCH | `/notifications/read-all` 🔒 | Mark all user notifications read. |

## Seller workspace

| Method | Endpoint | Purpose |
| --- | --- | --- |
| GET / POST / PATCH | `/seller/shop` 🔒 | Read, create, or update seller's single shop. |
| PUT | `/seller/shop/pickup-areas` 🔒 | Atomically replace pickup areas. |
| POST | `/seller/shop/logo`, `/seller/shop/banner` 🔒 | Replace public shop imagery. |
| GET / POST | `/seller/products` 🔒 | List seller inventory or create listing. |
| PATCH / DELETE | `/seller/products/:id` 🔒 | Update or delete owned listing. |
| POST | `/seller/products/:id/images` 🔒 | Append managed product image. |
| DELETE | `/seller/products/:id/images/:index` 🔒 | Remove managed image at ordered index. |
| GET | `/seller/orders` 🔒 | List orders for seller shop. |
| GET / PATCH | `/seller/orders/:id` / `/seller/orders/:id/status` 🔒 | Read participant order or transition it. |
| GET | `/seller/dashboard`, `/seller/analytics` 🔒 | Inventory/order/unread summaries and revenue/product analytics. |
| GET / PATCH | `/settings` 🔒 | Read or update notification preferences. |

All endpoints above are relative to `/api/v1`.
