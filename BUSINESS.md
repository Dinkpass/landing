# DinkPass — Business Model & Requirements

> **Audience:** Founder, future employees, club partners, investors.
> **Status:** Source of truth for the business side. Engineers read [ENGINEERING.md](ENGINEERING.md).

---

## 1. Executive summary

**DinkPass** is a curated, single-subscription pickleball-court network for Vietnam. One pass — one price — unlimited play across the best partner clubs in **Đà Nẵng**, with planned expansion to Hà Nội and Ho Chi Minh City.

We solve two pains at once:
- **For players:** stop juggling memberships at 2–3 clubs to find a court near home, the beach, or work.
- **For club operators:** monetize empty off-peak courts without diluting the existing membership business.

**Why Đà Nẵng first:** small enough that 20 partner clubs covers the whole urban area; high density of indoor + outdoor courts; strong active-lifestyle culture among locals, expats, remote workers, and tourists. We prove the model in a contained market, then expand to Hà Nội and HCMC where city density and competition are higher.

**Tagline:** *Một thẻ. Chơi mọi nơi.* / *One pass. Play anywhere.*

---

## 2. Problem & opportunity

### What's broken today
- Pickleball is the fastest-growing sport in Vietnam (300% YoY player growth in 2025).
- New indoor and outdoor clubs are opening monthly across Đà Nẵng — over 20 visible on the map today — but each one runs its own membership, its own booking system, and its own pricing.
- Active players (3+ sessions/week) frequently hold 2–3 separate memberships totaling 1.5–3 M ₫/month, plus drop-in fees on top.
- Travelers and relocators have no good way to play in a new city without joining a new club.
- Clubs themselves have idle off-peak inventory they can't fill.

### Why now
- Player base in Vietnam is digitally native (avg age has dropped each year — 40 % under 35).
- Đà Nẵng has reached the **density tipping point** for the sport — 20+ clubs sit within a 20-minute drive of any central district. Per capita that's higher density than HCMC.
- Vietnamese payment rails (PayOS, MoMo, VNPAY) are mature enough to support seamless mobile checkout.
- No incumbent: ClassPass doesn't operate in Vietnam; 365Dink (a US-based discount card) has zero presence.

---

## 3. Solution

A subscription that turns 25 + competing clubs into one product. Members pay DinkPass once; DinkPass pays clubs per visit at a rate they control. The platform is **player-side first** — discovery, booking, check-in, all in one app.

### Core features (MVP)
1. **Find a court** — list + map of partner clubs, with live slot availability filtered by district, indoor/outdoor, time.
2. **Book & pay** — one-tap booking with QR confirmation; pay-per-visit (Day Pass) or use an active subscription (City Pass).

### Post-MVP roadmap (informational, not committed)
- Skill-rated open play (DUPR integration)
- Lessons & league marketplace
- Guest passes
- Multi-city (Hà Nội, Đà Nẵng)
- Native mobile app

---

## 4. Market & customer segments

### 4.1 Players (demand side)

| Segment | Profile | What they pay today | What DinkPass replaces |
|---|---|---|---|
| **Multi-club regular** (target) | Plays 3–6×/week, 25–45 yo, professional or remote worker, lives in Sơn Trà / Hải Châu / Ngũ Hành Sơn | 1.5–3 M ₫/mo across 2–3 club memberships + drop-ins | All memberships → 690 K ₫/mo City Pass |
| **Remote worker / digital nomad** | Long-stay foreigner or Vietnamese remote worker in Đà Nẵng; plays 2–4×/week | Drop-ins at multiple clubs near their stay | City Pass as lifestyle subscription |
| **Frequent traveler** | Hà Nội or HCMC-based; visits Đà Nẵng monthly for work or beach weekends | 200–400 K ₫/visit drop-in fees | Day Pass at 99 K ₫; Vietnam Pass when their home city launches |
| **Curious newcomer** | New to the sport, doesn't want to commit to a single club | Pays drop-in trials at 2–3 clubs | Day Pass for low-risk exploration |
| **Visitor / expat** | In Đà Nẵng for business, leisure, or short-stay | Hotel concierge or asks Facebook groups | Day Pass; converts to City Pass if they extend stay |

### 4.2 Clubs (supply side)

| Segment | Pain | Why DinkPass helps |
|---|---|---|
| **Independent indoor club** (5–8 courts) | Off-peak slots (10 AM–4 PM, late evening) sit empty | Incremental revenue, no marketing burden |
| **Newly opened club** | Needs awareness fast | Instant exposure to existing DinkPass members |
| **Multi-location operator** | Wants utilization data across sites | DinkPass reporting (post-MVP) |

We **explicitly do not** target large chains (e.g., Picklr-style networks if they enter VN) at MVP — they'll prefer their own stack.

---

## 5. Value proposition

### For players
- One subscription replaces 2–3 — saves 30–60 % on monthly cost
- Real-time availability, no phone-call-to-the-front-desk
- One identity across every partner club
- Travel-ready when we expand

### For clubs
- 0 ₫ setup, 0 ₫ monthly platform fee
- Per-visit payouts, club sets the floor price
- New player acquisition handled by DinkPass marketing
- Existing members remain on existing memberships — no cannibalization

---

## 6. Revenue model

### 6.1 Pricing tiers (consumer)

| Plan | Price (VND) | Entitlement |
|---|---|---|
| **Day Pass** | 99.000 ₫ / 24 h | One booking, any partner club |
| **City Pass · Đà Nẵng** | 690.000 ₫ / month | Unlimited bookings in Đà Nẵng, fair-use cap (default 6 visits/club/month) |
| **Vietnam Pass** *(post-MVP)* | 1.190.000 ₫ / month | All cities as they launch |

**Founding-member offer:** **30 % off for the first 12 months** on City Pass (capped at first 1,000 members). After Month 12 the member rolls onto the standard rate — they can cancel at any time.

**Why we changed the offer:** "50 % off for life" cratered long-term margin and was hard to reverse without burning trust. "30 % for 12 months" still gives a strong early-adopter signal, protects unit economics, and gives us a natural retention moment at the renewal.

Annual prepay: pay-for-10, get-12 (≈ 17 % discount).

### 6.2 Club payout model

Per-visit payment to the club, **dynamically priced** based on:
- Club's listed drop-in price (anchor)
- Time of day (peak ≈ 70–80 % of drop-in, off-peak ≈ 35–50 %)
- Real-time court utilization (lower utilization → lower payout, higher payout when scarce)

**Initial launch numbers (subject to negotiation with each club):**
- Average payout: 100–180 K ₫ per visit
- Day Pass margin: 20–40 K ₫ per visit
- City Pass: economics depend on visit frequency — see §7

### 6.3 Future revenue lines (post-MVP, informational)
- Lessons & league marketplace (15 % take rate)
- Tournament entry fees (10 %)
- Brand sponsorships (paddle, ball, apparel)
- Equipment partner placements
- Corporate / enterprise plans

---

## 7. Unit economics (City Pass)

Assumptions (Đà Nẵng pilot):
- List price: **690.000 ₫/mo**
- Blended ARPU: **~590 K ₫/mo** (mix of full-price + 30 %-off founding members)
- Avg visits/member/mo: **8** (validated against multi-club regulars in player survey)
- Avg payout/visit: **70 K ₫** (Đà Nẵng drop-in fees are 50–100 K; we pay 60–80 % during peak, less off-peak)
- COGS = 8 × 70 K = **560 K ₫**
- **Gross margin per member ≈ 30 K ₫ (5 %) at founder discount; ~130 K ₫ (19 %) at full price**

This is **positive but thin** — exactly what we want at this stage. The pricing is calibrated so that even our worst cohort (heavy users on founder discount) doesn't lose us money. Levers that improve margin from here:

1. **Visit caps** (per club + per network) → cap COGS at the long-tail of super-heavy users
2. **Frequency mix** — the dropoff is steep, same as gym subscriptions; ~30 % of subs play <4 ×/mo
3. **Off-peak nudges** — push availability + reminders for off-peak slots where payout multiplier is 35–50 % of drop-in instead of 70–80 %
4. **Scale negotiation** — as DinkPass volume grows, club payout share drops 5–10 % at each renewal
5. **Day Pass conversions** for casual users (99 K, ~30 K margin per visit at full retail)

**Target by Month 6 of pilot:** Blended gross margin **25 %+** at City Pass cohort level.

We will publish a quarterly margin update internally and revise pricing if needed.

---

## 8. Go-to-market — Đà Nẵng pilot

### 8.1 Phase 1 — Anchor district (Weeks 1–6)
- Lock 5–10 LOIs with **anchor clubs** in **one** target district (recommend **Sơn Trà / An Hải** — highest cluster of pickleball clubs on the map and dense with target customers: locals, expats, remote workers near the beach)
- Launch at 1 anchor club; expand to 5 within 30 days
- Acquisition: founder's network, partner-club staff, on-site QR-code signage, Đà Nẵng pickleball Facebook & Zalo groups, expat / remote-worker communities (Hoianian, Da Nang Expats), IG ads

### 8.2 Phase 2 — Expand district by district (Months 2–4)
- Add Hải Châu → Ngũ Hành Sơn → Thanh Khê → Liên Chiểu
- 20 + partner clubs covering the urban core, 800 paying members

### 8.3 Phase 3 — National (Months 5–9)
- Hà Nội launch
- Ho Chi Minh City launch
- Vietnam Pass becomes purchasable

### 8.4 Acquisition channels (priority order)
1. **In-club QR signage at partner facilities** — highest-intent, lowest-cost
2. **Pickleball Facebook & Zalo groups** in Đà Nẵng (and expat / digital-nomad groups)
3. **Founder & advisor referrals** — first 100 members
4. **Influencer micro-partnerships** (5–10 local players with 5–50K followers each)
5. **Google + Meta ads** (only after CAC is measurable)
6. **Press** (VnExpress, Tuoi Tre, Saigoneer) — scheduled for the public launch event

### 8.5 KPI targets (first 90 days)
- 1,000 + waitlist signups
- 5–10 signed LOIs
- 250 + paying City Pass members
- ≥ 40 % activation (first booking within 7 days)
- ≥ 15 % conversion (Day-Pass-er → City-Pass within 14 days)
- NPS ≥ 30

---

## 9. Partnership terms (LOI structure)

A standard one-page LOI in Vietnamese covers:

1. **Term:** 90-day pilot, automatically renewing month-to-month after.
2. **Exclusivity:** none required. Club can join competing platforms.
3. **Payout:** per-visit at agreed dynamic schedule. Paid weekly via bank transfer or PayOS payout.
4. **Capacity:** club designates how many DinkPass slots/day, default 30 % of total court-hours.
5. **Pricing parity:** drop-in walk-in price at the club may not be lower than DinkPass payout-equivalent — protects margin.
6. **Data:** DinkPass shares utilization, member ratings, and booking patterns with the club monthly.
7. **Branding:** club agrees to display DinkPass signage at reception; receives a starter kit (signage, staff QR scanner, training).
8. **Termination:** 30-day written notice either side.

Anchor clubs in the first 30 days receive a **guaranteed minimum payout** (50 % of expected based on traffic forecast) — de-risks the pilot for them.

---

## 10. Operations (90 days)

### 10.1 Team
| Role | Headcount | Notes |
|---|---|---|
| Founder / CEO | 1 | Partnerships, ops, P&L |
| Engineering | 1–2 | Full-stack |
| Designer (part-time) | 0.5 | Web + product |
| Partnerships rep | 1 | Hire week 1 — most leveraged role |
| Customer support | 0.5 | Doubles as community manager |

### 10.2 Customer support
- **Channel:** Zalo OA (primary), email `hello@dinkpass.io`, in-app message later
- **Hours:** 8:00–22:00 daily during pilot (founder + 1 support rep)
- **SLA:** first response < 30 min during business hours

### 10.3 Club onboarding playbook
1. LOI signed
2. On-site visit — photos, court inventory, walk-through
3. Account created in admin; staff trained on QR check-in (15 min)
4. First week: founder physically present during peak hours
5. Weekly check-in call for first month
6. Monthly business review thereafter

---

## 11. Compliance & legal (Vietnam-specific)

- **Business entity:** registered Vietnamese LLC required to hold a PayOS merchant account.
- **VAT:** prices include 8 % VAT (current rate); invoiced monthly.
- **Tax:** corporate income tax 20 % on net profit.
- **Personal data:** subject to PDPL (Decree 13/2023). Data stored in-region preferred (Supabase Singapore acceptable for MVP, Vietnam region post-launch).
- **Consumer protection:** clear cancellation/refund policy in Vietnamese, prominently displayed.
- **Liability:** general liability covered by partner club; DinkPass carries umbrella policy from Month 4+.
- **Marketing claims:** no "guaranteed" results; "unlimited" disclosed with fair-use caveat at every consumer touchpoint.

---

## 12. Success metrics (North Stars)

### Business
- Monthly Active Members (MAM)
- Gross Booking Volume (GBV)
- City Pass Net Revenue Retention
- Club partner count
- Club retention (% renewing past pilot)

### Product
- Activation rate (signup → first booking, 7 d)
- Conversion rate (Day Pass → City Pass, 14 d)
- Bookings per active member per month
- Cancel rate, no-show rate
- NPS (every 90 d)

### Health
- Gross margin per City Pass cohort
- CAC payback months
- Club payout share of revenue (target: trending down past 65 %)

---

## 13. Risks & mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Heavy users blow up unit economics | High | High | Visit caps; dynamic payouts; off-peak nudges |
| Low club density at launch | Medium | High | Concentrate in 1 district until 5 + clubs signed; do not announce city-wide until then |
| Club operators reject the model | Medium | High | Guaranteed-minimum payout for anchor clubs; show real value in week 1 |
| PayOS approval delay | Medium | Medium | Apply day 1; have Stripe (international cards) as backup |
| Adverse selection (only heavy users join) | Medium | High | Day Pass priced as an on-ramp; founder discount drives volume past power-user core |
| Vietnamese legal entity setup delay | Low | High | Start the registration in week 1 |
| Competitor copies the model | Medium | Medium | Lock anchor-club exclusivity terms post-launch; build network density |
| Founder bandwidth | High | High | Hire partnerships rep week 1 |
| Pickleball trend cools | Low | High | Expand sport coverage post-Series A (tennis, badminton, padel) |

---

## 14. Phased roadmap (high-level)

| Phase | Window | Goal |
|---|---|---|
| **Phase 0 — Validation** | Weeks 1–3 | LOIs + waitlist + working booking demo |
| **Phase 1 — Pilot** | Weeks 4–12 | 1 → 20 partner clubs in Đà Nẵng; 250 paying members |
| **Phase 2 — Đà Nẵng scale** | Months 4–6 | 30 + clubs (incl. Hội An radius); 1,000 paying members; gross margin > 25 % |
| **Phase 3 — Multi-city** | Months 7–12 | Hà Nội, HCMC launch; Vietnam Pass live |
| **Phase 4 — Adjacent revenue** | Year 2 | Lessons, leagues, tournaments, sponsorships |

---

## 15. Brand & voice

DinkPass voice and visual identity are codified in [.claude/brand-voice-guidelines.md](.claude/brand-voice-guidelines.md). Every consumer-facing surface — landing page, app, push, email, signage — must conform.

---

## 16. Decision log (locked vs. open)

### ✅ Locked
- Brand: **DinkPass** (working)
- Launch city: **Đà Nẵng**
- Pilot district: **Sơn Trà / An Hải** (highest cluster of clubs on the map, dense target customers near the beach)
- Tech stack: Next.js + Supabase + PayOS + Resend
- Pricing tiers: 99 K / 690 K / 1.190 K ₫
- Founding-member offer: 30 % off for the first 12 months
- MVP languages: Vietnamese (English optional)

### ⏳ Open (decide before Week 2)
- Subscription renewal: manual (recommended) vs card-on-file
- SMS provider: Stringee / eSMS / VietGuys
- Legal entity: existing or new
- Auth: phone-only vs phone + Google

---

*Last updated: 2026-05-07 · Owner: Nam Nguyen*
