# Full-Stack: Data, Access Rules, Edge Cases, Deploy

> Module 5 · Full-Stack. Add data schemas, access rules, and edge cases; stress-test and deploy.

## Deployed link

[_The working, shareable link that survives real users._](https://crm-optimizations.lovable.app)

# Rep Home — Integration Plan

**Version:** 1.0 · **Date:** 20 October 2026 · **Status:** Plan only — nothing in this document is built or approved yet. Each build prompt below is a separate, sequential piece of work that needs its own go-ahead.

---

## 1. Current status

**Frontend (built, working):**
- One content screen, **Today**, at `/`. Route file owns metadata only; the screen lives in `src/features/today/`.
- Seven invented demo deals in React state. Overdue-first ordering, stage + Favorites filters, A–Z / Z–A sorting, icon-only logging (call / email / meet / snooze), optional note, next-step chips (Tomorrow / Next week / Pick date), Done today list, Undo toast, avg-time and avg-taps counters.
- Data, domain logic, hooks, and display are separated; 12 passing tests cover filters, sort, complete, undo, and metrics.
- Decorative-only affordances (Pipeline, Accounts, Deals, Reports, Help, Manager view, Settings, Search, breadcrumb, header icons) have no behavior and stay that way without new scope approval.

**Backend (schema exists, app not connected):**
- Lovable Cloud is on. Four owner-protected tables exist: `leads`, `follow_ups`, `activities`, `favorites` — all with row-level security, owner-only policies, explicit grants, and composite lead/owner foreign keys that prevent cross-owner references.
- The seven demo leads and seven open follow-ups are loaded under an isolated demo owner ID (`d3100000-…`). Activities and favorites are empty. No real accounts exist.
- **The app does not read or write the database.** The screen still runs on its in-memory demo; refresh erases all work. There is no sign-in, no profiles, no data-fetching code.

**The gap this plan closes:** connecting the working Today screen to the existing database, with real sign-in, so a rep's work survives refresh and the adoption experiment can actually be measured.

---

## 2. Data audit — hardcoded values to replace

Every value below is currently invented or hardcoded. The "Becomes" column says what replaces it when integrated.

| # | Hardcoded value | Where it lives | Becomes |
|---|---|---|---|
| 1 | Seven demo deals (companies, contacts, values, stages, reasons) | `data/demo-deals.ts` | Rows from `leads` + open `follow_ups` for the signed-in rep |
| 2 | Rep name "Jordan" and "Good afternoon" greeting | `TodayScreen.tsx` | Signed-in user's profile name; time-of-day greeting from local time |
| 3 | `overdue: true/false` flags on deals 1–2 | `data/demo-deals.ts` | Derived at read time: `due_at < start of today` in the agreed time zone |
| 4 | Fixed due dates on demo follow-ups (Oct 13/14/15) | database demo rows | Real `due_at` timestamps; demo rows reassigned to a real account or replaced |
| 5 | `saved` boolean on the Deal type | `types.ts`, `selectors.ts` | Existence of a `favorites` row for (owner, lead) |
| 6 | `done` object on the Deal type | `types.ts` | `activities` rows + `follow_ups.completed_at`, excluding voided activities |
| 7 | Next-step labels "Tomorrow" / "Next week" / raw date string | `data/options.ts`, card component | Resolved to real `next_due_at` timestamps that create a new follow-up row |
| 8 | `seconds` measured from card mount | `use-follow-up-logging.ts` | Measured from first tap on the card (or dropped as a metric) |
| 9 | `taps` assigned by path (1 or 2), not counted | `use-follow-up-logging.ts` | Real tap counter, or dropped as a metric |
| 10 | Deal IDs as numbers 1–7 | `types.ts`, `data/demo-deals.ts` | Database UUIDs |
| 11 | Value formatting hardcoded to "$" / en-US | `format.ts` | `currency_code` on the lead, with a confirmed default currency |
| 12 | "All clear." empty state | `TodayScreen.tsx` | Must distinguish "nothing due today" from "filter matches nothing" |
| 13 | Open-count badge on the person icon | `TodayScreen.tsx` | Count of the signed-in rep's open follow-ups due today |
| 14 | Stage list and action list as constants | `data/options.ts` | Keep as constants — they match the database CHECK constraints; change both together if ever revised |

**Decisions — resolved 20 October 2026:**
1. **Time zone for "today" and "overdue"** — one shared zone for the whole team: **America/Chicago**. Not per-rep device zones.
2. **Default currency** — **USD only**. All deal values display as dollars; no per-deal currencies.
3. **Profile data** — **name only**. Store a display name per rep for the greeting; no photos or roles. A `profiles` table is required.
4. **Demo data fate** — **reassign the seven demo rows to the user's real account** on sign-in, so the screen matches the tested demo.

---

## 3. Schema to add

The four core tables already exist. Integration needs **additions**, not rebuilds:

**a) `profiles` table (only if decision 3 above says yes)**
- `id` (references the auth user, deleted with the account), display name, optional time zone, created/updated timestamps.
- Created automatically when a new user signs up, via a database trigger on new auth users.

**b) Column/backfill work on existing tables — none required.** The existing columns already cover everything the screen shows. Confirmed mapping:

| Screen element | Source |
|---|---|
| Company, contact, value, stage | `leads` |
| Reason, due date, overdue (derived) | `follow_ups` (open rows: `completed_at IS NULL`) |
| Heart / Favorites filter | `favorites` |
| Done today list, action, note, next step | `activities` + `follow_ups.completed_at` |
| Avg time / avg taps | `activities.duration_seconds`, `activities.tap_count` |

**c) Atomic operations (database functions).** The riskiest writes must be single operations, not separate requests:
- **Log activity:** insert the `activities` row AND mark the follow-up complete AND insert the next follow-up (when a next step was chosen) — all-or-nothing.
- **Undo:** void the activity, reopen the completed follow-up, and delete the next follow-up it created — all-or-nothing.
- These run as privileged database functions so a half-finished save can never strand a follow-up.

**d) No new tables for metrics.** Counters stay derived from `activities` at read time.

---

## 4. RLS rules

Existing policies already enforce owner-only access on all four tables (every policy checks `owner_id = auth.uid()`; no anonymous grants). Integration adds:

| Table | Rule to add | Why |
|---|---|---|
| `profiles` (if created) | A user reads and updates **only their own** row; insert happens via the signup trigger, not the client | Prevents reps editing each other's identities |
| `leads`, `follow_ups`, `activities`, `favorites` | **No changes needed** — owner policies are already correct | Verified by the database linter and catalog checks |
| Demo rows | Stay under the isolated demo owner ID; no policy may ever grant cross-owner reads | Ordinary signed-in users must never see demo data |

**Hard rules that carry through every build prompt:**
- Never check roles or ownership in browser code alone — the database enforces it.
- Every new table gets explicit grants plus RLS in the same migration; RLS without grants returns permission errors.
- Server-side reads use the signed-in user's own session, so policies apply. The admin client is never used for ordinary reads.

---

## 5. Three sequential build prompts

Each prompt is self-contained and must finish — verified — before the next starts.

### Prompt 1 — Schema & data foundation
> "Add a `profiles` table (display name, time zone) with a signup trigger, owner-only RLS, and explicit grants. Add two database functions: `log_activity` (insert activity + complete follow-up + create next follow-up, atomically) and `undo_activity` (void activity + reopen follow-up + remove the created next follow-up, atomically). Add tests for both functions' all-or-nothing behavior. Do not touch the Today screen. Decide with me first: default time zone, default currency, and whether the seven demo rows get reassigned to my account."

**Done when:** migration applied, linter clean, function tests pass, screen visually unchanged.

### Prompt 2 — Auth + RLS wiring
> "Add sign-in (Google and email) with a public auth page and a protected Today route. On sign-in, load the rep's open follow-ups, favorites, and today's completed activities from the database instead of the demo array. Wire logging, undo, and favorite toggles through the atomic functions from Prompt 1. Keep the demo array as a signed-out preview only. Verify: a second test account sees none of the first account's data."

**Done when:** two real accounts confirmed isolated; refresh preserves work; Undo restores state after refresh; 12 existing tests still pass plus new auth tests.

### Prompt 3 — Edge cases & hardening
> "Handle the edge-case list in section 6: empty states per filter, past-date rejection on Pick date, failed-save retry with the follow-up left open, duplicate-tap protection, daily rollover of 'today', note length limits, and the greeting/name fallback when a profile is missing. Run the stress tests in section 7 and fix what they surface."

**Done when:** every edge case in section 6 has a verified behavior; stress tests in section 7 pass.

---

## 6. Edge cases to handle

**Data & time**
1. **Daily rollover** — a rep leaves the tab open past midnight; "today" must re-derive, not stay stale.
2. **Overdue boundary** — a follow-up due yesterday at 23:00 in the rep's zone shows overdue; one due today at 00:01 does not.
3. **Pick date in the past** — currently allowed; must be rejected or confirmed.
4. **Tomorrow/Next week across month and year boundaries** — resolve to real dates, not labels.
5. **Time zone travel** — rep crosses zones; due times stay anchored to the agreed zone.

**Writes & failures**
6. **Double-tap on an action icon** — must not create two activities or complete the follow-up twice.
7. **Save fails mid-operation** — the follow-up stays open and the rep sees a retry, never a silent loss.
8. **Undo after refresh** — works only if Undo is a real database operation (void marker + reversal), not in-memory state.
9. **Undo window expiry** — define how long Undo is offered; after that, corrections need a different path.
10. **Favorite toggled while filtered to Favorites** — un-hearting removes the card mid-view; confirm this is intended.

**Display & identity**
11. **Empty states** — "All clear" must not appear when a filter simply matches nothing; show "No deals in this stage" instead.
12. **Missing profile** — new user before the profile trigger runs: fall back to email prefix, never a blank greeting.
13. **Very long notes / company names** — cards truncate; Done today already truncates notes with no detail view (accept or add one).
14. **Zero open follow-ups on sign-in** — first-run experience: empty state plus how follow-ups get created (out of scope today — flag it).

**Access**
15. **Signed-out visitor hits `/`** — sees the demo preview or a sign-in prompt, never an error.
16. **Session expires mid-session** — next save fails with a clear sign-in prompt, not a generic error.
17. **Demo data isolation** — no query path may ever return the isolated demo rows to a real account.

---

## 7. Stress tests to run

**Data volume**
1. **500 open follow-ups for one rep** — Today grid renders and filters/sorts without lag; counts and progress bar stay correct.
2. **1,000 activities in one day** — Done today and avg metrics compute correctly; page stays usable.
3. **All seven demo leads completed, then Undo all seven** — final state matches the starting state exactly.

**Concurrency & repetition**
4. **Rapid-fire tapping** — 20 fast taps across action icons and next-step chips produce exactly one activity per intended completion.
5. **Two tabs, same account** — completing a follow-up in tab A, then acting in stale tab B, fails safely (no duplicate completion, clear refresh prompt).
6. **Toggle favorite 50 times rapidly** — exactly one row exists (or none) at the end; no duplicate-key errors surface to the user.

**Time & boundary**
7. **Clock at 23:59 → 00:01** — open list re-derives across midnight without a reload.
8. **Due dates at zone boundaries** — follow-ups due at 00:00 and 23:59 in the agreed zone land on the correct day.
9. **Snooze storm** — snooze every open follow-up; every one gets a real next `due_at`; none disappear permanently.

**Security**
10. **Cross-account probe** — account B attempts to read/complete/favorite account A's follow-ups by guessing IDs; every attempt denied.
11. **Demo-owner probe** — a real account queries for the demo owner ID directly; zero rows returned.
12. **Signed-out API calls** — every data endpoint called without a session returns unauthorized, not data.

**Regression**
13. **The 12 existing domain tests plus routing test** — pass after every prompt.
14. **Visual check** — the Today screen looks identical to the current design after integration; only the data source changes.

---

## 8. What this plan deliberately does not include

- Pipeline, Accounts, Deals, Reports, or any other nav screen — still decorative, still out of scope.
- Creating or editing leads/follow-ups from the UI — the experiment assumes follow-ups already exist; how they get created is a separate decision.
- CRM integrations, reporting, notifications, or manager views.
- Any change to the experiment itself: baselines (8 fields, 11 min, 18% weekly active), both rep quotes, and the kill switch stay intact.

**Kill switch reminder:** if reps still prefer their spreadsheet after the redesign, the tool isn't the blocker — pivot. Integration makes that measurement possible; it doesn't pre-judge the result.
