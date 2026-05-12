# Libra — Development Status / Status Pengembangan

> **Source of truth for current dev state.** Update this file at the end of every working session.
> **Sumber kebenaran untuk status pengembangan.** Perbarui file ini di akhir setiap sesi kerja.

Last updated: 2026-05-12

---

## 0. How to use this file / Cara pakai file ini

**EN.**
- **At session start:** read this file end-to-end. The Feature Status Table tells you *what's where*; the Dev Log tells you *what happened last*; Open Issues tells you *what's blocked*; the Decision Log tells you *why we chose what we chose*.
- **At session end:** update the status of any feature you touched, append a Dev Log entry for today, and add to Open Issues / Decision Log as needed.
- **Status legend:**
  - `on-dev` — designed in PRD, not started
  - `in-progress` — actively being built
  - `ready-qa` — code complete, awaiting test
  - `live` — deployed to production
  - `blocked` — waiting on a dependency (see Open Issues)

**ID.**
- **Di awal sesi:** baca file ini dari atas ke bawah. Tabel Status Fitur memberitahu *apa di mana*; Catatan Pengembangan memberitahu *apa yang terjadi terakhir*; Isu Terbuka memberitahu *apa yang macet*; Catatan Keputusan menjelaskan *kenapa kami memilih sesuatu*.
- **Di akhir sesi:** perbarui status fitur yang disentuh, tambahkan entri Dev Log untuk hari ini, dan isi Open Issues / Decision Log seperlunya.
- **Legenda status:**
  - `on-dev` — dirancang di PRD, belum dimulai
  - `in-progress` — sedang dikerjakan
  - `ready-qa` — kode selesai, menunggu test
  - `live` — sudah dirilis
  - `blocked` — menunggu dependensi (lihat Open Issues)

---

## 1. Feature Status Table / Tabel Status Fitur

| ID | Feature / Fitur | Milestone | Status | Owner | Target | PR/Issue | Notes |
|---|---|---|---|---|---|---|---|
| F-01 | Auth (email + Google OAuth via Supabase) | M1 | `on-dev` | — | Wk 1–2 | — | Supabase Auth + email verification |
| F-02 | Manual book entry (title/author/ISBN form) | M1 | `on-dev` | — | Wk 1–2 | — | Foundation for AI flow |
| F-03 | Library view (grid + list, search, filter) | M1 | `on-dev` | — | Wk 1–2 | — | RLS-enforced |
| F-04 | Photo capture + upload to Supabase Storage | M2 | `on-dev` | — | Wk 3–4 | — | Camera + file input, client-side compress |
| F-05 | AI cover scan (Claude vision → title/author) | M2 | `on-dev` | — | Wk 3–4 | — | Edge Function /scan-book |
| F-06 | Google Books metadata enrichment | M2 | `on-dev` | — | Wk 3–4 | — | Cache 30 days |
| F-07 | YouTube Data API video discovery | M3 | `on-dev` | — | Wk 5 | — | Top 5, embeddable filter |
| F-08 | Wishlist CRUD + convert-to-owned | M3 | `on-dev` | — | Wk 5 | — | |
| F-09 | Rules-based recommendations | M4 | `on-dev` | — | Wk 6–7 | — | Based on owned book categories |
| F-10 | PWA polish (manifest, SW, offline, install prompt) | M4 | `on-dev` | — | Wk 6–7 | — | Serwist or next-pwa |
| F-11 | i18n EN/ID | M4 | `on-dev` | — | Wk 6–7 | — | next-intl |
| F-12 | Settings (profile, theme, export, delete account) | M4 | `on-dev` | — | Wk 6–7 | — | |
| F-13 | Beta hardening (errors, perf, a11y) | M5 | `on-dev` | — | Wk 8 | — | Sentry, Lighthouse, axe |

---

## 2. Dev Log / Catatan Pengembangan

> Reverse-chronological. Newest entry on top. / Urutan terbalik. Entri terbaru di atas.

### 2026-05-12
- **Done:** PRD authored (`docs/prd.md`, bilingual EN/ID, 16 sections). Repo initialized on branch `claude/create-prd-wXJCX`. `docs/` structure established with `prd.md` and this `status.md`. Root `README.md` added as entry point.
- **Next:** Stand up Next.js + Supabase scaffold (M1). Create Supabase project, define schema (`books`, `wishlist`, `recommendation_cache`), enable RLS.
- **Notes:** No code yet — docs only. Stack confirmed: Next.js 15 + Supabase + Claude vision + Google Books API + YouTube Data API v3.

### Template

```
### YYYY-MM-DD
- **Done:** …
- **Next:** …
- **Notes:** …
```

---

## 3. Open Issues & Blockers / Isu & Blocker Terbuka

| ID | Issue | Severity | Owner | Mitigation | Opened |
|---|---|---|---|---|---|
| I-01 | API cost ceiling not yet set (Claude + YouTube + Google Books) | medium | — | Cache aggressively; add per-user quota; decide free vs freemium | 2026-05-12 |
| I-02 | Recommendation algorithm unproven (rules-based may feel shallow) | low | — | Track CTR; switch to LLM-based if < 10% CTR | 2026-05-12 |
| I-03 | Cover-photo quality variability (poor lighting, foreign-script covers) | medium | — | Manual edit fallback; ISBN barcode path in v2 | 2026-05-12 |

Severity scale: `low` · `medium` · `high` · `critical`.

---

## 4. Decision Log / Catatan Keputusan

> Lightweight ADRs. Newest on top. / ADR ringkas. Terbaru di atas.

### DR-002 — Docs live under `docs/`; status tracked in `docs/status.md` (2026-05-12)
- **Context:** Need a living document for feature status, dev log, and decisions, read at session start. PRD should stay stable.
- **Decision:** Move `prd.md` to `docs/prd.md` and add `docs/status.md`. Root `README.md` points to both.
- **Consequences:** Clean separation between stable spec and high-churn tracker. Session-start workflow reads `docs/status.md` first. Future docs (architecture, runbooks) go in `docs/` too.

### DR-001 — Stack: Next.js + Supabase + Claude vision + Google Books + YouTube (2026-05-12)
- **Context:** Need PWA with auth, DB, image storage, AI vision, and external book/video data.
- **Decision:** Next.js 15 (App Router) on Vercel; Supabase for Auth/Postgres/Storage/Edge Functions; Anthropic Claude for cover vision; Google Books API for canonical metadata; YouTube Data API v3 for related videos.
- **Consequences:** Single managed BaaS (low ops). Server-only API keys via Edge Functions keep secrets safe. Quota costs need monitoring (see I-01).

### Template

```
### DR-NNN — <Title> (YYYY-MM-DD)
- **Context:**
- **Decision:**
- **Consequences:**
```
