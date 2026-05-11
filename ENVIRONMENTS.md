# DinkPass — Environments

> Two environments. Same code. Different data and different URLs.

| | **Development** | **Production** |
|---|---|---|
| **Purpose** | Engineers test here. Break freely. | Real users. Don't break. |
| **URL** | `https://dev.dinkpass.io` *(set up DNS — see below)* · also any `*.github.io` · `localhost` | `https://dinkpass.io` · `https://www.dinkpass.io` |
| **Git branch** | `develop` | `main` |
| **Supabase project** | `Dinkpass-dev` (`yiyqwhiyvznvcqwtlmjw`) | `Dinkpass` (`zzwegqmjitnkobvmbyrr`) |
| **Supabase region** | `ap-southeast-1` (Singapore) | `ap-southeast-1` (Singapore) |
| **Waitlist data** | Test rows, throwaway | Real signups |
| **Visible UI marker** | Orange "DEV ENV" badge bottom-right | None |

---

## How environment selection works

There is **one** `index.html`. Environment is decided at page load by hostname:

```js
const PROD_HOSTS = new Set(['dinkpass.io', 'www.dinkpass.io']);
const ENV_NAME = PROD_HOSTS.has(location.hostname) ? 'production' : 'development';
```

This means:

- `dinkpass.io` → production
- `www.dinkpass.io` → production
- `dev.dinkpass.io` → development
- `localhost`, `127.0.0.1` → development
- `dinkpass.github.io/landing/` → development
- `file://` (opening the HTML directly) → development

You never need to remember to flip a flag. Just deploy the same `index.html` to both hosts.

---

## Git workflow

```
            ┌── feature/foo ──┐
            │                 │
        develop ──merge──► develop ──promote──► main
                              │                   │
                       dev.dinkpass.io      dinkpass.io
                       (Dinkpass-dev)       (Dinkpass)
```

1. Create a feature branch off `develop`
2. Open a PR into `develop` — preview deploys to dev
3. Test on `dev.dinkpass.io` (or local) — points at `Dinkpass-dev` Supabase
4. When ready to release, open a PR from `develop` → `main`
5. Merge to `main` → auto-deploys to production
6. **Never commit directly to `main`.** Branch protection rule recommended.

### Hot-fix path
For urgent production fixes:
```bash
git checkout main
git checkout -b hotfix/<thing>
# fix, commit
gh pr create --base main
# merge → deploys to prod
git checkout develop && git merge main   # backport
```

---

## Setting up `dev.dinkpass.io` (one-time)

### Option A — Same GitHub Pages, separate branch
Limitation: GitHub Pages only allows **one custom domain per repo**, so this won't work out-of-the-box. Use **Option B** instead.

### Option B — Cloudflare Pages (recommended)
1. Sign up at https://pages.cloudflare.com (free)
2. Connect the `Dinkpass/landing` repo
3. Set **Production branch** = `main`, **Preview branches** = `develop` (and any branch)
4. Cloudflare auto-deploys both:
   - `main` → automatically gets a `*.pages.dev` URL (point your prod domain here OR keep GH Pages)
   - `develop` → gets a `develop.dinkpass-landing.pages.dev` URL (also free preview)
5. Add custom domain `dev.dinkpass.io` to the `develop` preview branch
6. Add a CNAME in Hostinger:
   - **Type:** CNAME · **Name:** `dev` · **Content:** `dinkpass-landing.pages.dev`

### Option C — Vercel (alternative)
Same flow as Cloudflare Pages. Vercel gives every PR a unique preview URL automatically, which is great when reviewing branches. Recommended if/when we add the Next.js app — same project handles both.

---

## Supabase project details

### Production
- **Name:** Dinkpass
- **Ref:** `zzwegqmjitnkobvmbyrr`
- **Dashboard:** https://supabase.com/dashboard/project/zzwegqmjitnkobvmbyrr
- **URL:** `https://zzwegqmjitnkobvmbyrr.supabase.co`
- **Anon key (publishable):** `sb_publishable_uDBlvL-uTkMIPu1JIgOwGA_GypO6N-8`
- **Service-role key:** **never commit** — store only in Vercel/CF Pages env vars when we add the app

### Development
- **Name:** Dinkpass-dev
- **Ref:** `yiyqwhiyvznvcqwtlmjw`
- **Dashboard:** https://supabase.com/dashboard/project/yiyqwhiyvznvcqwtlmjw
- **URL:** `https://yiyqwhiyvznvcqwtlmjw.supabase.co`
- **Anon key (publishable):** `sb_publishable_FGO-AnKx1DdM08Iw9N3yKg_hKWv1XTr`

Both projects start with identical schemas: a single `waitlist` table with RLS allowing anon `INSERT` only.

---

## Promoting schema changes (dev → prod)

When the real app starts, we'll use **Supabase CLI migrations**. The pattern:

```bash
# 1. Develop the migration on the dev project
supabase link --project-ref yiyqwhiyvznvcqwtlmjw
supabase migration new <name>
# ... edit SQL
supabase db push                        # applies to dev

# 2. Once verified in dev, promote to prod
supabase link --project-ref zzwegqmjitnkobvmbyrr
supabase db push                        # applies to prod

# 3. Commit the migration file to git
git add supabase/migrations/<file>.sql
```

For now (landing-page-only era), we're applying SQL via the MCP / Studio. Once the app repo exists, all DDL goes through migration files committed to git.

---

## Local development

```bash
# 1. Clone
git clone git@github.com:Dinkpass/landing.git
cd landing
git checkout develop

# 2. Serve
python3 -m http.server 8000
# Open http://localhost:8000 — automatically uses the dev Supabase project

# 3. Make changes on a feature branch
git checkout -b feature/<thing>
# ... edit, commit ...
gh pr create --base develop
```

You'll see the **orange "DEV ENV" badge** in the bottom-right corner whenever you're not on production.

---

## Where to peek at data

### Production signups
- Supabase Studio → [Dinkpass project](https://supabase.com/dashboard/project/zzwegqmjitnkobvmbyrr/editor) → Table Editor → `waitlist`

### Development signups (test data — clear anytime)
- Supabase Studio → [Dinkpass-dev project](https://supabase.com/dashboard/project/yiyqwhiyvznvcqwtlmjw/editor) → Table Editor → `waitlist`

Useful SQL for the dashboard (works in either project):

```sql
-- Daily signup count + language split
select
  date_trunc('day', created_at)::date as day,
  count(*) as signups,
  count(*) filter (where language = 'vi') as vi,
  count(*) filter (where language = 'en') as en,
  count(*) filter (where source = 'hero') as from_hero,
  count(*) filter (where source = 'footer-cta') as from_footer
from public.waitlist
group by 1 order by 1 desc;

-- Latest 20 signups
select email, source, language, referrer, created_at
from public.waitlist
order by created_at desc
limit 20;

-- Truncate dev (DANGER — only run in Dinkpass-dev)
truncate table public.waitlist;
```

---

## Security notes

- **Publishable keys are public by design.** They are RLS-gated: anon can only `INSERT` into `waitlist`, never `SELECT`. Safe to commit and ship in client HTML.
- **Service-role keys** bypass RLS. Never commit them. Never put them in `index.html`. Future app server code will use them via Vercel/CF Pages env vars only.
- **Don't share dev data externally** — it's still real-looking emails and the same column names. Treat it like prod for PII handling.

---

*Last updated: 2026-05-10*
