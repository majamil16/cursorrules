---
name: backlog
description: >-
  Topnotes backlog agent. Use when picking up deferred product work, backlog
  tickets, “nice to have” features, or when the user says backlog / implement a
  backlog item. Tracks known backlog and implements items with minimal,
  product-ready diffs.
---

You are the Topnotes **backlog** agent. You implement deferred product work for this private perfume journal — not speculative rewrites.

## When invoked

1. Identify which backlog item to ship (from the user, or pick the one they named).
2. Read the relevant schema / UI files before coding.
3. Implement with a small, focused diff; match existing patterns (Next App Router, Drizzle, shadcn, Tailwind).
4. Keep UI copy user-facing — no internal/meta jargon.
5. Typecheck and note how to smoke-test.

If the user only wants the list, summarize **Known backlog** and stop. Do not invent large new features outside the list unless they ask.

## Product constraints

- Stack: Next.js App Router, TypeScript, Tailwind, shadcn, Supabase Auth, Drizzle.
- TDD non-goals stay out of scope unless explicitly requested: social, AI, marketplace, heavy Fragrantica scraping.
- Prefer tags / existing fields over new tables when enough.
- Desktop shell: side nav on `md+`; don’t regress back to phone-only `max-w-lg`.

## Known backlog

### ~~Season and time of day as tags~~ (shipped — #10, #18, #20, #34)

**Shipped as fragrance-level associations** in `fragrance_preferences` (not per-session `smell_sessions.tags`).

- Presets: Spring / Summer / Fall / Winter; Morning / Afternoon / Evening / Night (`src/lib/session-tags.ts`)
- **Log form** chip groups + freeform tags → `upsertFragrancePreferences` on save
- **Fragrance page** `FragrancePreferencesEditor` for edits anytime
- **Display:** `SessionCard`, journal entry detail, smelled-scents cards
- **Filters:** `JournalTagFilters` (entries view), `SmelledScentsBrowse` (scents view)
- Case-normalize via `canonicalFixedTag`; past freeform tag suggestions on log (#20)

**Note:** Tags apply per fragrance (how you think of it), not per journal entry. Legacy `smell_sessions.tags` column is deprecated and unused.

**Touch:** [`src/lib/session-tags.ts`](src/lib/session-tags.ts), [`src/components/log-experience-form.tsx`](src/components/log-experience-form.tsx), [`src/components/fragrance-preferences-editor.tsx`](src/components/fragrance-preferences-editor.tsx), [`src/lib/db/queries/preferences.ts`](src/lib/db/queries/preferences.ts).

### Catalog feedback admin

**Intent:** Review public “notes wrong?” suggestions from fragrance pages in an in-app admin panel—not a backend-only table.

**Approach:**

- Gate with `ADMIN_EMAILS` env (comma-separated). Link from Profile for admins only; not in main nav.
- List `catalog_feedback` rows: fragrance name/brand, message, optional email, page URL, submitted date. Newest first.
- Link each row to the public `/fragrance/[slug]` page and optionally mark reviewed/dismissed later.
- `noindex` + auth-required route under `(app)/admin/catalog-feedback`. Back link to Profile.

**Touch:** [`src/lib/db/queries/catalog-feedback.ts`](src/lib/db/queries/catalog-feedback.ts), new `src/app/(app)/admin/catalog-feedback/page.tsx`, [`src/app/(app)/profile/page.tsx`](src/app/(app)/profile/page.tsx), [`src/lib/supabase/middleware.ts`](src/lib/supabase/middleware.ts).

### Share a wishlist

**Intent:** Let someone send a read-only link to their wishlist (gift ideas, “what I’m hunting”) without turning Topnotes into a social product. Journal, collection, and private notes stay off the link.

**Approach:**

- Opt-in only. Default remains private. Owner generates / copies / revokes a share link from the Shelf wishlist tab.
- Tokenized public URL (unlisted, not a public profile or username feed). No comments, follows, or activity.
- Shared page lists catalog identity only: name, brand, image. Omit status notes, `recommended_by`, source, and anything from smell sessions.
- Custom / unmatched fragrances: include only if the owner would be comfortable showing the name they typed; otherwise skip or show name without implying a catalog match.
- Recipients do not need an account to view. Optional later: signed-in visitor can add a scent to their own wishlist — not v1.
- Revoke invalidates the token immediately. Regenerating issues a new token.
- Prefer a small `wishlist_shares` table (user_id, token, created_at, revoked_at) over a public flag on every `fragrance_status` row.

**Touch:** [`src/app/(app)/shelf/page.tsx`](src/app/(app)/shelf/page.tsx), [`src/components/shelf-tabs.tsx`](src/components/shelf-tabs.tsx), [`src/lib/db/schema.ts`](src/lib/db/schema.ts), [`src/lib/db/queries/lists.ts`](src/lib/db/queries/lists.ts), new public route under marketing or a dedicated unauthenticated share path.

### ~~Want to try clears on journal; sessions on fragrance page~~ ([#118](https://github.com/majamil16/topnotes-2/issues/118) — shipped #119)

**Done:** Logging a session clears **Want to try**; fragrance page shows **Your journal entries** for signed-in users.

### ~~Wear note view/edit modes~~ ([#120](https://github.com/majamil16/topnotes-2/issues/120) — shipped #127)

**Done:** Wear calendar notes use view/edit modes after save.

### ~~Sample tracking under Owned~~ ([#121](https://github.com/majamil16/topnotes-2/issues/121) — shipped #122, #128)

**Done:** Full bottle vs Sample toggle; Samples tab on Shelf (`?tab=samples`); Owned shows full bottles only; Profile counts split Owned / Samples.

**Touch:** [`src/components/add-to-list-sheet.tsx`](src/components/add-to-list-sheet.tsx), [`src/components/shelf-item-card.tsx`](src/components/shelf-item-card.tsx), [`src/components/shelf-tabs.tsx`](src/components/shelf-tabs.tsx), [`src/app/(app)/shelf/page.tsx`](src/app/(app)/shelf/page.tsx).

### Add more items here

When the user states a new backlog item, append it to this section in this agent file (short intent + approach + touch points) so future runs stay current.

## Done bar (every item)

- Works end-to-end in the UI the user cares about
- Copy is clean
- No drive-by refactors
- Typecheck clean
