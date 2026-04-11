# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Project

**HisaabKitaab** — India-first expense-splitting PWA (think Splitwise with UPI-native payments, rupee-first, multilingual).

- Repo: `https://github.com/Krishpotanwar/Hisab`
- Deploy: `https://hisab-rust.vercel.app`
- Source code lives in `HisaabKitab-merged/`

---

## Commands

All commands run from `HisaabKitab-merged/`:

```bash
npm run dev           # Start dev server at localhost:5173
npm run build         # Production build
npm run lint          # ESLint check
npm run test          # Run tests once (Vitest)
npm run test:watch    # Vitest watch mode

# Mobile
npm run cap:android   # Build + sync + open Android Studio
npm run cap:ios       # Build + sync + open Xcode
```

---

## Stack

| Layer | Tech |
|---|---|
| Frontend | React 18 + TypeScript + Vite 5 (SWC) |
| Styling | Tailwind CSS 3 + shadcn/ui (Radix UI) |
| Backend/Auth/DB | Supabase (Postgres + RLS + Auth + Edge Functions) |
| State | TanStack Query 5 + React Hook Form 7 |
| Payments | Razorpay UPI (Edge Function + Webhook) |
| OCR | Tesseract.js (client-side, no API key) |
| Routing | React Router DOM 6 |
| Mobile | Capacitor (Android/iOS) |
| Hosting | Vercel |

---

## Architecture

**Entry points:**
- `src/main.tsx` → React mount
- `src/App.tsx` → Router + QueryClient + AuthProvider setup
- `src/lib/auth.tsx` → `AuthContext` (sign up/in/out, password reset)

**Data flow pattern:** Pages call custom hooks in `src/hooks/` → hooks use TanStack Query with Supabase queries → Supabase enforces RLS per `auth.uid()`.

**Key directories:**
- `src/pages/` — Route-level components (12 pages: Dashboard, GroupDetail, Auth, Friends, Analytics, etc.)
- `src/components/` — Reusable UI; `src/components/ui/` contains shadcn/ui primitives
- `src/hooks/` — One hook file per domain (useGroups, useExpenses, useFriends, usePayments, useNotifications, useReceiptOcr, useAnalytics)
- `src/utils/` — Pure utilities: `debtSimplification.ts` (greedy netting), `splitMath.ts` (5 split modes), `exportData.ts`, `currency.ts`
- `src/integrations/supabase/` — `client.ts` (Supabase client init) + `types.ts` (generated schema types)
- `supabase/migrations/` — 18 SQL migration files; `combined_migrations_RUN_ONCE.sql` for fresh DB setup
- `supabase/functions/` — Edge Functions (Razorpay, notifications, etc.)

**Database tables:** `profiles`, `groups`, `group_members`, `expenses`, `expense_splits`, `settlements`, `pending_settlements`, `expense_audit_log`, `notifications`, `group_chat_messages`, `friendships`, `expense_comments`, `categories`.

**Split modes** (in `splitMath.ts`): equal, percentage, custom amount, shares, itemized.

**Debt netting** (in `debtSimplification.ts`): Greedy algorithm — compute net balance per user, match largest creditor with largest debtor until all balances reach zero.

---

## Coding Rules

1. **RLS is sacred** — never modify RLS policies without explicit instruction. Always verify queries respect the authenticated user's `auth.uid()`. Before writing any Supabase query, read the relevant migration file to confirm the schema.
2. **Audit log required** — every expense edit or delete must write a record to `expense_audit_log` (`user_id`, `action`, `timestamp`).
3. **SMS/notifications** — Android-only, always opt-in. Never auto-read or persist raw message text. iOS: share-sheet only, no background SMS reading.
4. **No secrets in source** — env vars only: `RAZORPAY_KEY_ID`, `RAZORPAY_KEY_SECRET`, `VITE_SUPABASE_URL`, `VITE_SUPABASE_PUBLISHABLE_KEY`.
5. **OCR** — do not persist raw receipt images beyond the processing window.

---

## Active Bug: Auth Email Redirect

**Symptom:** After sign-up, clicking the Supabase verification email lands on Vercel's login page instead of the app.

**Fix:**
1. Pass `emailRedirectTo: \`${window.location.origin}/auth/callback\`` in every `supabase.auth.signUp()` call.
2. `/auth/callback` route must call `supabase.auth.exchangeCodeForSession()`, then redirect to `/dashboard` on success.
3. Supabase dashboard → Auth → URL Configuration: set Site URL to `https://hisab-rust.vercel.app`; add `https://hisab-rust.vercel.app/**` and `http://localhost:5173/**` to Redirect URLs allowlist.
4. `vercel.json` must have catch-all rewrite: `{ "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }] }`.

**Acceptance test:** Sign up → receive email → click link → `/auth/callback` → auto-redirect to `/dashboard`. Must work on both production and localhost.

---

## Testing Requirements

- Unit tests for all `splitMath.ts` paths (equal, percent, shares, itemized, multiple payers).
- Unit tests for `debtSimplification.ts` — cover equal balances, single payer, circular debts (100% branch coverage).
- RLS security tests — assert user from group A cannot read expenses from group B.
- OCR golden fixture tests — sample receipt images + expected parsed output in `tests/fixtures/`.

---

## Feature Tags

When reading or planning features, tags indicate priority:
- `[MVP]` — build now
- `[NEXT]` — after MVP ships
- `[WOW]` — growth/delight
- `[RISKY]` — needs legal/privacy review before starting

<!-- code-review-graph MCP tools -->
## MCP Tools: code-review-graph

**IMPORTANT: This project has a knowledge graph. ALWAYS use the
code-review-graph MCP tools BEFORE using Grep/Glob/Read to explore
the codebase.** The graph is faster, cheaper (fewer tokens), and gives
you structural context (callers, dependents, test coverage) that file
scanning cannot.

### When to use graph tools FIRST

- **Exploring code**: `semantic_search_nodes` or `query_graph` instead of Grep
- **Understanding impact**: `get_impact_radius` instead of manually tracing imports
- **Code review**: `detect_changes` + `get_review_context` instead of reading entire files
- **Finding relationships**: `query_graph` with callers_of/callees_of/imports_of/tests_for
- **Architecture questions**: `get_architecture_overview` + `list_communities`

Fall back to Grep/Glob/Read **only** when the graph doesn't cover what you need.

### Key Tools

| Tool | Use when |
|------|----------|
| `detect_changes` | Reviewing code changes — gives risk-scored analysis |
| `get_review_context` | Need source snippets for review — token-efficient |
| `get_impact_radius` | Understanding blast radius of a change |
| `get_affected_flows` | Finding which execution paths are impacted |
| `query_graph` | Tracing callers, callees, imports, tests, dependencies |
| `semantic_search_nodes` | Finding functions/classes by name or keyword |
| `get_architecture_overview` | Understanding high-level codebase structure |
| `refactor_tool` | Planning renames, finding dead code |

### Workflow

1. The graph auto-updates on file changes (via hooks).
2. Use `detect_changes` for code review.
3. Use `get_affected_flows` to understand impact.
4. Use `query_graph` pattern="tests_for" to check coverage.

## Skill routing

When the user's request matches an available skill, ALWAYS invoke it using the Skill
tool as your FIRST action. Do NOT answer directly, do NOT use other tools first.
The skill has specialized workflows that produce better results than ad-hoc answers.

Key routing rules:
- Product ideas, "is this worth building", brainstorming → invoke office-hours
- Bugs, errors, "why is this broken", 500 errors → invoke investigate
- Ship, deploy, push, create PR → invoke ship
- QA, test the site, find bugs → invoke qa
- Code review, check my diff → invoke review
- Update docs after shipping → invoke document-release
- Weekly retro → invoke retro
- Design system, brand → invoke design-consultation
- Visual audit, design polish → invoke design-review
- Architecture review → invoke plan-eng-review
- Save progress, checkpoint, resume → invoke checkpoint
- Code quality, health check → invoke health
