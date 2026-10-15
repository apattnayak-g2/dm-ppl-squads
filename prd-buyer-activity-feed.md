# PRD — Buyer Activity Feed

**Status:** Draft for review · new concept · Thursday session with Chase & Ricardo
**Prototype:** https://apattnayak-g2.github.io/dm-ppl-squads/research/buyer-activity-feed/
**Owner:** Arosha Pattnayak

## Problem

We now have unified buyer accounts, so when a buyer we already have a profile for comes back to the site on their own, we can recognize them — but today that recognition doesn't go anywhere. Reactivation only becomes visible to a rep if it happens to coincide with a new call being logged or a new lead coming in through an existing channel. A known, previously-qualified buyer who quietly returns to browse is, for all practical purposes, invisible to the rep who last worked them, and the window to re-engage them while they're actually active can close before anyone notices.

## Goals

- Detect when a buyer who already has a profile returns to the site, and turn that into something a rep can act on.
- Route that signal to the rep who last worked the buyer, to preserve relationship continuity.
- Let reps triage these signals on their own time, separate from the live Opportunity Queue, and promote the ones worth acting on.
- Keep the signal useful rather than noisy — a buyer shouldn't be able to spam the feed by refreshing a page a dozen times in an afternoon.

## Non-goals (v1)

- **Not page-level intent tracking.** The trigger is *any* site visit by a matched buyer, not specific vendor/category page views. This version cannot tell you what they looked at — only that they're back. Page-level tracking is a candidate Phase 2 upgrade, not part of this PRD.
- **Not a new Opportunity Queue row.** A passive visit isn't the same category of event as a scheduled call or an inbound lead, so it doesn't compete for the same slot in the queue. It lives in its own feed until a rep decides to promote it.
- **Not territory-based reassignment.** v1 always routes to the rep who last worked the buyer, not through normal territory rules.
- **Not a real data pipeline.** This PRD and its prototype describe the intended UX and workflow; the actual identity-resolution / site-visit pipeline that would feed it does not exist yet and is out of scope here.

## Users

Advocates and Advisors who have previously worked a buyer and want to know when that buyer becomes active again.

## Functional requirements

1. **New "Activity Feed" nav item** under Buyer Accounts, with a badge showing the count of active signals.
2. **Trigger:** any site visit from a buyer identity-matched to an existing Buyer Account.
3. **Per-signal card** shows: buyer avatar/name/title/company, buyer score and status chip (e.g. "Hot · Re-engaged"), and visit recency (e.g. "Returned to site · 12 min ago"), with a live indicator for very recent activity.
4. **"Known interest (from buyer profile)" panel:** market plus available/blocked product counts, pulled from the buyer's qualification history — explicitly labeled as inferred from profile history, not from this visit, since the chosen trigger has no page-level data.
5. **"Routes to" panel:** the rep who last worked this buyer, plus the date they last worked them.
6. **Per-card actions:**
   - *Flag as opportunity action* — promotes the signal into the Opportunity Queue, routed to the listed rep; card moves to an "Added to queue" state.
   - *Snooze* — removes it from the active list until tomorrow, undoable.
   - *Dismiss* — removes it from the feed entirely.
7. **Summary stat strip:** active signals today, awaiting action, added to queue this week, snoozed.
8. **Status filters:** All / New / Added to queue / Snoozed, plus Rep / Market / Recency filters consistent with existing Portal filter conventions.
9. **Dedupe rule:** the same buyer cannot generate more than one active signal per rolling 24-hour window, regardless of how many times they visit in that window.

## Open questions

- **Routing fallback:** what happens when the last-worked rep is off territory, inactive, or it's been more than ~12 months since they worked this buyer? v1 assumes same-rep routing always, but needs a documented fallback rule from Chase/Ricardo.
- **Dedupe window:** confirm whether 24 hours is the right cadence, or whether it should vary by buyer score/market.
- **Placement:** does this deserve its own left-nav spot (as mocked, under Buyer Accounts), or should it live as a tab inside the Opportunity Queue instead?
- **Phase 2 scoping:** if this signal type proves valuable, is upgrading to vendor/category page-view tracking (to show real intent instead of inferred interest) worth the investment?

## Success metrics (proposed)

- Percentage of flagged signals that convert into a scheduled call.
- Median rep response time from a signal appearing to it being flagged, snoozed, or dismissed.
- Reduction in "silently churned" known buyers — buyers who return to the site but go uncontacted before this feature existed.

## Dependencies & risks

- **Primary dependency:** an identity-resolution pipeline that matches site visits (logged-in or cookie-matched) back to an existing Buyer Account. This does not exist today; this PRD defines the UX ahead of that pipeline being built.
- **Noise/alert-fatigue risk** if the dedupe window and routing rules aren't tuned before launch.
- **Misrouting risk:** without a fallback rule, a hot buyer could route to a rep who's no longer available to act on it.

## Prototype notes

Reuses the same Portal chrome, buyer-score chip, and product-eligibility styling as the Buyer Summary in the Opp Queue mock, so the two can ship as a coherent pair. Includes a "+ Simulate a new visit" control and five example buyers covering the new / added-to-queue / snoozed states, for walking through the full lifecycle live in review.
