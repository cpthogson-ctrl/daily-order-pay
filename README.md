# Daily Order Pay

A deploy-ready group food-ordering web app based on the earlier Daily Order concept, with online payment and date-by-date nominated collectors.

## Included

- **Today-first ordering page** with an **08:00 Europe/London cutoff**.
- **Preorder calendar** for future weekdays.
- **Automatic closure every Saturday and Sunday**.
- **Automatic England & Wales bank-holiday closure** using the official GOV.UK bank-holiday feed.
- **Per-date nominated collector** so Ben can nominate someone else in advance.
- **Manual date closure** for shutdowns or unavailable days.
- **Stripe Checkout** with card payments and eligible **Apple Pay / Google Pay**.
- **Stripe Connect** payout destination based on the nominated collector for that order date.
- **Supabase Auth** passwordless email login.
- Supabase tables for profiles, daily settings, paid orders and messaging.
- Light/dark mode.
- Demo mode when Supabase/Stripe environment variables are not configured.

## Free hosting stack

- Vercel Hobby: app hosting.
- Supabase Free: database/auth.
- Stripe: no monthly app fee, but normal payment processing / Connect fees apply.

## Setup

1. Create a Supabase project.
2. In Supabase SQL Editor, run `supabase/schema.sql`.
3. Copy `.env.example` to `.env.local` and add the Supabase keys.
4. Create a Stripe account and add `STRIPE_SECRET_KEY`.
5. In Stripe, enable Connect. Collectors use `/api/stripe/connect` to create/onboard an Express account.
6. Add a Stripe webhook pointing to `/api/stripe/webhook` and subscribe to:
   - `checkout.session.completed`
   - `checkout.session.expired`
7. Put its signing secret in `STRIPE_WEBHOOK_SECRET`.
8. Set `NEXT_PUBLIC_SITE_URL` to your deployed URL.
9. Create Ben's account, then promote it once using the SQL comment at the bottom of `supabase/schema.sql`.

## Run locally

```bash
npm install
npm run dev
```

Then open `http://localhost:3000`.

## Important payment behaviour

An order is created as `pending_payment`. It becomes `paid` only after Stripe's signed webhook confirms checkout completion. The nominated collector's connected Stripe account is used as the payment destination for that date.

For production, confirm your Stripe Connect configuration and fee model is appropriate for how your group uses the service before taking real payments.
