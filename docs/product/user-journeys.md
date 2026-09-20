# User journeys

## Buyer journey

```mermaid
flowchart LR
  A[Browse or search] --> B[View product]
  B --> C{Signed in?}
  C -->|No| D[Create account or log in]
  C -->|Yes| E[Save or message seller]
  E --> F[Build single-shop cart]
  F --> G[Choose delivery or campus pickup]
  G --> H[Place order]
  H --> I[Track order and delivery chat]
  I --> J[Review completed purchase]
```

Buyers can explore the catalogue, categories, product details, public shops, search, guidelines, safety, terms, privacy, help, contact, and reporting pages without a session. A session is required for favourites, messages, orders, profile actions, checkout, and reviews.

## Seller journey

```mermaid
flowchart LR
  A[Sign up as seller] --> B[Submit verification]
  B --> C[Create single shop]
  C --> D[Set pickup areas and shop imagery]
  D --> E[Create or edit listings]
  E --> F[Open shop and publish active inventory]
  F --> G[Receive orders and messages]
  G --> H[Advance fulfilment]
  H --> I[Review dashboard, analytics, settings]
```

Seller access is role-gated. A seller without a shop sees the setup-required flow rather than an assumed shop. Seller tooling includes inventory CRUD and images, shop profile and pickup-area configuration, order handling, shared conversation UI, analytics, and notification preferences.

## Order lifecycle

```mermaid
stateDiagram-v2
  [*] --> new: buyer creates order
  new --> preparing: seller accepts/prepares
  preparing --> ready: seller marks ready
  ready --> completed: seller completes
  new --> cancelled: buyer or seller cancels
  preparing --> cancelled: seller cancels when permitted
```

Orders are limited to one shop per checkout. The order creation workflow calculates totals and reserves stock atomically. Order items retain a name and unit-price snapshot so history remains meaningful if a product later changes or is deleted.
