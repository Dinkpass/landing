# DinkPass — Engineering Requirements

> **Audience:** Engineers building the DinkPass MVP.
> **Scope:** Web app (PWA) covering two features — *find & book a court* and *pay*.
> **Status:** Source of truth. Update via PR.

---

## 1. Overview

DinkPass is a court-booking and subscription platform for pickleball players in Đà Nẵng (launch city). A member buys a Day Pass or City Pass and books open-play slots at any partner club from a single app. Clubs publish availability via a simple admin and receive per-visit payouts.

**MVP delivers** the smallest end-to-end loop:
1. Member signs in with phone OTP
2. Browses clubs and live slot availability
3. Books a slot
4. Pays via PayOS (Day Pass) or has an active City Pass
5. Receives a QR confirmation
6. Club staff verifies QR at check-in
7. Booking is recorded; payout accrues to the club

Everything else (DUPR, lessons, leagues, multi-city, guest passes, loyalty) is **post-MVP**.

---

## 2. Architecture

```
                ┌──────────────────┐
                │   Next.js (PWA)  │  member + club-admin UI
                │   on Vercel      │  server actions for mutations
                └────────┬─────────┘
                         │
            ┌────────────┴───────────┬─────────────┐
            ▼                        ▼             ▼
     ┌─────────────┐          ┌────────────┐  ┌──────────┐
     │  Supabase   │          │   PayOS    │  │  Resend  │
     │ Postgres +  │          │ Payment    │  │ Email    │
     │ Auth + RLS  │          │ webhooks   │  │ receipts │
     └─────────────┘          └────────────┘  └──────────┘
```

- **Frontend:** Next.js 15 App Router, TypeScript, Tailwind, shadcn/ui, server actions for all mutations.
- **Backend:** Supabase Postgres for data, Supabase Auth for phone OTP, Supabase Realtime for live slot updates, Row-Level Security for authorization.
- **Payments:** PayOS for VND collection. Webhook → Next.js route handler → DB state machine.
- **Transactional email:** Resend.
- **Hosting:** Vercel (web) · Supabase (DB) · both auto-deploy from `main`.

---

## 3. Tech stack (locked)

| Layer | Tool | Notes |
|---|---|---|
| Framework | Next.js 15 (App Router) | React 19, server actions |
| Language | TypeScript (strict mode) | |
| Styling | Tailwind v3 + shadcn/ui | Match landing-page brand voice |
| DB | Supabase Postgres (Singapore region) | PostGIS for distance queries |
| Auth | Supabase Auth (phone OTP) | Google sign-in optional, post-MVP |
| Realtime | Supabase channels | Subscribe to `slots` updates |
| Validation | Zod | Server actions + form parsing |
| Forms | React Hook Form | |
| Date/time | Day.js (timezone: `Asia/Ho_Chi_Minh`) | All slot times stored as `timestamptz` |
| Email | Resend | Booking receipts, OTP fallback |
| SMS | Stringee / eSMS / VietGuys (decide week 1) | OTP delivery for Vietnam numbers |
| Payments | PayOS (primary) | Stripe optional for foreign cards |
| Analytics | PostHog | Self-host later if needed |
| Hosting | Vercel | Preview deploy per PR |
| Source control | GitHub (`Dinkpass` org) | `main` is always deployable |

---

## 4. Data model

```sql
-- Users (members, club admins, super admins)
create table users (
  id            uuid primary key default gen_random_uuid(),
  phone         text unique not null,                -- E.164 format
  email         text,
  display_name  text,
  role          text not null default 'member',      -- 'member' | 'club_admin' | 'admin'
  created_at    timestamptz not null default now()
);

-- Clubs (curated partner facilities)
create table clubs (
  id            uuid primary key default gen_random_uuid(),
  name          text not null,
  slug          text unique not null,
  district      text not null,                       -- 'Sơn Trà', 'Hải Châu', 'Ngũ Hành Sơn', 'Thanh Khê', etc.
  address       text not null,
  location      geography(point, 4326),              -- PostGIS
  photos        text[] not null default '{}',
  amenities     text[] not null default '{}',        -- ['indoor','shower','parking',...]
  active        boolean not null default true,
  created_at    timestamptz not null default now()
);

-- Courts (a club has many courts)
create table courts (
  id            uuid primary key default gen_random_uuid(),
  club_id       uuid not null references clubs(id) on delete cascade,
  name          text not null,                       -- 'Court 1', 'Sân A'
  indoor        boolean not null default true,
  surface       text not null default 'concrete'     -- 'concrete' | 'asphalt' | 'sport-court'
);

-- Bookable slots (the source of truth for availability)
create table slots (
  id              uuid primary key default gen_random_uuid(),
  court_id        uuid not null references courts(id) on delete cascade,
  start_at        timestamptz not null,
  end_at          timestamptz not null,
  base_price_vnd  integer not null,                  -- club's listed price
  status          text not null default 'open',      -- 'open' | 'booked' | 'blocked'
  created_at      timestamptz not null default now(),
  unique (court_id, start_at)
);

-- Bookings
create table bookings (
  id            uuid primary key default gen_random_uuid(),
  slot_id       uuid not null unique references slots(id),
  user_id       uuid not null references users(id),
  payment_id    uuid references payments(id),
  status        text not null default 'pending',     -- 'pending' | 'confirmed' | 'cancelled' | 'no_show'
  created_at    timestamptz not null default now(),
  cancelled_at  timestamptz
);

-- Payments (subscription purchases or per-booking charges)
create table payments (
  id              uuid primary key default gen_random_uuid(),
  user_id         uuid not null references users(id),
  amount_vnd      integer not null,
  type            text not null,                     -- 'day_pass' | 'city_pass' | 'booking'
  provider        text not null default 'payos',
  provider_ref    text not null,                     -- PayOS order code
  status          text not null default 'pending',   -- 'pending' | 'paid' | 'expired' | 'failed' | 'refunded'
  raw_payload     jsonb,                             -- last webhook body
  created_at      timestamptz not null default now(),
  paid_at         timestamptz
);

-- Pass entitlements derived from paid 'city_pass' payments
create table passes (
  id            uuid primary key default gen_random_uuid(),
  user_id       uuid not null references users(id),
  payment_id    uuid not null references payments(id),
  type          text not null,                       -- 'day_pass' | 'city_pass'
  starts_at     timestamptz not null,
  expires_at    timestamptz not null,
  created_at    timestamptz not null default now()
);

-- Indexes
create index slots_court_start_idx on slots(court_id, start_at);
create index slots_open_idx on slots(start_at) where status = 'open';
create index bookings_user_idx on bookings(user_id, created_at desc);
create index payments_provider_ref_idx on payments(provider_ref);
create index clubs_location_idx on clubs using gist (location);
```

---

## 5. Authentication

- **Method:** Phone-OTP via Supabase Auth, Vietnamese SMS provider (decide between Stringee/eSMS/VietGuys week 1).
- **Session:** Supabase JWT in HTTP-only cookie. Refresh handled client-side.
- **Role assignment:** Defaults to `member`. Promotion to `club_admin` is manual via DB until Week 4+.
- **Account merge / email backup:** post-MVP.

---

## 6. Feature requirements

### 6.1 Member: Find a court

- **List + map view** of clubs filtered by:
  - District (multi-select)
  - Indoor / outdoor / both
  - Distance from user's geolocation (optional)
  - Has open slots **today** / **this week**
- **Live availability:** subscribe to `slots` channel; UI updates without page reload.
- **Club detail** page shows:
  - Photos, amenities, address, district, distance
  - Today + next 6 days as date tabs
  - Slots grouped by court — each slot shows time, price, "Book" button
- **Empty / loading / error states** required for every screen.

### 6.2 Member: Book a slot

- **Booking flow:**
  1. Tap a slot → confirm modal (court, time, price, terms)
  2. If user has an **active City Pass** → free; create booking immediately
  3. Else → redirect to PayOS payment link (Day Pass equivalent: slot price)
  4. On PayOS success webhook → mark `payment.status='paid'`, `booking.status='confirmed'`
  5. Send email + SMS receipt with QR code (signed booking ID)
- **Cancellation:** free up to 2 hours before slot start; full refund.
- **No-show:** flagged after slot end + 30 min if no check-in scan; counts toward fair-use cap.

### 6.3 Member: Subscription purchase

- **Day Pass** (199.000 ₫) — entitles 1 booking within 24 h.
- **City Pass** (990.000 ₫) — unlimited bookings for 30 days, with per-club fair-use cap (configurable, default 6 visits/club/month).
- **Manual renewal:** 3 days before `expires_at`, send reminder email + SMS with fresh PayOS link. No card-on-file in MVP.
- **Founding-member discount:** 50% off for life — apply via promo code at checkout (single hardcoded code OK for MVP).

### 6.4 Club admin: Schedule editor

- **Weekly grid** (7 days × hourly rows) per court.
- Click a cell → toggle status: `open` ↔ `blocked`, edit `base_price_vnd`.
- Bulk: "open every weekday 6 AM – 10 PM at 150K ₫" template button.
- Slots are generated nightly by a Supabase scheduled function for the next 14 days.

### 6.5 Club admin: Booking ledger

- Today's bookings (member name redacted to first name + last initial; full data only to admins of that club).
- Today's gross + net payout estimate.
- Manual "no-show" mark.

---

## 7. Payments integration (PayOS)

### 7.1 Create payment link
On booking confirm or pass purchase:

```
POST https://api-merchant.payos.vn/v2/payment-requests
{
  "orderCode": <int, store as payments.provider_ref>,
  "amount":    <int VND>,
  "description": "DinkPass: City Pass HCM",
  "returnUrl":   "https://dinkpass.io/payment/return",
  "cancelUrl":   "https://dinkpass.io/payment/cancel",
  "signature":   <HMAC-SHA256 of params with PAYOS_CHECKSUM_KEY>
}
```

Persist `payments` row with `status='pending'` before redirecting the user.

### 7.2 Webhook
- Endpoint: `POST /api/payos/webhook`
- Verify HMAC signature against `PAYOS_CHECKSUM_KEY`. **Reject if missing or invalid.**
- Idempotent — keyed by `payments.provider_ref`. Repeat webhooks must not double-confirm.
- On `code === '00'`: update `payments.status='paid'`, `payments.paid_at=now()`. Trigger booking confirmation or pass creation.
- On other codes: `failed` or `expired`. Booking remains `pending` until expiry sweep cancels it.

### 7.3 Refunds
- Manual via PayOS dashboard for MVP; reflected in DB by ops via internal admin route. Full automation post-MVP.

### 7.4 State machine

```
booking.status:    pending → confirmed → cancelled | no_show
payment.status:    pending → paid | expired | failed → refunded
pass.status:       active until expires_at
```

---

## 8. Concurrency & integrity

- **No double-booking guarantee** comes from a single atomic SQL update:

  ```sql
  update slots set status = 'booked'
  where id = $1 and status = 'open'
  returning id;
  ```
  If 0 rows returned → slot was taken; show "Just got booked" and refresh.
- **Pending bookings expire** after 15 minutes if payment not completed → `slots.status` reverts to `open`. Run via Supabase Cron every minute.
- All time stored as `timestamptz`, displayed in `Asia/Ho_Chi_Minh`.

---

## 9. Authorization (Row-Level Security)

| Table | Member | Club admin | Admin |
|---|---|---|---|
| `users` | own row | members of own club via bookings | all |
| `clubs` | active rows | own club | all |
| `courts` | active rows | own club | all |
| `slots` | read all open | full CRUD on own club | all |
| `bookings` | own bookings | bookings at own club | all |
| `payments` | own | none | all |
| `passes` | own | none | all |

All policies expressed as Supabase RLS — never trust client-side checks alone.

---

## 10. API surface (server actions)

```
/* member */
signInWithPhone(phone)
verifyOtp(phone, code)
listClubs({ district?, indoor?, near? })
getClub(slug)
listSlots(courtId, dateRange)
createBooking(slotId)               // returns paymentLink or { ok: true } if pass active
cancelBooking(bookingId)
purchasePass(type)                  // returns paymentLink

/* club admin */
listMySchedule(weekOf)
toggleSlot(slotId, status)
setSlotPrice(slotId, vnd)
applyTemplate(courtId, template)

/* webhook */
POST /api/payos/webhook
```

All actions: Zod-validate input, RLS-enforce on DB, return discriminated-union result `{ ok: true, data } | { ok: false, error }`.

---

## 11. Repository structure

```
dinkpass-app/
├── app/
│   ├── (member)/
│   │   ├── page.tsx              # discovery
│   │   ├── clubs/[slug]/page.tsx
│   │   ├── booking/[id]/page.tsx
│   │   ├── account/page.tsx
│   │   └── layout.tsx
│   ├── (club)/
│   │   ├── schedule/page.tsx
│   │   ├── bookings/page.tsx
│   │   └── layout.tsx
│   ├── api/payos/webhook/route.ts
│   ├── actions/                  # server actions, one file per domain
│   ├── globals.css
│   └── layout.tsx
├── components/                    # shared UI
├── lib/
│   ├── supabase/{server,client,middleware}.ts
│   ├── payos.ts
│   ├── i18n.ts
│   └── time.ts
├── supabase/migrations/*.sql
├── public/manifest.json           # PWA
└── tests/
```

---

## 12. Environment variables

```
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=          # server only

PAYOS_CLIENT_ID=
PAYOS_API_KEY=
PAYOS_CHECKSUM_KEY=

RESEND_API_KEY=

SMS_PROVIDER=stringee|esms|vietguys
SMS_API_KEY=
SMS_SENDER_NAME=DinkPass

POSTHOG_KEY=

NEXT_PUBLIC_APP_URL=https://dinkpass.io
```

Store production secrets in Vercel + Supabase dashboards. **Never commit `.env.local`.**

---

## 13. Deployment & CI

- `main` → auto-deploys to production on Vercel.
- PRs → preview deploys.
- DB migrations: `supabase db push` from local; review via PR diff before merge.
- Required CI checks before merge: typecheck, eslint, `next build`. (Tests added incrementally — see §16.)

---

## 14. Observability

- **App logs:** Vercel built-in.
- **DB logs:** Supabase dashboard.
- **Errors:** Sentry (free tier) — wire after first deploy.
- **Product analytics:** PostHog events for: `signup`, `club_view`, `slot_view`, `booking_started`, `booking_paid`, `pass_purchased`, `cancel`.
- **Uptime:** Better Stack or UptimeRobot ping `/api/health` every 5 min.

---

## 15. Performance budget

- Largest Contentful Paint < 2.5 s on 4G in Đà Nẵng (Supabase + Vercel both in Singapore region — single-digit-ms hop from VN edge).
- Server actions p95 < 400 ms.
- Slot list render under 200 visible items uses virtualization (Tanstack Virtual).
- Images: Next.js `<Image>` only; clubs upload max 1080p, served as AVIF/WebP.

---

## 16. Testing strategy (proportional to MVP)

| Layer | What | Tool |
|---|---|---|
| Type safety | Strict TS, Zod at boundaries | tsc + Zod |
| Unit | Pricing math, date math, RLS predicates | Vitest |
| Integration | Booking concurrency, payment webhook idempotency | Vitest + Supabase test container |
| E2E | Happy-path: signup → book → pay → confirm | Playwright (1 spec for MVP) |

Manual QA checklist run before every prod deploy until E2E covers it.

---

## 17. Security checklist

- [ ] All mutations go through server actions with Zod validation
- [ ] Service-role key never exposed to client
- [ ] RLS enabled on every table; deny-by-default
- [ ] PayOS webhook verifies HMAC; rejects on signature mismatch
- [ ] Phone numbers validated against E.164 + Vietnam country code
- [ ] Rate limit OTP requests (1/min/phone, 5/hour/IP)
- [ ] No PII in URLs or logs
- [ ] Booking QR code is a JWT signed with server-only key (cannot be forged)
- [ ] CSRF: server actions are Origin-checked by Next.js by default
- [ ] Dependabot alerts on; weekly upgrade pass
- [ ] Production HTTPS enforced (handled by Vercel + GitHub Pages on landing)

---

## 18. Definition of Done (per feature)

A feature ships only when:
1. Code is merged to `main` and deployed
2. Manual QA against the happy path + 2 edge cases passes
3. PostHog event for the feature is firing in production
4. Empty / loading / error states exist
5. Strings are in Vietnamese (English optional in MVP)
6. RLS policies are verified
7. The relevant section of this doc is updated

---

## 19. Out of scope for MVP

- Native mobile app
- Multi-city (Đà Nẵng only)
- DUPR integration
- Lessons / leagues / tournaments
- Guest passes
- Loyalty / referrals
- Push notifications
- Real-time chat
- Admin dashboard beyond Supabase Studio
- Card-on-file recurring subscriptions
- Reviews and ratings
- Friend graph / social features
