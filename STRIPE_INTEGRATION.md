# PARKBACK GLOBAL — Stripe Payments + Billing

## Estado (sandbox)

- Stripe account: acct_1UaJDB98nYoktmkR (test only)
- Premium product: prod_VbWnrRCMfsPXPs; price: price_1UaJjq98nYoktmkRGqdlz8Kb (USD 2.99/month)
- Business product: prod_VbWnNjIoNEsSdv; price: price_1UaJju98nYoktmkR2kOOvFyE (USD 49/month)
- Test Checkout Payment Links in index.html; managed_payments disabled and automatic_tax disabled for sandbox links.
- **No real payments or premium entitlement provisioning.**

## Secure production architecture

1. Select a serverless backend (e.g. Cloudflare Workers, Supabase Edge Functions) and managed authentication. GitHub Pages cannot hold Stripe secret keys or process webhooks.
2. Store STRIPE_SECRET_KEY and STRIPE_WEBHOOK_SECRET in backend secret manager; never commit keys or embed them in the PWA.
3. Authenticated endpoint POST /checkout creates Stripe Checkout Session in subscription mode using server-side mapped price IDs. Never trust a client-supplied amount or price ID.
4. Webhook endpoint verifies Stripe signature against raw request body, deduplicates event IDs, handles checkout.session.completed, customer.subscription.created/updated/deleted, invoice.paid, invoice.payment_failed. Persist customer ID, subscription ID, status, current period end, user ID, plan, and event processing history.
5. Server-side entitlement endpoint maps authenticated user to current paid subscription. Do not grant paid access based on query parameters or browser localStorage.
6. Authenticated POST /billing-portal creates Stripe Customer Portal Session for an existing customer.
7. Make cancel-at-period-end and failed-payment behavior explicit; Stripe Billing recovery emails and customer portal configured in Dashboard.
8. Add real Premium features and business features, account login, privacy policy, terms, refund and support details before activating live charges.
9. Test Stripe Checkout with test cards, webhook retries, duplicate delivery, failed payments, cancellations, expiry, and restore access. Check tax obligations before live launch.
10. Switch to live Stripe account only after business verification and live product/price setup; never reuse test links in production.

## Current implementation limitations

PARKBACK currently stores parking entries locally on each device. There is no login, cloud sync, backend, paid entitlement or real billing. The visible links are sandbox demonstrations only. The ?payment=success query string is not proof of payment.
