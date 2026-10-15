# Rep Home — Living Product Requirements Document

**Scenario 02 · The Internal Tool Nobody Uses**  
**Prepared for:** Peter Dycus  
**Version:** 1.0 · **Last updated:** 15 October 2026  
**Status:** Functional usability prototype; adoption hypothesis not yet validated  
**Product owner / engineering owner:** To be assigned

This document follows the eight blocks in the supplied screenshot. It describes the current prototype, distinguishes evidence from assumptions, and labels future work as proposed—not implemented or approved.

**Evidence:** Original scenario and rep quotes; current source; existing experiment brief; live-preview checks on 15 October 2026. No production usage or customer research results were supplied.

## 1. Product Overview

### Purpose and audience

Rep Home is a focused daily worklist for sales representatives who currently avoid the CRM or maintain a shadow spreadsheet. It prioritizes follow-ups and reduces the effort of recording an interaction. Sales managers and product stakeholders are secondary users of the experiment evidence, not supported application roles.

### Current experience

The first screen is the Today page at `/`, with seven sample deals. Each card shows company, contact, deal value, stage, follow-up reason, a favorite heart, and four icon-only actions: call, email, meeting, and snooze. Overdue deals stay first.

Reps can filter by stage or Favorites, sort by company, add an optional note, and choose a next follow-up. Completed items move to Done today. A collapsible evidence panel preserves the hypothesis, baseline, quotes, success measures, and kill switch.

**Important qualification:** “One-tap logging” is the hypothesis language. Calls, emails, and meetings currently require an action tap plus a next-step selection to commit; snooze commits in one tap. Icons record an interaction—they do not place calls, send emails, or arrange meetings.

### Presentation and delivery status

The design follows the supplied product-listing reference: white surfaces, dark actions, condensed uppercase headings, sharp-edged cards, Barlow Condensed headings, and Barlow body text. Red identifies overdue items.

- Preview: https://id-preview--d6fe4ab2-4b75-45c1-b397-aa22b4abeff0.lovable.app
- Published address: https://crm-optimizations.lovable.app
- Verification in this document refers to the local preview, not a fresh audit of the published address.
- All contacts, companies, values, and “Jordan” are invented demo content, not customer records.

## 2. Problem & Hypothesis

### Problem

The riskiest assumption is that the CRM does not fit how reps actually work, causing low adoption. Too many required fields and unclear navigation encourage spreadsheet workarounds.

### Supplied baseline

| Evidence | Baseline | Qualification |
| --- | --- | --- |
| Required fields per deal update | 8 | Workflow burden; not a tap-count baseline |
| Average time to update a deal | 11 minutes | Supplied baseline; collection method not provided |
| Weekly active adoption | 18% of seats | Supplied baseline; denominator and active definition need confirmation |

> “It's faster to keep my deals in a spreadsheet than to fight the CRM's eight required fields.”  
> — Account exec, 2 logins / month

> “I never know where the thing I need lives. Every screen looks like a settings page.”  
> — Sales rep, onboarded but inactive

### Hypothesis

A stripped-back home showing only today's follow-ups with one-tap logging will cause reps to use the CRM daily instead of a shadow spreadsheet.

### Intervention and decision

Test a focused Today list, optional rather than required notes, short logging flow, overdue prioritization, and lightweight filtering and sorting. Success requires weekly active reps and logged activities to rise—not merely a favorable reaction to the design.

**Kill switch:** If reps still prefer their spreadsheet after the redesign, the tool isn't the blocker—pivot.

The prototype makes this hypothesis testable for usability. It does not establish that adoption has improved.

## 3. User Flow & Screen Map

### Screen and state map

```text
/ · Today — the only content page
|
+-- Open follow-ups
|   +-- All / Proposal / Negotiation / Discovery / Demo / Favorites
|   +-- SORT -> A to Z / Z to A
|   +-- Card heart -> toggle favorite
|   +-- Call / Email / Meeting icon
|   |   +-- Optional note
|   |   +-- Tomorrow / Next week / Pick date -> commit
|   |   +-- Cancel -> return to action icons
|   +-- Snooze icon -> commit immediately, next = Tomorrow
|
+-- Done today -> recorded action, next step, optional note
|   +-- Temporary confirmation toast -> Undo -> restore open card
|
+-- Empty filtered list / all completed -> empty state
|
+-- Experiment panel -> collapsed / expanded evidence
```

### Interaction requirements and current behavior

| Interaction | Current behavior / acceptance criterion |
| --- | --- |
| Stage filter | Shows unfinished deals matching one selected stage; All restores all unfinished deals. |
| Favorites | Shows only unfinished hearted deals; removing a heart excludes that deal from Favorites. |
| Alphabetical sort | Sorts company names A–Z or Z–A **within** overdue and non-overdue groups; overdue always comes first. Selecting the active direction again clears alphabetical sorting. |
| Call/email/meeting | Opens optional note and next-step choices; does not mark done until a next step is selected. |
| Tomorrow / Next week | Commits the action and stores that text label; Tomorrow is visually emphasized, not automatically committed. |
| Pick date | Reveals a native date input; selecting a date commits and stores the chosen date string. No past-date restriction is implemented. |
| Snooze | Marks the item done immediately with next step Tomorrow; no note-entry step. |
| Confirmation / Undo | Toast names the action and next step; Undo restores the item while the toast is available. |
| Progress | Counts all completed items, including snoozes, against the seven seeded deals. |
| Headline / person badge | Counts the currently filtered unfinished list, not always all follow-ups. |
| Done today | Displays all completed items regardless of the active stage/Favorites filter. |
| Refresh | Restores sample data and clears completion, favorites, notes, and filter/sort state. |

### Known screen gaps

Pipeline, Accounts, Deals, Reports, Help, Manager view, Settings, Search, the header heart, and the bag/user icons are display-only affordances; they do not open screens or perform actions. The breadcrumb is also not navigation.

When a filter has no matches, the current “All clear / Every follow-up for today is logged” message can be misleading: other stages may still contain unfinished deals. Correct this before a broader pilot.

## 4. Success Metrics

### North Star and leading indicators

**Proposed North Star:** Weekly active reps performing meaningful CRM work. Define this as a unique eligible rep with at least one saved call, email, or meeting during a seven-day reporting window. This definition is proposed and must be reconciled with the supplied 18% baseline before comparison.

| Metric | Baseline | Target / decision use | Measurement status |
| --- | --- | --- | --- |
| Weekly active reps / eligible rep seats | 18% weekly active seats | Rise versus a comparable baseline | Not collected by the prototype |
| Logged activities per rep per week | Unknown | Rise; report totals and per-rep distribution | No durable activity history |
| Time to update one deal | 11 minutes | Under 2 minutes | Session display exists, but timing is not task-valid |
| Interaction taps to commit a standard update | Unknown | 2 or fewer for the quick next-step path | Simplified internal counter only |
| Daily meaningful use | Unknown | Supporting evidence for the daily-use hypothesis | Not collected |
| Spreadsheet preference / continued use | Qualitative friction only | Apply kill switch if reps still prefer spreadsheets | Requires interviews and observation |

The eight required fields must not be represented as eight taps. Filter use and favorites use are exploratory measures, not success goals by themselves.

### Current counter limitations

- Average time starts at card mount, not at the first logging action. Waiting inflates it; filtering cards away and back resets it.
- Average taps counts a predefined logging path, not every real click, keystroke, cancellation, or date-picker interaction.
- Quick next-step completion records 2 taps; Pick date completion records 3; snoozes are excluded from both averages.
- The display flags averages of **120 seconds or more** as failing; exactly 2 average taps passes.
- Counters disappear on refresh and cannot measure weekly adoption.

### Proposed measurement plan

1. Conduct a usability round with 5–8 reps using representative, non-sensitive tasks; observe actual completion time, errors, and spreadsheet preference.
2. Before claiming adoption results, run a persistent pilot with real assigned follow-ups and a comparable baseline window. A one-week pilot is exploratory, not proof of a durable trend.
3. Record proposed events: activity-started, activity-saved, activity-undone, follow-up-snoozed, filter-changed, and favorite-toggled. Tie saved activities to a rep and timestamp; avoid note contents in analytics.
4. Report saved calls/emails/meetings separately from snoozes and reversals; do not count a page view or snooze as meaningful activity.
5. Agree cohort, duration, minimum improvement, and review owner before launch. These numerical adoption thresholds are not yet supplied.

## 5. Technical Reality

### Truth versus mock

| Area | Reality today | Handoff implication |
| --- | --- | --- |
| Frontend | TanStack Start / React; Today page uses local React state | Functional interactive prototype |
| Data | Seven hard-coded deals; no database or app data API | Not a live CRM feed |
| Activity logging | Updates a deal's `done` object in memory | No durable activity record or write-back |
| Next follow-up | Stores Tomorrow, Next week, or selected date as a value | No scheduling engine or future-day reappearance |
| Overdue / today | Overdue is a seeded boolean; all sample deals start on Today | No due-date calculation or automatic daily rollover |
| Favorites / filters / sort | Working in-session state | Not shared, retained, or encoded in a URL |
| Login and permissions | Not implemented; no Lovable Cloud connection | No rep identity, assignment, or access enforcement |
| Notes | Optional in-memory text | Lost on refresh; no audit trail |
| Metrics | Session-only derived counters | Not production analytics |
| Notifications | Sonner confirmation toast with Undo | No reminders, emails, or push notifications |
| Other navigation / search | Decorative | No additional product screens |

### Current model and code locations

`Deal` includes id, contact, company, value, stage, reason, optional overdue/favorite flags, and optional completion data: action, note, next, seconds, and taps. No canonical next-follow-up timestamp, assignee, activity identifier, or change history exists.

- `src/routes/index.tsx`: demo model, Today list, filtering, sorting, logging, counters, and experiment panel.
- `src/routes/__root.tsx`: shared page shell, fonts, and toast setup.
- `src/styles.css`: shared visual tokens.
- TanStack routing/query scaffolding exists, but the Today page does not use it to retrieve CRM data.

### Verification record — 15 October 2026

**Observed in the live preview:** Initial count was 7; hearting a deal then choosing Favorites showed one card; Z–A sorted within overdue-first groups; recording a call with a note and Tomorrow reduced the headline to 6 and showed the note in Done today; Undo restored 7; refresh returned the seeded state. The resulting screen was visually inspected, and no browser runtime errors were captured in that run. Latest available build diagnostic reported OK.

**Source-confirmed, not independently exercised in that run:** Other stage filters, snooze, meeting/email paths, date selection, empty-state behavior, and timing limitations. Mobile, accessibility, production deployment, authentication, and automated interaction-test coverage were not verified for this PRD.

## 6. Assumptions & Risks

Confidence labels are qualitative judgments, not research scores.

| Assumption / risk | Confidence or severity | Evidence / next action |
| --- | --- | --- |
| Workflow mismatch is the main adoption blocker | Low confidence | Quotes suggest friction, not causation; compare rep behavior and spreadsheet preference. |
| A Today-only view is sufficient for daily rep work | Low–medium confidence | Plausible workflow; test missed needs and follow-ups outside Today. |
| Icon-only logging remains discoverable | Medium risk | Labels are available to assistive technology and through titles; validate recognition on touch devices. |
| Company sorting and Favorites help | Low confidence | Requested features, not validated impact; observe actual use without making it a success requirement. |
| Demo counters represent task efficiency | High risk | Mount timing and synthetic tap counts can mislead; use observed task timing until instrumentation is corrected. |
| Prototype can support a week-long live pilot unchanged | High risk | Refresh loses all work; persistence and real due dates are prerequisites. |
| “All clear” means every item is handled | High risk | Empty filters can hide remaining deals; distinguish no matches from complete work. |
| Next steps will resurface when due | High risk | Currently only stored labels; implement scheduling before operational use. |
| Mistakes are recoverable | Medium risk | Undo is temporary; production needs durable correction/history. |
| Rep/company data is safe for team rollout | High risk if real data is introduced | No identity/access rules; do not treat the public prototype as a secure CRM. |

**Kill-switch evaluation:** Ask reps directly whether they still prefer their spreadsheet after the redesigned workflow is available. If they do, apply the stated pivot rule rather than treating added features as an automatic remedy.

## 7. In vs. Out Scope

### In scope — implemented prototype

- Single Today screen with seven invented follow-ups and a placeholder greeting.
- Deal cards, overdue priority, icon-only activity choices, optional note, and next-step selection.
- Immediate snooze, Done today, transient Undo, and session progress.
- All / Proposal / Negotiation / Discovery / Demo / Favorites filters.
- Per-card favorite hearts and company A–Z / Z–A sorting under overdue priority.
- Session average-time/tap displays and collapsible experiment evidence.
- Product-listing-inspired presentation; SORT has no notification number.

### Out of scope — not implemented

- Persistent records, real CRM connection/write-back, authentication, or team permissions.
- Actual calling, email delivery, meeting booking, reminders, and calendar integration.
- Live due-date selection of today's work or next-step rescheduling.
- Pipeline, Accounts, Deals, Reports, manager screens, search, and settings.
- Production analytics, history editing, durable undo, imports/exports, and multi-user coordination.

### Proposed roadmap — decision-gated

| Phase | Outcome | Gate |
| --- | --- | --- |
| 1 · Usability readiness | Clarify false affordances and empty states; fix timing; add targeted flow tests | Reps understand and complete the core task |
| 2 · Persistent pilot | Add identity, access rules, real due dates, durable activity records, and measurement | Representative cohort and measurement definitions agreed |
| 3 · Adoption decision | Compare meaningful use and activities with baseline; apply spreadsheet kill switch | Evidence supports continued investment or pivot |
| 4 · Expansion | Consider additional screens/integrations only if observed needs justify them | Adoption evidence and explicit scope approval |

## 8. Engineering Recommendation

### Recommended build order

Preserve the narrow Today workflow. Do not build the decorative navigation into a full CRM before testing the central assumption.

1. **Make the prototype honest:** Fix filtered empty states; clarify filter-sensitive counts; remove or clearly disable dead affordances; correct task timing; retain accessible names for icon actions.
2. **Protect the core flow:** Add tests for stage/Favorites filtering, overdue-first alphabetical sorting, all action paths, notes, date selection, cancel, Undo, empty states, and refresh behavior. Check phone layouts and keyboard use.
3. **Enable a real pilot:** Use Lovable Cloud for identity and durable storage; introduce assigned deals, canonical due dates/time zones, separate activity records, and access rules. Persist an activity and its next follow-up together so partial saves cannot lose work.
4. **Make saves safe:** Handle loading, failed writes, duplicate submissions, corrections, and read-back. Undo must reverse a stored action safely; never report success before saving succeeds.
5. **Measure then decide:** Instrument meaningful actions and comparable weekly adoption. Conduct the spreadsheet-preference review before broadening scope.

These are recommendations only; this document does not authorize implementation or enable Cloud.

### Indicative effort — planning assumptions, not commitments

| Work package | Rough effort | Dependencies / uncertainty |
| --- | --- | --- |
| Prototype clarity, timer fixes, focused interaction tests | 1–3 engineer-days | No redesign or external service integration |
| Persistent pilot with identity, activity storage, due-date logic, and access controls | 5–10 engineer-days | Agreed data model and team access rules; excludes external CRM integration |
| Instrumentation and pilot reporting | 2–4 engineer-days | Stable event definitions and persisted identity |
| CRM import/write-back | Not estimated | Requires named CRM, permissions, API limits, and source-of-truth decision |

Ranges are preliminary; do not sum them into a delivery promise. Production security/accessibility review and integration discovery may add effort.

### Open decisions and dependencies

| Decision | Owner | Needed before |
| --- | --- | --- |
| Who owns product decisions and engineering delivery? | Peter Dycus / sponsor | Next revision |
| Which reps and real follow-ups form the pilot cohort? | Product + sales lead | Persistent pilot |
| What counts as an active seat in the original 18% baseline? | Product / analytics | Adoption comparison |
| What improvement and test duration warrant continued investment? | Product + sales lead | Pilot launch |
| Is a next step mandatory, and how should snoozes count? | Product + reps | Final logging behavior |
| Which CRM is the source of truth; what integration access exists? | Engineering + CRM owner | Integration estimate |
| Which time zone defines Today, and may reps choose past dates? | Product + engineering | Due-date scheduling |
| Who can view or edit notes and deal records? | Sales lead + engineering | Real-data rollout |

### Living-document maintenance

- Update this PRD whenever behavior, scope, evidence, risks, or decisions change—not only at release.
- Keep implemented, verified, and proposed items distinct. Record the date and method behind new evidence.
- Preserve baseline sources and both rep quotes; replace assumptions with findings when research arrives.
- Save the next revision as a new versioned Markdown file, preserving this snapshot.
- At each review, revisit all eight blocks and the kill switch; record the decision, owner, and next review date here.

**Revision record:** v1.0 — 15 October 2026 — Initial eight-block PRD; audited current behavior, verified the central demo flow, and separated usability capability from missing adoption infrastructure.  
**Next review:** After the first rep usability round or an approved scope change; date and owner to be assigned.
