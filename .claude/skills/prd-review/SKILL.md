---
name: prd-review
description: Use when the user asks to review, audit, or assess the PRD itself (not the implementation). Two audits in one report — (A) user-flow clarity ("is the UX path easy to follow?"), (B) developer-readiness ("can an agent start coding from this without clarifying questions?"). Triggers on phrases like "review the PRD", "is the PRD ready", "assess the user flow", "is the PRD clear enough for the agent to code", "audit PRD", "PRD readiness check". Project: Libra book-library PWA — anchors on PRD §5/§6/§7/§9/§10/§11/§14.
---

# Libra PRD Review Skill

You are auditing `docs/prd.md` as an artifact — *before* code is written. The question is not "does the code match the PRD?" (that's `prd-qa`) but "is this PRD itself good enough?". Two audits, one report. Be concrete, quote the PRD verbatim, and never paraphrase when citing.

## Inputs

1. `docs/prd.md` — the artifact under review.
2. `docs/status.md` — open issues + decision log (so already-known gaps aren't re-flagged as new).

## Scope

- Default: audit every feature and user flow.
- If the user named a feature (e.g. "F-05", "AI scan", "wishlist"), audit only that one.

## Audit A — User-flow clarity

For each user journey in §5 Core Features and §6 User Stories, evaluate:

- **Entry point** — Is it explicit how the user *reaches* this flow?
- **Happy path** — Walked through step by step?
- **States** — Empty / loading / error / offline states described?
- **Reversibility** — Are critical actions undoable (edit, delete, undo)?
- **PWA behaviors** — Install prompt, offline reads, sync described where they matter? (cross-ref §7.7)
- **Accessibility** — Mentioned where it affects the flow (keyboard, screen reader, motor)?

Mark each flow: 🟢 clear / 🟡 has gaps / 🔴 unclear. Quote the specific PRD text and explain what's missing.

## Audit B — Technical clarity (developer-readiness)

For each feature in §7, evaluate whether an agent can start coding without asking back:

- **Data model** — All involved tables, columns, types, FKs, RLS policies specified? (cross-ref §10)
- **APIs / contracts** — Endpoints, methods, request/response shapes, status codes specified?
- **External integrations** — Provider, endpoint, auth method, rate limits, fallback specified? (Google Books, YouTube, Claude vision — cross-ref §9, §11)
- **State management** — Client vs server state, caching, invalidation described?
- **Edge cases** — Failures, timeouts, empty results, offline, rate-limit responses described?
- **Acceptance criteria** — Testable? Each requirement should be observable/verifiable.
- **Open questions** — Unresolved decisions explicitly flagged (vs glossed over)? Cross-ref §14.

Mark each feature: 🟢 agent-ready / 🟡 needs clarification / 🔴 blocked. List the specific clarifying questions an agent would have to ask.

## Output

Write to `docs/qa-reports/YYYY-MM-DD-prd-review.md` (or `YYYY-MM-DD-prd-review-<feature>.md` if scoped). Use today's date.

```markdown
# PRD Review — <scope>

**Date:** YYYY-MM-DD
**PRD version:** <short commit hash of docs/prd.md>
**Scope:** <all features | F-XX>

## Summary

| Feature | UX clarity | Agent-ready | Top gap |
|---|---|---|---|
| F-XX | 🟢/🟡/🔴 | 🟢/🟡/🔴 | <one-line> |

## A. User-flow audit

### <flow name from §5 or §6>

- **Verdict:** 🟢 / 🟡 / 🔴
- **Gaps:**
  - "<verbatim quote from PRD>" — <what's missing>
- **Suggested addition:** <one sentence the user could paste into the PRD>

## B. Technical clarity audit

### F-XX — <feature>

- **Verdict:** 🟢 / 🟡 / 🔴
- Data model: ✅/⚠️/❌ — <note>
- APIs: ✅/⚠️/❌ — <note>
- External integrations: ✅/⚠️/❌ — <note>
- State management: ✅/⚠️/❌ — <note>
- Edge cases: ✅/⚠️/❌ — <note>
- Acceptance criteria: ✅/⚠️/❌ — <note>
- **Specific questions an agent would ask:**
  1. …
  2. …

## Proposed PRD edits

```diff
@@ docs/prd.md §7.2 @@
- <existing line>
+ <clarified line>
```

(unified diff snippets per gap, ready to apply)

## Proposed status.md updates

Add to Open Issues:
- <new issue surfaced by this review>
```

## After writing

1. Print the report path.
2. Show the proposed PRD diff and status.md additions inline.
3. **Do not auto-edit `docs/prd.md` or `docs/status.md`.** Ask the user to confirm before applying.

## Tone

Concise. Quote-evidence: "the PRD says X at §7.2, but doesn't specify Y". Never paraphrase the PRD when citing it. Bilingual headers OK to mirror the PRD's style, but findings stay in one language for clarity (English unless the user is writing in Indonesian).
