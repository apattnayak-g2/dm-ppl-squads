# PRD — Buyer Summary in the Opportunity Queue

**Status:** Draft for review · Thursday session with Chase & Ricardo
**Prototype:** https://apattnayak-g2.github.io/dm-ppl-squads/research/buyer-summary-opp-queue/
**Owner:** Arosha Pattnayak

## Problem

When a known buyer recalls or re-engages, the rep opens the expanded Opportunity Queue row and gets two things that don't help them prepare for the call: a Conversion Contact block that duplicates the Opportunity Contact almost field-for-field, and a Potential Duplicates list that only tells them *which fields matched*, not whether the match actually matters. Neither answers the three questions a rep actually needs before dialing: is this a viable recall, how lucrative is it, and what can I legally send this buyer today versus what's still inside a requalification window. Today that means manually scanning up to 40+ logged interactions — most of them "NML" — to reconstruct history by hand, which is slow and error-prone enough that a real callback can get missed entirely (as in the April follow-up in our example buyer's log).

## Goals

- Replace redundant/low-value panels (duplicate conversion contact, raw duplicate-match list) with information the rep can act on immediately.
- Compute and display product eligibility automatically from send history, so reps never have to manually track 6- and 12-month requalification windows.
- Cut prep time before a recall call to near-zero by putting qualification count, sendable products, blocked products, and next-unblock date in one glance.
- (Phase 2) Compress a noisy interaction log into the handful of entries that actually mattered, plus sentiment, stated pain points, and tech stack.

## Non-goals

- Not changing where "Send" actions live — they stay on the qualification page. This is a read-only summary view.
- Not changing the underlying duplicate-detection/matching logic, only how a confirmed match is surfaced.
- Not a replacement for the full Buyer Profile page — "Open buyer profile" still exists as an escape hatch.

## Users

- **Advocates** (primary) — default to the new Buyer Summary tab.
- **Advisors** (secondary) — default stays on Opportunity Actions; Buyer Summary is one click away.

## Functional requirements

1. **Collapse Conversion Contact to one inline line.** Brand · Source · Medium · Market stay visible by default; the full redundant block (name/company/email/phone, already shown in Opportunity Contact) is hidden behind a "Show conversion contact" toggle.
2. **Potential Duplicates → Linked buyer account card.** Shows avatar, buyer name, a single "100% match" chip (no field-by-field match detail), prior-qualification count, last-qualified date, and a count of products currently blocked by a requal window. Includes a "View buyer summary" jump link.
3. **Segmented control replaces the old toggle.** Opportunity Actions · Interactions (with a count badge) · Buyer Summary, in the existing panel header. Defaults differ by role (Advocate → Buyer Summary, Advisor → Opportunity Actions).
4. **Four headline stats** above any other detail: total qualifications (with in-market count), products sendable today, products blocked, next unblock date.
5. **Color-coded, computed eligibility grid**, one row per product in the buyer's market: green = available or eligible to requalify, red = blocked. Each blocked row shows the computed unblock date and which rule produced it.
   - Rule: first send to a given vendor → 6-month wait.
   - Rule: a 3rd send of the same vendor → 12-month wait measured from the 2nd send.
   - A "Simulate today" control (prototype-only) lets reviewers move the clock and watch rows flip between available/blocked to sanity-check the math.
6. **Qualification history as a dated timeline**, not a flat list: market, rep name/role, qualification kind, products sent (as chips), a freeform note, and a window badge per entry. Entries outside the current market are shown dimmed and can be hidden via a checkbox — they're context, not a requalification constraint.
7. **(Phase 2) AI conversation summary.** Reduces the full interaction log to the handful of entries that mattered, plus a sentiment line, a stated-pain-points box, and a tech-stack/interests box. Sourced from call-log entries and Gong transcripts.
8. **(Phase 2) Gong-driven "Ask about this."** Surfaces questions pulled from recorded calls that the rep might not otherwise think to ask (e.g., a competitor demo mentioned on a prior call, a stated budget sign-off threshold).

## Open questions

- Confirm the 6-month / 12-month requalification thresholds against Chase's rules doc — Ricardo also mentioned an 18-month variant that needs to be reconciled.
- Decide whether Buyer Summary should be the Advocate default, or whether that needs to stay a per-rep preference.
- Decide whether "Send" actions should eventually move into this view, or permanently stay on the qualification page.

## Success metrics (proposed)

- Reduction in average time-to-first-action after opening a recall/re-engaged row.
- Reduction in products sent to a buyer while still inside a requalification window.
- Advocate adoption rate of the Buyer Summary tab vs. Opportunity Actions as their working default.

## Dependencies & risks

- Depends on an existing, trusted buyer-account match (the prototype assumes a 100% match; lower-confidence matches aren't addressed here).
- Phase 2 (items 7–8) depends on Gong integration and a call-log summarization pipeline that doesn't exist yet — this PRD only defines the UX those features would land in.

## Prototype notes

Built against one illustrative example (Lisa Ellis / Meridian Logistics, Contract Management market) reusing real Portal layout pieces: the expanded-row grid, the panel-header toggle pattern, and the buyer-score chip from the existing Buyer Profile.
