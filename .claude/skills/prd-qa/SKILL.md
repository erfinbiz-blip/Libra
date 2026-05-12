---
name: prd-qa
description: Use when the user asks to QA, audit, verify, or "review against the PRD"; when checking whether a feature implementation matches requirements; before flipping a feature status to ready-qa/live in docs/status.md; or after substantial commits on a feature branch. Project-specific to Libra (book-library PWA, Next.js + Supabase + Claude vision AI scan). Triggers on phrases like "review F-XX", "QA the scan flow", "does this match the PRD", "check if feature X is done".
---

# Libra PRD QA Skill

You are auditing implemented code against `docs/prd.md`. Be evidence-based, concise, and never speculate — every claim cites `file_path:line_number`.

## Inputs (read in this order)

1. `docs/prd.md` — source of truth (sections §7 Functional Requirements, §10 Data Model, §11 AI Flow are the audit anchors).
2. `docs/status.md` — current feature statuses (F-01..F-13), milestones (M1–M5), open issues, decision log.
3. `git status` and `git diff main...HEAD` — what's changed on the current branch.
4. Relevant code: Next.js routes under `app/` or `src/app/`, Supabase migrations under `supabase/migrations/`, Edge Functions under `supabase/functions/`.

## Scope detection

- If the user named a feature (e.g. "F-05", "AI scan", "import CSV"), audit ONLY that feature.
- Otherwise, audit every feature whose status in `status.md` is `in-progress` or `ready-qa`. Skip `on-dev` (not started) and `live` (already verified) unless explicitly asked.
- If nothing is in-progress, do a baseline report confirming the current `on-dev` snapshot and stop.

## Checks

### 1. Functional requirements

For each in-scope feature, extract its bullet from PRD §7 and locate the implementing code. Mark each requirement:

- ✅ **pass** — code present, behavior matches
- ⚠️ **partial** — code present but missing a sub-requirement
- ❌ **miss** — no implementation found

Cite the evidence file + line for every pass/partial.

### 2. Data model

Use the Supabase MCP tools:

- `list_tables` — current tables
- `list_migrations` — migration history
- Compare against PRD §10 (tables: `profiles`, `books`, `user_books`, `notes`, `tags`, `book_tags`, `ai_scan_cache`, etc. — check the actual PRD for the canonical list).

Flag: missing tables/columns, type mismatches, missing RLS policies, missing indexes called out in the PRD.

### 3. AI flow

If F-05 (AI scan) is in scope:

- `list_edge_functions` then `get_edge_function` for `scan-book` (or equivalent name).
- Verify the sequence in PRD §11: image upload → signed URL → Claude vision call → Google Books enrichment → YouTube enrichment → cache write → return.
- Flag: missing caching (`ai_scan_cache`), missing signed-URL handling, missing error fallbacks, unsafe direct API key exposure.

## Output

Write a report to `docs/qa-reports/YYYY-MM-DD-<short-topic>.md` (use today's date, kebab-case topic, e.g. `2026-05-12-f05-ai-scan.md`). Create the folder if needed.

**Report structure:**

```markdown
# QA Report — <topic>

**Date:** YYYY-MM-DD
**Branch:** <branch>
**Scope:** <features audited>
**Commits reviewed:** <range or list>

## Summary

| Feature | Verdict | Pass | Partial | Miss |
|---|---|---|---|---|
| F-XX | ✅ / ⚠️ / ❌ | n | n | n |

## Findings

### F-XX — <feature name>

- **PRD requirement:** <verbatim bullet from §7>
  - **Evidence:** `path/to/file.ts:42` — <one-line description>
  - **Verdict:** ✅ pass

(repeat per requirement)

## Schema diff

<table-by-table comparison; cite migration files>

## AI flow check

<step-by-step trace if applicable>

## Proposed status.md updates

```diff
- | F-05 | AI scan | in-progress | ... |
+ | F-05 | AI scan | ready-qa    | ... |
```

## Open issues / new decisions

- <bullet>
```

## After writing the file

1. Print the report path.
2. Show the proposed `status.md` diff inline.
3. **Do not auto-edit `status.md`.** Ask the user to confirm before applying.

## Tone

Concise. Evidence > opinion. Cite `file:line` for every claim. If something is unverifiable from code alone (e.g., UX feel, a11y), say so explicitly rather than guessing.
