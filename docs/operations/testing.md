# Testing and troubleshooting

## Verification matrix

| Layer | Command or method | What it establishes |
| --- | --- | --- |
| API syntax | `npm.cmd run typecheck` | API JavaScript syntax validation. |
| API tests | `npm.cmd test` | Vitest/Supertest behavior. |
| API acceptance | `npm.cmd run acceptance:local` | Non-destructive local API acceptance workflow. |
| Database | `npm.cmd run supabase:lint` | Local schema linting. |
| Web lint | `npm.cmd run lint` | ESLint findings. |
| Web production | `npm.cmd run build` | Next.js production compilation. |
| Documentation | `py -m mkdocs build --strict` | Site config and internal documentation integrity. |
| User experience | Browser acceptance with buyer and seller accounts | Rendered UI, role boundaries, sessions, and end-to-end journeys. |

Source checks and builds are not visual proof. Validate important UI changes in a real rendered browser state.

## Two-account acceptance outline

1. Sign up and log in as a seller; submit verification, create a shop, choose pickup campuses, and publish a listing with an image.
2. In a separate browser profile, sign in as a buyer; search for the product, save it, message the seller, and place delivery and pickup orders.
3. As the seller, reply and move orders through `new → preparing → ready → completed`.
4. As the buyer, verify order history and submit a review for a completed purchase.
5. Reload pages to verify dashboard metrics, notifications, profile changes, and settings persistence.

## Common issues

| Symptom | First check |
| --- | --- |
| API returns `503` locally | Confirm Express is listening on `4000`; apply pending local migrations before treating Docker rebuilds as the fix. |
| Product or seller form returns `400` | Inspect client payload; do not send server-owned `shop_id` or managed `image_urls`. |
| Browser image optimizer returns `500` | Remote image allowlisting alone is insufficient; use the implemented unoptimized/fallback strategy where appropriate. |
| Protected page appears stale after Back | Check proxy `no-store`, auth refresh, and BFCache `pageshow` behavior. |
| GitHub Pages is raw/broken/404 | Confirm Pages source is GitHub Actions and workflow publishes generated `site/`, not repository root. |
| Signup/profile sync fails | Use sanitized structured API diagnostics and reconcile the applied Supabase migration history. |
