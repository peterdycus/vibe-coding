# Rep Home — Engineering Handoff

**Prepared for:** Peter Dycus  
**Version:** 1.0 · **Date:** 15 October 2026  
**Status:** Functional, in-memory usability prototype; not a production CRM  
**Scope reference:** Rep Home Living PRD v1.0, plus the subsequent feature refactor

## 1. The 60-second read

Rep Home tests whether a simpler daily follow-up workflow makes sales reps use their CRM instead of a spreadsheet. There is one content screen, **Today**, at `/`: seven invented deals, overdue-first ordering, stage and Favorites filters, company-name sorting, icon-only activity logging, optional notes, next-step choices, Done today, and temporary Undo. The React/TanStack Start frontend is separated into display, hooks, data, and pure domain logic under `src/features/today/`; the route only wires the screen and metadata. There is no application backend, login, database, CRM connection, scheduling, or adoption analytics: refresh discards all work. Calls/emails/meetings take two quick-path taps to record; snooze takes one; none performs the actual communication. The structure and tested domain behavior are useful foundations, but the timer, synthetic tap counts, filtered empty state, and decorative navigation are prototype shortcuts. First make the workflow honest and test it with reps; add secure persistence and real due dates only for an approved pilot. If reps still prefer spreadsheets, apply the PRD’s pivot rule rather than expanding the CRM.

## 2. Architecture in plain language

### Frontend: what the rep sees

The app uses React 19, TypeScript, TanStack Start/Router, Tailwind CSS v4, shared UI controls, Lucide icons, and Sonner confirmation messages. It retains the white, dark-action, condensed-heading design from the supplied listing-page reference. Fonts are Barlow and Barlow Condensed, loaded from Google Fonts.

Only Today is a working content screen. Pipeline, Accounts, Deals, Reports, Help, Manager view, Settings, Search, the breadcrumb, and header icons are visual placeholders—not implemented destinations. Do not create those screens as part of finishing this workflow without new scope approval.

### Backend/data: where information lives

There is **no application data backend** and no Lovable Cloud connection. TanStack Start has server-rendering infrastructure, but that is not a CRM service. The configured build target defaults to Cloudflare; production deployment was not verified for this handoff.

Seven demo records are imported into React state. Favorites, completed activities, notes, filter choice, and sorting exist only for the mounted screen. Nothing is saved to a database, browser storage, or an external CRM. Reloading resets the demo.

The `Deal` model contains company/contact details, value, stage, reason, optional overdue/favorite flags, and an optional `done` object holding action, note, next-step text, seconds, and taps. It lacks rep ownership, canonical due dates, activity IDs, timestamps for an activity history, and an audit trail. “Jordan” and all sample business records are placeholders.

### File map after the refactor

```text
src/routes/index.tsx                 / route, TodayScreen, page metadata
src/routes/__root.tsx                Shared shell, fonts, toast setup
src/styles.css                      Shared visual tokens and styling
src/features/today/
  TodayScreen.tsx                    Today page and presentation controls
  components/
    TodayFollowUpCard.tsx            Card, heart, actions, note/date inputs
    TodayDoneToday.tsx               Completed-item display
    TodayLoggingMetric.tsx           Session metric display
    TodayExperimentPanel.tsx         Collapsible experiment evidence
  hooks/
    use-today.ts                    Session data, selection, completion, Undo
    use-follow-up-logging.ts        Per-card action/note/date state and timing
  data/
    demo-deals.ts                   Invented records
    options.ts                     Stages, actions, next-step choices
    experiment.ts                  Hypothesis, baselines, quotes, kill switch
  types.ts                         Domain types
  selectors.ts                     Pure filtering, sorting, state transitions
  format.ts                        Deal-value formatting
  today.test.ts                    Domain tests
```

**Reading order:** `AGENTS.md` → `TodayScreen.tsx` → the two hooks → `selectors.ts` → types/data → cards/tests. Use the shared Button’s `unstyled` variant for the existing bespoke controls.

**PRD correction:** The PRD v1.0 file-location list predates the refactor: the model and screen logic no longer live in `src/routes/index.tsx`. This handoff describes their current locations; it does not revise the original PRD snapshot.

### Key flows

1. **Open Today:** Load demo records; show unfinished items, with seeded overdue flags first. The header date is formatted in the browser’s local time zone after mounting; it does not determine which records are due.
2. **Filter and sort:** Stage chips select unfinished matches; Favorites selects unfinished hearted items. SORT chooses A–Z or Z–A by company **within overdue/non-overdue groups**. Choosing the active direction again clears alphabetical sorting.
3. **Record an activity:** Call, email, or meeting opens an optional note and next-step choices. Tomorrow/Next week commits; Pick date reveals a native date input and choosing a date commits. Cancel returns to the action icons. These actions record work; they do not place a call, deliver email, or book a meeting.
4. **Snooze:** Immediately marks the item done with next = Tomorrow. No scheduling or future reappearance follows.
5. **Complete and recover:** Store one completion object in memory, remove the item from the open list, show it in Done today, and offer Undo in a temporary toast. Undo clears the completion; there is no durable reversal/history.
6. **Progress and metrics:** Progress counts all completions, including snoozes. Done today ignores the active filter. Time/tap averages exclude snoozes. The headline and person badge count the filtered open list, not necessarily all remaining work.

## 3. What is solid versus duct tape

| Area | Solid foundation | Prototype shortcut / remaining work |
| --- | --- | --- |
| Boundaries | One explicit Today feature; data, domain logic, hooks, and display separated | No persistence/service boundary implemented yet |
| Domain behavior | Pure filtering, overdue-first sorting, favorite toggles, completion/Undo, and metric selection have passing tests | No measured coverage percentage; tests do not establish complete correctness |
| Core interaction | Favorites, note + Tomorrow, completion, Undo, and refresh exercised in the live demo | No committed end-to-end flow tests; no save/loading/error states for a real service |
| Activity data | Optional note and short next-step path | One mutable completion state per deal, not durable activity history |
| Today and next steps | Clear sample worklist | Seeded overdue booleans; Tomorrow/Next week are labels, not computed dates; no daily rollover |
| Efficiency displays | Derived averages are separated from JSX | Timer starts at card mount; taps are assigned by path, not observed interactions |
| UI controls | Shared Button, accessible names for icon actions | Decorative navigation; custom sort popup needs keyboard/focus/dismissal review |
| Empty state | A visible empty state exists | A filter with no matches incorrectly says every follow-up is logged |

Additional shortcuts: Cancel does not explicitly clear the note/date state, so reopening a still-mounted card can retain it; filtering a card out unmounts and loses that draft. Pick date permits past dates. Long notes are truncated in Done today with no detail view. Identity and greeting are hard-coded.

## 4. Risks, assumptions, and decisions

### Product assumptions—not validated findings

- Workflow mismatch is the primary adoption blocker. The two supplied rep quotes indicate friction, not causation.
- A Today-only worklist covers enough daily work to displace spreadsheets.
- Icon-only logging is discoverable, especially on touch devices.
- Favorites and sorting improve workflow; feature use itself is not the success metric.

**Keep the experiment intact:** Baseline is 8 required fields, 11 minutes per deal update, and 18% weekly active adoption; target update time is under 2 minutes. Success means weekly active reps and logged activities rise. The eight fields are not an eight-tap baseline. Retain both rep quotes and the evidence panel. **Kill switch: if reps still prefer their spreadsheet after the redesign, the tool is not the blocker—pivot.**

### Operational risks before using real data

- **Data loss/security:** Refresh erases work; there is no identity, assignment enforcement, or access control. Do not treat this public prototype as a secure team CRM.
- **Missed follow-ups:** Saved next-step labels never resurface. Define canonical due dates, Today’s time zone, date validation, and daily rollover before operational use.
- **Misleading metrics:** Waiting before an action inflates timing; filtering can reset it. Quick paths report 2 taps, date paths 3, regardless of keystrokes or cancellations. These counters cannot measure weekly adoption.
- **Misleading completion:** Filter-sensitive headlines and the “All clear” message can hide unfinished work. Distinguish no matches from all work complete.
- **Future save reliability:** A persistent pilot needs atomic activity/next-step saves, duplicate prevention, failed-save handling, durable corrections, and read-back before reporting success.

### Open decisions and recommended sequence

1. Clarify empty/count states and dead affordances; fix task timing and test keyboard/phone workflows without adding new screens.
2. Run representative usability tasks and interview reps about spreadsheet preference.
3. For an approved persistent pilot, agree owners, rep cohort, record access, time zone, CRM source of truth, and integration permissions; then use Lovable Cloud for identity and durable data.
4. Agree the active-rep definition and eligible-seat denominator behind 18%, pilot duration, and improvement threshold before collecting/comparing adoption. Saved calls/emails/meetings should be separate from snoozes and reversals; omit note contents from analytics.
5. Review the evidence and apply the kill switch before expanding scope.

These are recommendations, not implemented features or delivery commitments.

## 5. How to run it

### Local setup

Use the project source/repository URL supplied by its owner. The checked environment uses **Node 22.22.0** and **Bun 1.3.3**; these are observed versions, not a declared minimum. `bun.lock` is committed, so Bun is the reproducible first choice. No CRM keys or application data credentials are needed for this demo.

```sh
# From the project root after obtaining the source:
bun install --frozen-lockfile
bun run dev

# Open the local URL printed by Vite.
```

In the managed Lovable sandbox, the server already runs at `http://localhost:8080`; do not start a second server. Outside that sandbox, use the address Vite prints rather than assuming port 8080.

### Tests and checks

```sh
bun run test                                  # Entire current Vitest suite
bunx vitest run src/features/today/today.test.ts
bun run test:watch                            # Watch tests during development
bun run lint                                 # ESLint

# For a separate local production-build check:
bun run build
bun run preview                              # Preview generated output locally
```

`bun run format` rewrites formatting throughout the project; use it intentionally. Keep the existing TanStack bootstrap and Vite wrapper: it already supplies the framework/style plugins, aliases, and sandbox settings. Do not add duplicate plugins.

### Manual smoke test

1. Open `/`; confirm seven follow-ups and two overdue items first.
2. Heart one card → Favorites; confirm one match. Return to All.
3. Choose SORT → A to Z, then Z to A; confirm overdue items stay first and alphabetical order changes inside the groups.
4. Tap Call → enter an optional note → Tomorrow; confirm six open items and the note/action in Done today.
5. Tap Undo while the toast exists; confirm seven open items again.
6. Try email, meeting, snooze, cancel, date choice, and an empty filter; note the limitations above rather than expecting production scheduling.
7. Refresh; expect the original demo, not retained work.

### Verification record for this handoff

- **Automated, 15 October 2026:** `bun run test` passed **12 tests across 2 files**: 11 Today domain cases and 1 routing test. No coverage report was generated; hooks/components are not covered by committed full-flow tests.
- **Live local preview, same date:** Favorites showed 1; recording a call with a note + Tomorrow reduced 7 to 6 and displayed the note in Done today; Undo restored 7; refresh restored the demo. Start and saved-state screenshots were visually inspected; no page runtime errors were captured in this run.
- **Not freshly verified:** Production deployment, build/lint commands, phone layouts, accessibility audit, remaining UI action/date paths, or any authenticated/persistent flow (none exists).

**Bottom line:** Ready for a bounded demo/usability handoff; not ready for a live CRM adoption pilot without the explicitly missing infrastructure and measurement work.
