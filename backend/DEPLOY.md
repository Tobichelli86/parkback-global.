# PARKBACK billing backend deployment (NOT DEPLOYED YET)

This is a backend starter implementation, not a completed live integration. It must be configured and tested before enabling purchases.

1. Create a Supabase project and execute schema.sql in SQL Editor. Enable email login and configure redirect URL to the published PARKBACK site.
2. Create a Cloudflare Workers account. Copy wrangler.toml.example to wrangler.toml. Set SUPABASE_URL and SUPABASE_ANON_KEY. Use `npx wrangler deploy` from backend/.
3. Add Worker secrets via `npx wrangler secret put STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, and `SUPABASE_SERVICE_ROLE_KEY`. Do not paste these into GitHub or the client.
4. In Stripe test mode create a webhook destination pointing to https://YOUR-WORKER.workers.dev/stripe-webhook and subscribe to checkout.session.completed and customer.subscription.created/updated/deleted. Set STRIPE_WEBHOOK_SECRET to the endpoint's signing secret.
5. Configure Stripe Billing customer portal and test subscriptions.
6. Integrate Supabase Auth in index.html, then call Worker endpoints /checkout, /portal and /entitlement using the logged-in user's bearer access token. NEVER infer paid status from a URL parameter.
7. Test all flows with Stripe test cards: payment success, failure, retry, cancellation, expiration, duplicate webhooks and webhook out-of-order events. Improve reconciliation for missed events and subscription state before live.
8. Add and test actual Premium features, privacy policy, terms, customer support, tax compliance, and business verification.
9. Create live-mode products/prices and live webhook destination separately after live Stripe account activation. Change Worker secrets and price IDs only after testing.

Security notes: This starter verifies Stripe webhook HMAC and Supabase user tokens server-side, and uses server-side plan/price mapping. It has not been end-to-end tested or deployed. The current browser app has no sign-in flow and still links to sandbox payment links.
