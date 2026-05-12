# Libra — Product Requirements Document (PRD)

> Bilingual PRD: each section presents **English** first, then **Bahasa Indonesia**.
> PRD dwibahasa: tiap bagian menampilkan **Bahasa Inggris** lebih dulu, lalu **Bahasa Indonesia**.

---

## 1. Overview / Ikhtisar

**EN.** Libra is a Progressive Web App (PWA) that helps physical-book owners catalog, discover, and plan their reading. Users photograph a book cover; an AI vision model extracts title and author, enriches metadata via the Google Books API, and surfaces related YouTube videos (reviews, author interviews, summaries). Users also keep a wishlist and receive personalized recommendations.

**ID.** Libra adalah Progressive Web App (PWA) untuk membantu pemilik buku fisik mendata, menemukan, dan merencanakan bacaan mereka. Pengguna memotret sampul buku; model AI vision mengekstrak judul dan penulis, memperkaya metadata melalui Google Books API, dan menampilkan video YouTube terkait (ulasan, wawancara penulis, ringkasan). Pengguna juga dapat menyimpan wishlist serta menerima rekomendasi yang dipersonalisasi.

| Field | Value |
|---|---|
| Product name | Libra |
| Document status | Draft v0.1 |
| Owner | TBD |
| Last updated | 2026-05-12 |
| Target launch | TBD |

---

## 2. Problem & Vision / Masalah & Visi

**EN — Problem.** People who buy physical books struggle to keep track of what they own, what they've read, and what to read next. Manual cataloging (typing title, author, ISBN) is tedious. Discovering trustworthy companion content (reviews, summaries, author talks) requires hopping between apps.

**EN — Vision.** A friction-free pocket library. One photo turns a stack of physical books into a searchable, enriched digital shelf — with smart suggestions for what to read next.

**ID — Masalah.** Pemilik buku fisik kesulitan melacak buku yang mereka miliki, yang sudah dibaca, dan rencana berikutnya. Pendataan manual (mengetik judul, penulis, ISBN) merepotkan. Mencari konten pelengkap yang terpercaya (ulasan, ringkasan, talkshow penulis) membutuhkan banyak aplikasi.

**ID — Visi.** Perpustakaan saku tanpa hambatan. Satu foto mengubah tumpukan buku fisik menjadi rak digital yang dapat dicari dan diperkaya — lengkap dengan saran cerdas untuk bacaan berikutnya.

---

## 3. Goals & Non-Goals / Tujuan & Bukan Tujuan

### Goals / Tujuan

**EN.**
- Let a user save a physical book in under 15 seconds via a single cover photo.
- Auto-fill ≥85% of book metadata correctly without manual edits.
- Surface 3–5 relevant YouTube videos per book.
- Maintain a wishlist and produce useful recommendations from owned books.
- Be installable as a PWA on Android and iOS; usable on slow networks.

**ID.**
- Menyimpan satu buku fisik dalam < 15 detik dengan satu foto sampul.
- Mengisi otomatis ≥85% metadata buku secara benar tanpa edit manual.
- Menampilkan 3–5 video YouTube relevan per buku.
- Memelihara wishlist dan menghasilkan rekomendasi yang berguna dari koleksi.
- Dapat diinstal sebagai PWA di Android dan iOS; tetap nyaman di jaringan lambat.

### Non-Goals (v1) / Bukan Tujuan (v1)

- E-book reader / DRM content.
- Social network features (followers, public feeds).
- Marketplace / lending / selling.
- OCR'ing book interior pages.
- Multi-language UI beyond ID + EN.

---

## 4. Target Users & Personas / Pengguna Target & Persona

**EN.**
1. **The Collector** — Owns 50+ physical books, wants a clean catalog and visual shelf.
2. **The Casual Reader** — Buys a few books a year, needs reminders of what's at home and what to read next.
3. **The Aspirational Buyer** — Maintains a wishlist of "someday" books and tracks deals.

**ID.**
1. **Sang Kolektor** — Memiliki 50+ buku fisik, ingin katalog rapi dan rak visual.
2. **Pembaca Kasual** — Beli beberapa buku per tahun, perlu pengingat koleksi & rencana baca.
3. **Pembeli Aspirasional** — Memelihara wishlist buku "suatu hari nanti" dan memantau promo.

---

## 5. Core Features / Fitur Utama

### 5.1 Capture & Save Book / Tangkap & Simpan Buku

**EN.** User taps "Add Book", takes a photo of the cover (or uploads). The app uploads the image to Supabase Storage, calls a server-side AI flow (Claude vision) that returns probable title/author, then queries Google Books API for canonical metadata and YouTube Data API for related videos. User reviews & confirms a pre-filled form, then saves.

**ID.** Pengguna menekan "Tambah Buku", memotret sampul (atau mengunggah). Aplikasi mengunggah gambar ke Supabase Storage, memanggil alur AI di server (Claude vision) yang mengembalikan dugaan judul/penulis, lalu meminta Google Books API untuk metadata kanonis dan YouTube Data API untuk video terkait. Pengguna meninjau & mengonfirmasi formulir yang telah terisi, lalu menyimpan.

**Data captured:** title, author(s), ISBN-13, publisher, published date, page count, cover URL, description, categories, YouTube video links (title + URL + thumbnail).

### 5.2 Recommendations / Rekomendasi

**EN.** Based on the user's owned books (genres, authors, categories), Libra suggests new titles to read. v1 uses a rules-based approach over Google Books categories; later versions can use LLM-generated suggestions or collaborative filtering.

**ID.** Berdasarkan buku yang dimiliki pengguna (genre, penulis, kategori), Libra menyarankan judul baru. v1 menggunakan pendekatan berbasis aturan atas kategori Google Books; versi berikutnya dapat memakai saran berbasis LLM atau collaborative filtering.

### 5.3 Wishlist / Daftar Keinginan

**EN.** Users save books they don't yet own (via search, manual entry, or photo). Wishlist items can be moved to "owned" with one tap when purchased.

**ID.** Pengguna menyimpan buku yang belum dimiliki (melalui pencarian, entri manual, atau foto). Item wishlist dapat dipindahkan ke "dimiliki" dengan satu ketukan saat dibeli.

---

## 6. User Stories / Cerita Pengguna

**EN.**
- *As a collector*, I want to scan a book cover and have details auto-filled so that I don't have to type metadata.
- *As a reader*, I want to see YouTube videos about a book so that I can decide whether to read it next.
- *As a buyer*, I want to maintain a wishlist of books I don't own so that I remember them when shopping.
- *As a user*, I want recommendations grounded in what I already own so that I discover relevant new books.
- *As a mobile user*, I want to install Libra on my home screen and use it offline (read-only) so that it feels like a native app.

**ID.**
- *Sebagai kolektor*, saya ingin memindai sampul buku agar detail terisi otomatis tanpa mengetik manual.
- *Sebagai pembaca*, saya ingin melihat video YouTube tentang buku untuk menentukan bacaan berikutnya.
- *Sebagai pembeli*, saya ingin memelihara wishlist buku yang belum dimiliki agar tidak lupa saat berbelanja.
- *Sebagai pengguna*, saya ingin rekomendasi berbasis koleksi saya agar menemukan buku baru yang relevan.
- *Sebagai pengguna mobile*, saya ingin memasang Libra di home screen dan menggunakannya offline (read-only) agar terasa seperti aplikasi native.

---

## 7. Functional Requirements / Persyaratan Fungsional

### 7.1 Authentication / Autentikasi
- **MVP:** Email + password and Google OAuth via Supabase Auth. Email verification required.
- **Later:** Apple Sign-In, magic links, passkeys.

### 7.2 Add Book by Photo / Tambah Buku via Foto
- **MVP:** Camera capture & file upload (single image, ≤ 10 MB, JPEG/PNG/WebP). Client compresses before upload. Server-side AI extraction (Claude vision). Google Books lookup by title+author and by ISBN fallback. Editable confirmation form. Save on confirm. Failure → manual entry fallback.
- **Later:** Multi-cover batch scan; ISBN barcode scanner as alternate path.

### 7.3 YouTube Enrichment / Pengayaan YouTube
- **MVP:** Up to 5 videos per book using YouTube Data API v3 search `"{title} {author} book review"`; filter by `videoEmbeddable=true`; cache per book in DB.
- **Later:** User-curated playlist; remove/replace videos.

### 7.4 Library View / Tampilan Perpustakaan
- **MVP:** Grid (cover thumbnails) + list view; search by title/author; filter by status (owned, reading, finished); sort by date added.
- **Later:** Shelves/tags, custom collections, reading progress (page %).

### 7.5 Wishlist / Wishlist
- **MVP:** Add via search (Google Books), manual, or photo. Convert wishlist → owned on purchase. Optional notes & target price.
- **Later:** Price alerts via affiliate APIs.

### 7.6 Recommendations / Rekomendasi
- **MVP:** Top categories from owned books → Google Books "subject:" query → de-dup against owned + wishlist → present 10 cards.
- **Later:** LLM-generated personalized rationale per recommendation; "more like this" per book.

### 7.7 PWA Behaviors / Perilaku PWA
- **MVP:** Web app manifest, service worker (Serwist or next-pwa), installable prompt, offline shell, cached library list (last viewed), camera permission UX.
- **Later:** Background sync, push notifications for wishlist deals.

### 7.8 Settings / Pengaturan
- Profile, language (EN/ID), theme (light/dark/system), export library (CSV/JSON), delete account.

---

## 8. Non-Functional Requirements / Persyaratan Non-Fungsional

| Category | Requirement |
|---|---|
| Performance | LCP < 2.5s on 4G; initial JS < 200 KB gz; cover thumbnails lazy-loaded. |
| AI latency | Cover scan p95 ≤ 8s end-to-end. |
| Reliability | 99.5% monthly availability for read paths. |
| Security | RLS on all Supabase tables (users see only their own rows); server-only API keys (Anthropic, Google Books, YouTube) in Edge Functions; signed Storage URLs. |
| Privacy | Covers stored in user-scoped bucket; account deletion purges all user data within 30 days; compliant with Indonesia PDP Law (UU PDP) and GDPR principles. |
| Accessibility | WCAG 2.1 AA: color contrast, keyboard nav, screen-reader labels, captions for camera flow. |
| i18n | UI strings in EN & ID via next-intl or similar. |
| Devices | Modern Chromium, Safari iOS 16+, Firefox; responsive 320–1440px. |

---

## 9. Tech Stack & Architecture / Tumpukan Teknologi & Arsitektur

**Frontend**
- Next.js 15 (App Router, RSC) + React 19
- Tailwind CSS + shadcn/ui
- PWA via Serwist (or next-pwa)
- Camera capture via `<input type="file" capture="environment">` with `MediaDevices.getUserMedia` enhancement

**Backend / BaaS**
- Supabase Auth (email + OAuth)
- Supabase Postgres (RLS-enforced tables)
- Supabase Storage (private bucket `covers/`)
- Supabase Edge Functions for AI orchestration and external API calls

**External APIs**
- Anthropic Claude (vision) — extract title/author from cover image
- Google Books API — canonical metadata
- YouTube Data API v3 — related videos

**Hosting**
- Vercel (frontend + serverless), Supabase managed (DB, Storage, Functions)

**Observability**
- Vercel Analytics + Supabase logs; Sentry for error tracking.

---

## 10. Data Model / Model Data

```
users                 -- managed by Supabase Auth
  id (uuid, pk)
  email
  display_name
  created_at

books
  id (uuid, pk)
  user_id (uuid, fk users.id)
  title (text)
  authors (text[])
  isbn_13 (text, nullable)
  publisher (text, nullable)
  published_date (date, nullable)
  page_count (int, nullable)
  description (text, nullable)
  categories (text[])
  cover_url (text)              -- Storage path
  source_image_url (text)       -- original photo
  google_books_id (text, nullable)
  youtube_links (jsonb)         -- [{title, url, thumbnail, channel}]
  status (enum: owned|reading|finished)
  rating (smallint 1-5, nullable)
  notes (text, nullable)
  created_at, updated_at

wishlist
  id (uuid, pk)
  user_id (uuid, fk)
  title, authors, isbn_13, cover_url, google_books_id
  notes (text)
  target_price (numeric, nullable)
  created_at

recommendation_cache
  user_id (uuid, fk)
  payload (jsonb)
  generated_at
```

All tables enforce RLS: `auth.uid() = user_id`.

---

## 11. AI / Integration Flow / Alur AI

```
1. Client: user takes photo → compress → upload to Supabase Storage (private)
2. Client → Edge Function /scan-book { image_path }
3. Edge Function:
   a. Fetch signed URL for image
   b. Call Claude (vision) with prompt: "Return JSON {title, author, isbn?, confidence}"
   c. If confidence >= threshold:
        - Call Google Books API by ISBN, else by title+author
   d. Call YouTube Data API: search "{title} {author} book review"
        - Take top N where videoEmbeddable=true, captionsAvailable preferred
   e. Return merged payload to client
4. Client: prefill review form → user confirms → POST to /books (insert row, RLS)
```

Caching: Google Books + YouTube responses cached server-side by ISBN/title-hash for 30 days to reduce quota cost.

---

## 12. Success Metrics / Metrik Keberhasilan

| Metric | Target (3 months post-launch) |
|---|---|
| Activation: % of new users who save ≥1 book in first session | ≥ 60% |
| Scan auto-fill accuracy (title + author correct, no manual edit) | ≥ 85% |
| Median time to add a book via photo | ≤ 15s |
| D7 retention | ≥ 25% |
| Books added per active user per week | ≥ 1 |
| Wishlist → owned conversion (30-day) | ≥ 15% |
| Recommendation CTR (card → detail) | ≥ 10% |

---

## 13. Milestones / Tonggak

| Milestone | Scope | Target |
|---|---|---|
| **M1 — Foundation** | Next.js + Supabase scaffold, auth, manual book entry, library view, RLS, basic PWA manifest | Week 1–2 |
| **M2 — AI Scan** | Photo upload, Edge Function, Claude vision, Google Books enrichment, confirm form | Week 3–4 |
| **M3 — YouTube + Wishlist** | YouTube enrichment, wishlist CRUD, convert-to-owned | Week 5 |
| **M4 — Recommendations + Polish** | Rules-based recs, offline shell, install prompt, i18n EN/ID, settings/export | Week 6–7 |
| **M5 — Beta** | Internal testing, error tracking, performance pass, accessibility audit | Week 8 |

---

## 14. Risks & Open Questions / Risiko & Pertanyaan Terbuka

**Risks**
- **API cost** — Claude + YouTube quotas can scale unpredictably. *Mitigation:* aggressive caching, per-user rate limits, free-tier cap.
- **Cover-photo variability** — Poor lighting, multiple covers in frame, non-English titles. *Mitigation:* manual edit fallback, ISBN barcode path in v2.
- **Recommendation quality** — Rules-based may feel shallow. *Mitigation:* track CTR; iterate to LLM-based in v2.
- **Privacy/PDP-PI compliance** — Cover photos may include personal annotations. *Mitigation:* private storage, clear deletion flow.

**Open Questions**
- Pricing model: free with quota, or freemium (paid = unlimited scans)?
- Should Libra link to purchase sources (Tokopedia, Gramedia, Amazon)? Affiliate revenue path?
- Reading progress tracking (page %) in MVP or later?
- Family sharing of a library?

---

## 15. Out of Scope / Di Luar Lingkup

- Reading e-books inside the app.
- Audiobook playback.
- Social timeline, follow/friend graph.
- Buying/selling/lending marketplace.
- Library staff / institutional use.

---

## 16. Appendix / Lampiran

- Design files: _TBD (Figma link)_
- API references:
  - Anthropic Claude API — https://docs.claude.com/en/api
  - Google Books API — https://developers.google.com/books
  - YouTube Data API v3 — https://developers.google.com/youtube/v3
  - Supabase — https://supabase.com/docs
- Related: future v2 features doc — _TBD_

---

*End of PRD / Akhir PRD*
