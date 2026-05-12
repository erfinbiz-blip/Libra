# PRD Review — Libra (all features)

**Date:** 2026-05-12
**PRD version:** `1bce374` (`docs/prd.md`)
**Scope:** all features (F-01..F-13)
**Source of truth cross-checked:** `docs/status.md` (I-01 cost, I-02 rec quality, I-03 cover variability already tracked — not re-flagged below).

---

## Summary

| Feature | UX clarity | Agent-ready | Top gap |
|---|---|---|---|
| F-01 Auth | 🟡 | 🟡 | Password reset / verification retry / OAuth callback unspecified |
| F-02 Manual entry | 🟢 | 🟢 | Field validation rules not stated |
| F-03 Library view | 🟡 | 🟡 | Pagination + empty state missing |
| F-04 Photo capture/upload | 🟡 | 🟡 | Compression target + storage path scheme missing |
| F-05 AI scan | 🟡 | 🟡 | Confidence threshold + Claude model + cache table all unspecified |
| F-06 Google Books | 🟢 | 🟡 | Cache *location* (table vs KV) undefined |
| F-07 YouTube | 🟡 | 🟡 | Quota mgmt + zero-results empty state missing |
| F-08 Wishlist | 🟡 | 🟡 | Convert-to-owned semantics (move row vs flag) undefined |
| F-09 Recommendations | 🟡 | 🟡 | "Top categories" math + cache invalidation undefined |
| F-10 PWA | 🟡 | 🟡 | SW caching strategy + app-shell contents missing |
| F-11 i18n | 🟢 | 🟡 | Locale source of truth + detection logic missing |
| F-12 Settings | 🟡 | 🟡 | Export schema + delete-account grace period missing |
| F-13 Beta hardening | 🟢 | 🟢 | n/a (catch-all by design) |

**Headline:** No 🔴-blocked items. The PRD is solid as a *product* spec — vision, goals, success metrics, milestones are sharp. The pattern of gaps is consistent: **UX edge-states** (loading/error/empty/offline) and **mid-layer technical specs** (cache locations, validation rules, exact thresholds, endpoint shapes) are under-specified. An agent could start on F-01, F-02, F-13; everything else needs 1–3 clarifying questions before coding.

---

## A. User-flow audit

### A.1 Capture & Save Book (§5.1, §7.2, story 1)

- **Verdict:** 🟡
- **Gaps:**
  - "User taps 'Add Book', takes a photo of the cover (or uploads)." (§5.1) — Entry-point location unspecified (FAB? bottom nav? library header?).
  - "Failure → manual entry fallback." (§7.2) — What counts as failure? Who decides? Is the AI's best guess offered as a *seed* for manual entry, or thrown away?
  - "If confidence >= threshold" (§11 step 3c) — UX for sub-threshold case not described.
  - No loading-state description for the up-to-8s p95 scan (§8). Spinner? Skeleton? Stepwise progress?
  - Camera permission denial path absent. §7.7 mentions "camera permission UX" without describing it.
  - Multi-cover-in-frame risk flagged in §14 but no UI resolution (crop? "pick which book"?).
  - Offline behavior of Add-Book undefined (implicit: blocked — needs network for scan).
- **Suggested addition:** §5.1.1 sub-section "Add-Book happy path & error states" with a 5-step storyboard (open → permission → capture → progress states → review form → save) and explicit copy for low-confidence and permission-denied branches.

### A.2 YouTube discovery (story 2, §7.3)

- **Verdict:** 🟡
- **Gaps:**
  - "Up to 5 videos per book" (§7.3) — surface location (book detail page? embedded in confirm form?) not stated.
  - Zero-embeddable-results empty state absent.
  - Playback location: in-app embed vs deep-link to YouTube app? Not stated.
- **Suggested addition:** One line in §7.3 specifying placement + empty-state copy.

### A.3 Wishlist (§5.3, §7.5, story 3)

- **Verdict:** 🟡
- **Gaps:**
  - Entry point: separate tab? Filter in library? Not stated.
  - "Convert wishlist → owned on purchase" (§7.5) — carries over `notes`/`target_price`? Discards them? Reverse direction (owned → wishlist when sold/given away) supported?
- **Suggested addition:** §7.5 bullet on conversion semantics + reverse path.

### A.4 Recommendations (§5.2, §7.6, story 4)

- **Verdict:** 🟡
- **Gaps:**
  - When is the rec list recomputed? On every visit? Daily? Triggered by save?
  - Empty state for a brand-new user (0 owned books) absent.
  - "10 cards" (§7.6) — can user dismiss / mark "not interested"?
- **Suggested addition:** §7.6 bullet on recompute trigger + empty state.

### A.5 PWA install + offline (story 5, §7.7)

- **Verdict:** 🟡
- **Gaps:**
  - Install-prompt trigger timing unspecified.
  - Offline read-only scope unclear: "cached library list (last viewed)" — does it include book detail pages? Recommendations? Settings?
  - Online/offline indicator not mentioned.
  - Sync-back-online behavior unspecified.
- **Suggested addition:** §7.7 bullet enumerating offline-cached surfaces and install-prompt trigger.

### A.6 Authentication (§7.1) — user-facing flow not in §5/§6

- **Verdict:** 🟡
- **Gaps:**
  - Password reset flow absent.
  - Email-verification retry / "resend verification" path absent.
  - OAuth account-collision (same email signs up with Google after using email/password) unspecified.
- **Suggested addition:** §7.1 add bullets for password reset, resend-verification, OAuth collision.

### A.7 Settings — Delete account (§7.8)

- **Verdict:** 🟡
- **Gaps:**
  - §8 says deletion "purges all user data within 30 days" — is there a 30-day undo grace window, or is it a back-end purge SLA? UX implication is opposite.
  - Confirmation flow (re-enter password? typed confirmation?) absent.
- **Suggested addition:** §7.8 add a delete-account sub-bullet specifying confirmation UX and whether the 30 days is a grace period or a back-end SLA.

---

## B. Technical clarity audit

### F-01 Authentication (§7.1)
- **Verdict:** 🟡
- Data model: ✅ — Supabase Auth users
- APIs: ⚠️ — Sign-up/sign-in route names + OAuth callback URL not given
- External integrations: ✅ — Supabase Auth
- State management: ⚠️ — Server-side session handling (`@supabase/ssr` middleware? Server Actions?) not specified
- Edge cases: ⚠️ — Verification expiry, OAuth collision, locked accounts
- Acceptance criteria: ⚠️ — "Email verification required" not observable as written
- **Questions an agent would ask:**
  1. Use `@supabase/ssr` middleware pattern?
  2. Where does the OAuth callback land?
  3. Resend-verification endpoint?

### F-02 Manual book entry (§7.4 form, §10 schema)
- **Verdict:** 🟢
- Data model: ✅ — `books` table fully enumerated
- APIs: ⚠️ — POST shape not given but implicit (PostgREST insert)
- Edge cases: ⚠️ — Field validation rules (required/optional, max lengths, ISBN-13 checksum?) not stated
- **Questions:**
  1. Which fields are required at insert time?
  2. Validate ISBN-13 checksum client-side?

### F-03 Library view (§7.4)
- **Verdict:** 🟡
- APIs: ⚠️ — Pagination strategy + page size not given
- State management: ⚠️ — Server-side search via Postgres `ilike`/`tsvector` vs client-side filter?
- Edge cases: ⚠️ — Empty-library state copy
- **Questions:**
  1. Search: full-text index or simple `ilike`?
  2. Page size + scroll vs pagination?
  3. Empty-state copy in EN+ID?

### F-04 Photo capture + upload (§7.2, §9)
- **Verdict:** 🟡
- Data model: ✅ — `source_image_url`
- External integrations: ⚠️ — Compression target (max-edge px? JPEG quality?) not given
- State management: ⚠️ — Storage path scheme not given
- Edge cases: ⚠️ — Upload-failure retry; mime mismatch
- **Questions:**
  1. Compress to what max dimension and quality?
  2. Storage object path convention?
  3. Signed-URL TTL?

### F-05 AI cover scan (§7.2, §11)
- **Verdict:** 🟡 — biggest single gap source
- Data model: ❌ — §11 promises "Google Books + YouTube responses cached server-side by ISBN/title-hash for 30 days" but §10 has **no** `scan_cache` table
- APIs: ⚠️ — `/scan-book` named but request/response shapes only pseudocoded; no status codes
- External integrations: ⚠️ — Claude model not specified (Haiku/Sonnet/Opus?); exact prompt not given; confidence threshold value not given
- Edge cases: ⚠️ — Claude returns invalid JSON / no match / quota exhausted not handled
- Acceptance criteria: ✅ — Quantified (85% auto-fill, ≤8s p95, ≤15s e2e)
- **Questions:**
  1. Cache: Postgres table or external KV?
  2. Which Claude model?
  3. Exact vision prompt + JSON schema?
  4. Confidence threshold value?
  5. Invalid-JSON retry/repair logic?
  6. Per-user rate limit value?

### F-06 Google Books enrichment (§7.3, §11)
- **Verdict:** 🟡
- External integrations: ⚠️ — Endpoint variant ok-ish; rate-limit budget not stated (default 1000/day)
- State management: ❌ — Cache location undefined (see F-05)
- **Questions:**
  1. Which `volumes` endpoint variant — `?q=intitle:…+inauthor:…` or `?q=isbn:…`?
  2. Cache table + key shape (`title_hash` how computed)?

### F-07 YouTube Data API (§7.3, §11)
- **Verdict:** 🟡
- External integrations: ⚠️ — Quota (10k units/day free tier; `search.list` costs 100 units → 100 scans/day) not surfaced as a constraint
- Edge cases: ⚠️ — Zero embeddable results / quota-exceeded response
- **Questions:**
  1. On quota exhaustion: cached-only mode, queue, or hide section?
  2. "captionsAvailable preferred" — re-query or sort client-side?

### F-08 Wishlist (§5.3, §7.5, §10)
- **Verdict:** 🟡
- Data model: ✅ — `wishlist` table, but...
- APIs: ❌ — Convert-to-owned: separate tables (`books` vs `wishlist`) imply delete+insert, but transaction semantics, FK carryover, and audit trail not stated
- **Questions:**
  1. Convert = delete-from-wishlist + insert-into-books in one transaction, carrying notes/cover_url?
  2. Reverse path?

### F-09 Recommendations (§5.2, §7.6, §10)
- **Verdict:** 🟡
- Data model: ✅ — `recommendation_cache.payload jsonb` but TTL/invalidation absent
- APIs: ⚠️ — Algorithm under-specified: "Top categories from owned books" — how many top? Weighted by frequency? Recency-decayed?
- State management: ⚠️ — Cache invalidation on save / wishlist-add not stated
- **Questions:**
  1. Top-N categories — value of N?
  2. Cache TTL + invalidation triggers?
  3. Dismissed-recs storage?

### F-10 PWA (§7.7, §9)
- **Verdict:** 🟡
- External integrations: ⚠️ — Serwist *or* next-pwa — pick one; SW caching strategy per route not stated
- State management: ⚠️ — App-shell contents not enumerated
- **Questions:**
  1. Serwist or next-pwa?
  2. Which routes are SW-cached and with what strategy?

### F-11 i18n (§7.8 settings, §8)
- **Verdict:** 🟡
- External integrations: ⚠️ — "next-intl or similar" — pick; translation file structure not stated; locale detection not stated
- **Questions:**
  1. next-intl confirmed?
  2. URL-prefix locale or cookie?
  3. Default locale?

### F-12 Settings — export & delete (§7.8, §8)
- **Verdict:** 🟡
- APIs: ⚠️ — Export schema (CSV columns? JSON shape? includes wishlist + notes?) not given
- Edge cases: ⚠️ — Delete-account: §8 "within 30 days" — sync or async job? Cascade order?
- **Questions:**
  1. Export schema spec?
  2. Delete: immediate soft-delete + async purge, or synchronous?
  3. Cascade order: Storage objects → DB rows?

### F-13 Beta hardening (§13 M5)
- **Verdict:** 🟢 — Catchall checklist; no further spec needed at PRD level.

---

## Cross-cutting gaps

1. **Missing cache layer in §10.** §11 promises 30-day caching of Google Books + YouTube; §10 has no table for it. Either add `scan_cache(key text pk, source enum, payload jsonb, fetched_at, expires_at)` or specify external KV. **Highest-priority gap** — blocks F-05/F-06/F-07.
2. **No index list.** None of `books.user_id`, `wishlist.user_id`, `books.isbn_13`, or `recommendation_cache.user_id` are flagged for indexing.
3. **Validation rules per field absent.** Title max length, ISBN-13 checksum, `rating` 1–5 enforced at app or DB?
4. **Edge Function endpoint catalog incomplete.** Only `/scan-book` is named. No endpoint for recommendations recompute, wishlist conversion, or export.
5. **Rate limiting absent.** §14 mentions "per-user rate limits" as mitigation but no concrete budget.
6. **Loading/error/empty state copy absent.** Affects every UX flow.

---

## Proposed PRD edits

```diff
@@ docs/prd.md §10 Data Model (after wishlist) @@
+
+scan_cache
+  key (text, pk)              -- normalized hash: sha1(lower(title+author)) OR isbn_13
+  source (enum: google_books|youtube)
+  payload (jsonb)
+  fetched_at (timestamptz)
+  expires_at (timestamptz)    -- fetched_at + 30 days
```

```diff
@@ docs/prd.md §11 AI Flow (step 3b) @@
- b. Call Claude (vision) with prompt: "Return JSON {title, author, isbn?, confidence}"
+ b. Call Claude (model: claude-haiku-4-5 for cost / claude-sonnet-4-6 for accuracy — pick at deploy) with a vision prompt returning a strict JSON envelope:
+    { "title": string, "author": string, "isbn_13": string|null, "confidence": number 0-1 }
+    Confidence threshold for auto-prefill: 0.7. Below → present AI guess as a *seed* for the manual form with a "low confidence" badge.
```

```diff
@@ docs/prd.md §7.2 Add Book by Photo @@
- **MVP:** Camera capture & file upload (single image, ≤ 10 MB, JPEG/PNG/WebP). Client compresses before upload. Server-side AI extraction (Claude vision). Google Books lookup by title+author and by ISBN fallback. Editable confirmation form. Save on confirm. Failure → manual entry fallback.
+ **MVP:** Camera capture & file upload (single image, ≤ 10 MB, JPEG/PNG/WebP).
+   - Client compresses to max-edge 1600px, JPEG quality 0.85.
+   - Storage path: `covers/{user_id}/{book_id}.jpg`; signed URL TTL = 60s for server fetch.
+   - Server-side AI extraction (Claude vision; see §11).
+   - Google Books lookup: by ISBN first, fallback to title+author.
+   - Editable confirmation form; save on confirm.
+   - Failure paths:
+     - Camera permission denied → file-picker fallback + helper text.
+     - Low AI confidence (<0.7) → AI guess pre-fills form as a seed with a "low confidence" badge.
+     - All extraction fails → fully blank manual entry form.
+   - Per-user rate limit: 20 scans/day on free tier.
```

```diff
@@ docs/prd.md §7.5 Wishlist @@
- **MVP:** Add via search (Google Books), manual, or photo. Convert wishlist → owned on purchase. Optional notes & target price.
+ **MVP:** Add via search (Google Books), manual, or photo. Convert wishlist → owned on purchase (transactional: insert into `books`, copy `notes` and `cover_url`, delete from `wishlist`). Owned → wishlist supported via book detail menu. Optional notes & target price.
```

```diff
@@ docs/prd.md §7.7 PWA Behaviors @@
- **MVP:** Web app manifest, service worker (Serwist or next-pwa), installable prompt, offline shell, cached library list (last viewed), camera permission UX.
+ **MVP:**
+   - Web app manifest + icons (192/512/maskable).
+   - Service worker via Serwist (default). Caching strategies:
+     - App shell + static assets: cache-first.
+     - Library list + book detail: stale-while-revalidate.
+     - Edge Functions (`/scan-book`, etc.): network-only.
+   - Installable prompt: shown on second visit after first book saved.
+   - Offline shell: library list (last 50 viewed) + book detail; recommendations not cached.
+   - Online/offline status badge in header.
+   - Camera permission UX: pre-prompt explainer modal before triggering the browser prompt.
```

```diff
@@ docs/prd.md §7.8 Settings @@
- Profile, language (EN/ID), theme (light/dark/system), export library (CSV/JSON), delete account.
+ - Profile (display name).
+ - Language (EN/ID).
+ - Theme (light/dark/system).
+ - Export library: synchronous file download. CSV columns: `title, authors, isbn_13, status, rating, created_at`. JSON: array of full `books` rows + `wishlist` rows.
+ - Delete account: requires typed confirmation ("DELETE"). Soft-deletes immediately (account unusable); async purge of Storage objects + DB rows within 30 days (PDP/GDPR SLA, not a user-facing grace period).
```

---

## Proposed `docs/status.md` updates

Add to **Open Issues**:

| ID | Issue | Severity | Owner | Mitigation | Opened |
|---|---|---|---|---|---|
| I-04 | PRD missing `scan_cache` table despite §11 promising 30-day caching | high | — | Add to §10; pick storage (Postgres vs KV) before M2 | 2026-05-12 |
| I-05 | Loading/error/empty state copy missing across all flows | medium | — | Add per-flow storyboards or a separate UX-states doc before M1 | 2026-05-12 |
| I-06 | Claude model + confidence threshold + exact prompt not pinned in §11 | high | — | Decide Haiku vs Sonnet; pin threshold; commit prompt to repo | 2026-05-12 |
| I-07 | Rate-limit budgets (Claude, Google Books, YouTube) not concretized | medium | — | Set per-user free-tier caps; surface to user as quota meter | 2026-05-12 |

Append to **Dev Log** under the 2026-05-12 entry:
- **Done (additional):** First PRD review run via `prd-review` skill — produced `docs/qa-reports/2026-05-12-prd-review.md`. No blocked features; 11/13 features need 1–3 clarifications before coding.
- **Next (additional):** Apply proposed §10/§11/§7.2/§7.5/§7.7/§7.8 edits; pick scan-cache storage + Claude model.
