# Rep Home — Experiment Brief (v2)

**Scenario 02 · The Internal Tool Nobody Uses**
Prepared for: Peter Dycus · 13 Oct 2026 · Updated with filtering and sorting

---

## 1. Riskiest assumption

The CRM doesn't fit how reps actually work, so they never adopt it.

## 2. Hypothesis

A stripped-back home page showing **only today's follow-ups** with **one-tap logging** will cause reps to use the CRM daily instead of a shadow spreadsheet.

## 3. Success measures

| Measure | Baseline | Target |
| --- | --- | --- |
| Weekly active reps | 18% of seats | Rising week over week |
| Logged activities per rep | — | Rising |
| Time to update one deal | 11 min | Under 2 min |
| Taps per deal update | 8 required fields | 2 or fewer |

## 4. Kill switch

If reps still prefer their spreadsheet after the redesign, the tool isn't the blocker — pivot.

## 5. Baseline evidence

- **8** required fields per deal update
- **11 min** average time to update a deal (2-min target)
- **18%** weekly active seats

### What reps said

> "It's faster to keep my deals in a spreadsheet than to fight the CRM's eight required fields."
> — Account exec, 2 logins / month

> "I never know where the thing I need lives. Every screen looks like a settings page."
> — Sales rep, onboarded but inactive

---

## 6. What the prototype does

A single page at `/`. No login, no backend — sample deals are held in the page so the test is about value and usability, not infrastructure.

1. **Header strip** — greeting, today's date, and a progress counter ("3 of 7 follow-ups done").
2. **Headline** — "You have 7 follow-ups today." The number counts down as deals are logged and reads "no follow-ups today" when the list is clear.
3. **Today's follow-ups** — one card per deal: company, stage, deal value, contact, and why it's due ("Promised pricing on Tuesday"). Overdue deals are pinned to the top and flagged in red.
4. **One-tap logging** — four large icon buttons per card: phone (called), envelope (emailed), people (met), clock (snooze). No words on the buttons; hovering names them.
5. **Optional quick note** — after a tap, one text line slides open. It can be skipped. This replaces the eight required fields.
6. **Next step picker** — three chips (Tomorrow, Next week, Pick date) set the next follow-up. Tomorrow is the pre-selected default.
7. **Filtering** — chips above the list narrow it to one stage (Proposal, Negotiation, Discovery, Demo) or to **Favorites**. Tapping the heart on any card saves it to Favorites.
8. **Sorting** — the SORT button opens a menu to order deals by company name, A to Z or Z to A. Overdue deals stay pinned at the top in every view.
9. **Done today** — logged deals move to a separate section with an undo option in the toast.
10. **Built-in measurement** — live "avg time" and "avg taps" counters at the top of the page, coloured red when a deal update goes over 120 seconds or 2 taps.
11. **Evidence panel** — a collapsible section at the bottom holding the hypothesis, baselines, rep quotes, success measures and kill switch, so stakeholders reviewing the prototype see the reasoning without a separate deck.



## 7. How to run the test

1. Put the prototype in front of 5–8 reps for one week.
2. Watch the two on-page counters: does one update take under 2 minutes and 2 taps or fewer?
3. Track weekly active reps and logged activities against the 18% baseline.
4. Note whether reps reach for the stage filters or Favorites — if they only ever use "All", that's a signal the daily list alone is doing the work.
5. Ask the spreadsheet question directly at the end of the week — that answer decides the kill switch.

## 8. Design notes

Visual direction follows the attached product-listing reference: white background, sharp corners, black-and-white high contrast, condensed uppercase display type (Barlow Condensed) over Barlow body text, one accent used only for actions and overdue flags. Cards read like product tiles so a rep scans the list instead of reading a form.

## 9. Open items

- The seven deals, contact names and the rep name "Jordan" are invented placeholders. Send real examples and they'll replace them.
- "Help", "Manager view" and "Settings" in the top bar are not wired to anything yet.
