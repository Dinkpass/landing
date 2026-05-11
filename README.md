# DinkPass

> **Một thẻ. Chơi mọi nơi.** · One pass. Play anywhere.

The membership for people who don't want a membership. One subscription unlocks unlimited pickleball at the best clubs in your city — starting in **Đà Nẵng**, expanding across Vietnam.

---

## What's in this repo

A single-file static landing page (HTML + Tailwind CDN + vanilla JS) with bilingual support (Vietnamese 🇻🇳 + English 🇬🇧) and a functional waitlist form.

```
.
├── index.html                 # The landing page (Tailwind via CDN, no build step)
├── .claude/
│   └── brand-voice-guidelines.md
├── .gitignore
└── README.md
```

## Run locally

No build step. Just open the file:

```bash
open index.html
```

Or serve it for nicer dev experience:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Deploy

Drop into any static host:

- **Cloudflare Pages** — connect the repo, no build command
- **GitHub Pages** — Settings → Pages → Deploy from `main`, root
- **Netlify** — drag-and-drop the folder, or connect the repo
- **Vercel** — `vercel --prod` from this directory

## Languages

- 🇻🇳 **Vietnamese** (default — primary market)
- 🇬🇧 **English** (toggle in top-right nav)

Translations live in the `I18N` object near the bottom of `index.html`. The user's choice is saved to `localStorage` and detected from `navigator.language` on first visit.

## Waitlist form

Both forms (hero + final CTA) submit to a single endpoint via `fetch`. Until you configure the endpoint, the form runs in **demo mode** — it validates emails, stores them in `localStorage`, and shows a "demo mode" success message so you can preview the UX immediately.

### Wire it up to a real provider

Open `index.html`, search for `WAITLIST_ENDPOINT`, and replace the placeholder. Pick whichever you prefer:

#### Option 1 — Formspree (recommended)
1. Sign up at https://formspree.io (free tier: 50 submissions/month)
2. Create a new form, copy your form ID (looks like `xayzqbjk`)
3. Replace:
   ```js
   const WAITLIST_ENDPOINT = 'https://formspree.io/f/xayzqbjk';
   ```

#### Option 2 — FormSubmit (no signup)
1. Replace with your destination email:
   ```js
   const WAITLIST_ENDPOINT = 'https://formsubmit.co/hello@dinkpass.vn';
   ```
2. Submit the form once to confirm the email — done.

#### Option 3 — Web3Forms / Buttondown / ConvertKit / Loops
Any service that accepts a `POST` with `Accept: application/json` will work. The form sends `email`, `_gotcha` (honeypot), and the request includes `data-source` (`hero` vs `footer-cta`) and the active language.

### Built-in features
- ✅ Email validation (client-side)
- ✅ Loading / success / error states (bilingual)
- ✅ Honeypot field for spam protection
- ✅ Local fallback in `localStorage` (key: `dinkpass-waitlist`) — useful for debugging or as a safety net
- ✅ Disabled button while submitting
- ✅ Status messages adapt to dark/light section background

## Brand voice

See [`.claude/brand-voice-guidelines.md`](.claude/brand-voice-guidelines.md) for the full voice and visual identity spec — colors, typography, tone matrix, do/don't lists, and confidence-scored guidelines.

## Launch plan

| Phase | Cities | Timing |
|---|---|---|
| **MVP launch** | Đà Nẵng (Sơn Trà, Hải Châu, Ngũ Hành Sơn, Thanh Khê) | Q3 2026 |
| **Phase 2** | Hà Nội, Ho Chi Minh City | Q4 2026 |
| **Phase 3** | Hải Phòng, Cần Thơ, Nha Trang | 2027 |

## Pricing (launch)

| Plan | Price | What you get |
|---|---|---|
| **Day Pass** | 99.000₫ / day | 24-hour access at any partner club |
| **City Pass · Đà Nẵng** | 690.000₫ / month | Unlimited play across Đà Nẵng partner clubs |
| **Vietnam Pass** | 1.190.000₫ / month | Multi-city access (as new cities launch) |

Founding members lock in **30% off for the first 12 months**.

## License

Proprietary. © 2026 DinkPass Vietnam. All rights reserved.
