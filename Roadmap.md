# DinkPass — 3-Week Sprint Roadmap

> **Goal by end of Week 3:** Live landing page collecting waitlist signups, 5–10 signed LOIs with **Đà Nẵng** partner clubs, and a working booking + payment flow demoed end-to-end with **one pilot club**.

> **North-star metric for this window:** *Number of paid bookings completed by real users at a real partner club.* Target: ≥ 10.

> **Owner tags:** 🧭 = founder / business · 🛠 = engineering · 🎨 = product/design · 🤝 = partnerships

---

## ⚠️ Decisions to lock in Week 1 (Day 1–2)

These unblock everything else. Don't start building before these are answered.

- [ ] 🧭 **Brand name lock** — keep "DinkPass" or change?
- [x] 🧭 **Pilot district** — ✅ **Sơn Trà / An Hải** (highest cluster of clubs on the map, near My Khe Beach)
- [ ] 🧭 **Payment gateway** — PayOS (recommended) vs. direct VNPAY+MoMo
- [ ] 🧭 **Subscription model** — manual renewal (recommended for MVP) vs. card-on-file
- [ ] 🧭 **Auth method** — phone OTP only vs. phone OTP + Google
- [ ] 🧭 **MVP language** — VI-only (faster) vs. VI + EN (broader)
- [x] 🧭 **Pricing** — locked at **99K / 690K / 1.190K ₫** with **30 % off for 12 months** founding offer. Validate with first 5 club conversations and 20 player surveys; adjust if signal says low.
- [ ] 🧭 **Legal entity** — using existing company or registering new VN entity for DinkPass?

---

## Week 1 — Validate, Sign, Set Up

**Theme:** *Prove demand and lock the supply side. Engineering does setup only.*

### 🤝 Partnerships (the most important track)
- [ ] 🤝 Build a target list of 20 Đà Nẵng partner clubs (priority: Sơn Trà / An Hải / Bắc Mỹ Phú clusters from the map — Duy King, San Pickleball Union, Pikaball, T-Army, Pickle Head Mỹ Khê, Happy Pickleball, Cosevco Thanh Khê, Riverpoint, GreenZone, AK Club, VQ Fitness, Zone 43)
- [ ] 🤝 Draft a **1-page LOI** in Vietnamese: revenue share, exclusivity (none required), 90-day pilot terms
- [ ] 🤝 Pitch 15+ clubs in person — target 8 verbal yeses, 5 signed LOIs by end of week
- [ ] 🤝 Survey 20 active players: pricing willingness, top-3 clubs they'd want, frustration with current memberships
- [ ] 🤝 Identify **1 anchor pilot club** willing to onboard real bookings in Week 3

### 🧭 Business setup
- [ ] 🧭 Confirm legal entity for receiving payments
- [ ] 🧭 Open business bank account (if new entity)
- [ ] 🧭 Apply for PayOS merchant account (3–5 day approval) — submit Day 1
- [ ] 🧭 Register for VND-denominated Stripe account (international card backup, optional)
- [ ] 🧭 Buy a Vietnam mobile number for OTP testing + customer support

### 🎨 Brand & marketing
- [x] ✅ Landing page bilingual + functional waitlist (Done)
- [ ] 🎨 Wire waitlist form to **Loops.so** or **Resend Audiences** (replace demo mode)
- [ ] 🎨 Finish DNS — `dinkpass.io` live with HTTPS
- [ ] 🎨 Set up `hello@dinkpass.io` mailbox
- [ ] 🎨 Create simple logo file (SVG) — for now use the lime-circle paddle mark
- [ ] 🎨 Open social accounts: Facebook page, Instagram, Zalo OA, TikTok

### 🛠 Engineering — *only foundation, no features yet*
- [ ] 🛠 Create new repo `Dinkpass/app` (Next.js 15 + TypeScript + Tailwind + shadcn/ui)
- [ ] 🛠 Provision Supabase project (HCM region or nearest — Singapore)
- [ ] 🛠 Define schema migrations: `users`, `clubs`, `courts`, `slots`, `bookings`, `payments`
- [ ] 🛠 Configure Supabase Auth (phone OTP via SMS provider)
- [ ] 🛠 Set up Vercel project + preview deploys + env vars
- [ ] 🛠 Skeleton routes: `/` `/clubs` `/clubs/[id]` `/booking/[id]` `/account` `/admin/club`

**End-of-week check:**
- ✅ ≥ 5 signed LOIs in hand
- ✅ Landing page on `https://dinkpass.io` with HTTPS
- ✅ Real waitlist provider receiving submissions
- ✅ App repo scaffolded, builds + deploys to Vercel
- ✅ PayOS merchant application submitted

---

## Week 2 — Build the Booking Loop

**Theme:** *End-to-end member journey on staging, with seed data. No payments yet.*

### 🛠 Engineering
- [ ] 🛠 Implement phone OTP signup/login flow
- [ ] 🛠 **Member: club discovery** — list view + map view (filter by district, indoor/outdoor)
- [ ] 🛠 **Member: club detail** — court list + today's slots (Supabase realtime subscription)
- [ ] 🛠 **Member: booking confirmation** screen with QR code
- [ ] 🛠 **Club admin: weekly schedule editor** — block/unblock slots, set price
- [ ] 🛠 Row-level security: members see only their bookings; club admins see only their club
- [ ] 🛠 Atomic slot booking via single `UPDATE … WHERE status='open'` (no double-bookings)
- [ ] 🛠 Seed data: 5 clubs × 4 courts × 7 days × 8 slots = 1,120 slots in dev

### 🎨 Design
- [ ] 🎨 Apply brand voice + color palette to the app (use [.claude/brand-voice-guidelines.md](.claude/brand-voice-guidelines.md))
- [ ] 🎨 Design 5 core screens in Figma: home, club detail, slot picker, confirm, success
- [ ] 🎨 Component library: pill buttons, rounded-3xl cards, lime accents — match landing page

### 🤝 Partnerships
- [ ] 🤝 Onboard the 1 pilot anchor club (collect court info, photos, schedule)
- [ ] 🤝 Convert 3 more verbal yeses into signed LOIs (target: 8 total signed)
- [ ] 🤝 Recruit 30 beta-tester players from the waitlist (paid pilot trial: free first 2 weeks)

### 🧭 Operations
- [ ] 🧭 Approve PayOS account (should arrive this week)
- [ ] 🧭 Draft member Terms of Service + Privacy Policy in VI
- [ ] 🧭 Set up customer support: Zalo + email triage in `hello@dinkpass.io`

**End-of-week check:**
- ✅ Member can sign in, browse clubs, view real-time availability, book a slot, see confirmation QR
- ✅ Pilot club admin can publish a real schedule
- ✅ All bookings still **free** (no payment gate yet)
- ✅ Internal team has booked at least 5 test slots end-to-end

---

## Week 3 — Payments + Pilot Launch

**Theme:** *Charge real money, run real bookings at the pilot club. Find what breaks.*

### 🛠 Engineering
- [ ] 🛠 PayOS payment-link generation on booking confirm
- [ ] 🛠 PayOS webhook endpoint (HMAC verification, idempotent)
- [ ] 🛠 Pay state machine: `pending → paid → confirmed` or `pending → expired → cancelled`
- [ ] 🛠 Day Pass purchase flow (one-time, 99K ₫)
- [ ] 🛠 City Pass purchase flow (manual renewal: 690K ₫ for 30 days · 30 % off promo for founding members)
- [ ] 🛠 Email receipts via Resend
- [ ] 🛠 SMS booking confirmation via Hostinger or Twilio (just the QR code link)
- [ ] 🛠 Cancel + refund flow (PayOS supports partial refunds)
- [ ] 🛠 Basic analytics: PostHog or Plausible event tracking on key funnel steps

### 🤝 Pilot launch (Friday Week 3)
- [ ] 🤝 Soft-launch with **30 beta members** at the **1 pilot club**
- [ ] 🤝 Founder physically on-site for first weekend to handle issues live
- [ ] 🤝 Hand check-in process to club staff (paper backup ready)
- [ ] 🤝 WhatsApp/Zalo group with all 30 beta users for instant feedback

### 🎨 Final polish
- [ ] 🎨 Empty states, loading states, error states (every screen)
- [ ] 🎨 Onboarding email sequence (welcome → how to book → first reminder)
- [ ] 🎨 Help center: 10 FAQ articles in VI

### 🧭 Measurement
- [ ] 🧭 Define success metrics for the pilot:
  - **Activation:** % of signups completing first booking (target ≥ 40%)
  - **Conversion:** % of bookers buying City Pass within 14 days (target ≥ 15%)
  - **NPS** at day 7 (target ≥ 30)
  - **Club satisfaction:** weekly call with pilot club owner
- [ ] 🧭 Daily standup at 9am with engineering, partnerships, support

**End-of-week check:**
- ✅ ≥ 10 paid bookings completed by real users
- ✅ ≥ 3 City Pass purchases
- ✅ Zero double-bookings, zero stuck payments
- ✅ Pilot club owner says "yes, expand to my other location"

---

## Risks & dependencies

| Risk | Likelihood | Mitigation |
|---|---|---|
| Clubs refuse to sign without seeing volume | High | Offer guaranteed payout floor for the 90-day pilot |
| PayOS approval delay | Medium | Apply Day 1 of Week 1; have Stripe as backup for foreign cards |
| Double-booking under load | Low | Postgres atomic update is bullet-proof at this scale; load test in Week 3 |
| No-show / abuse | Medium | Require booking confirmation 30 min before; flag repeat no-shows |
| Founder bandwidth (sales + ops + product) | High | Hire one part-time partnerships rep ASAP |
| Vietnamese SMS deliverability | Medium | Test 3 providers in Week 1: Stringee, eSMS, VietGuys |

---

## What we're NOT doing in these 3 weeks

To stay focused, defer everything below. Write down the idea, ignore it.

- ❌ Mobile native app (PWA only — `manifest.json` + add-to-homescreen prompt is enough)
- ❌ DUPR integration / skill matching
- ❌ Multi-club passes (City Pass only — Vietnam Pass is post-MVP)
- ❌ Guest passes
- ❌ Lessons / leagues / tournaments
- ❌ Loyalty / referrals
- ❌ Hà Nội / Đà Nẵng expansion
- ❌ Custom club analytics dashboard (Supabase Studio is enough for now)
- ❌ Push notifications (use SMS + email)
- ❌ A/B testing framework
- ❌ Real-time chat support (Zalo + email triage)

---

## Stack reminder (locked)

| Layer | Tool |
|---|---|
| App | Next.js 15 (App Router) + TypeScript + Tailwind + shadcn/ui |
| DB + Auth + Realtime | Supabase (Singapore region) |
| Payments | PayOS (primary) · Stripe (backup) |
| Email | Resend |
| SMS | Stringee / eSMS / VietGuys (decide Week 1) |
| Analytics | PostHog (free, self-hostable later) |
| Hosting | Vercel |
| Domain | dinkpass.io (Hostinger registrar) |

---

## Daily cadence

- **Mon–Fri 9:00** — 15-min standup (3 questions: yesterday, today, blockers)
- **Wed 16:00** — partnership review (clubs signed / lost / pipeline)
- **Fri 17:00** — week-end review against the end-of-week checks above
- **Sat morning** — beta member office hours (during pilot week)

---

*Last updated: 2026-05-07 · Owner: Nam Nguyen · This file is the source of truth — update it daily.*
