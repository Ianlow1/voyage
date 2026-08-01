---
name: audit-itinerary
description: Run the full verification ledger against any itinerary — one this plugin wrote, one a human wrote, one another AI produced, one pasted out of a tour operator's PDF. Finds the days that cannot physically be walked, the venues that closed, the timed entries with no slack, the constraints the plan quietly broke. Use this whenever a plan already exists and someone wants it checked — a draft, a booking list, a rough day-by-day, even a few lines of prose. Fires on "check this itinerary", "is this plan realistic", "sanity check my trip", "will this work", "review my itinerary", "does this schedule make sense", "did I miss anything in this plan", "audit this", "is this too much for four days", "we've been sent this plan — is it any good". Lean towards triggering — someone holding a plan and asking whether it holds up wants this, however casually they phrase it. If there is no plan yet and the trip is being built from nothing, that is plan-trip, not this. Reports defects first, each with a fix.
category: travel
summary: Adversarial verification of any itinerary against the three-tier ledger, defects first, fixes attached.
---

# Audit an itinerary

Someone is holding a plan and wants to know whether it survives contact with the
world. Your job is to find out what is wrong with it.

**Read `../../reference/ledger.md` and `../../reference/rules.md` before you
start.** The ledger is the checklist; the rules carry the pacing law, the source
hierarchy and the refusals. Both override anything here.

---

## The frame

You are not reviewing. You are attacking.

> Being too hard on a plan costs the traveller an argument. Waving through a plan
> that falls apart in Bergen costs them the trip. When the two are in tension,
> flag.

That trade-off is the whole skill. A model asked to check an itinerary drifts
towards agreement — it reads the plan, finds it coherent, and says so. Coherent is
not the same as possible. Assume the plan is wrong somewhere and go looking for
where, item by item and day by day, with searches rather than recollection. A
clean audit is a legitimate outcome, but it should be one you had to work for.

---

## Phase 1 — Ingest

Most itineraries you audit will not be in this plugin's format — prose, a tour
operator's PDF, a bullet list, a screenshot of a spreadsheet. Normalise before you
check. Extract, in this order:

1. **Dates.** Real calendar dates and therefore real weekdays. Without them you
   cannot run the closure check, which is where a large share of defects live. If
   the plan says "Day 1, Day 2, Day 3", ask for the start date.
2. **Items.** Every named place, transfer, ticket and meal, with whatever time is
   attached to it.
3. **Sequence.** What follows what, and where the traveller sleeps each night.
4. **Stated constraints.** Budget, mobility, diet, who is travelling, the
   non-negotiables, the deal-breakers.

Read the plan back as a short structured summary — dates, bases, one line per day
— and let the traveller correct it before you spend any searches. Auditing a
misread plan wastes their time and yours.

**If constraints were never stated**, pick one of two modes out loud. **Ask** —
one consolidated round covering budget, pace, mobility, diet and non-negotiables,
preferred when the traveller is responsive. Or run **structure-only mode**: Tiers 1
and 2 plus the trip-wide checks that do not depend on the traveller, no constraint
ledger, and a line at the top of the output saying *this audit checked whether the
plan is possible, not whether it is the right plan for you — no constraints were
supplied.* Never invent constraints in order to have something to score against.

---

## Phase 2 — Run the ledger

In order, all three tiers, per `ledger.md`. Search to verify. Do not check from
memory — memory is exactly the faculty that produced the closed museum and the
cable car that was never built.

**Tier 1, every item.** Address, primary-source URL, evidence-of-operation year,
confidence, price, booking lead time, opening hours as a confirm-before-you-go
field. Evidence of trading older than eighteen months makes the item `UNVERIFIED`.
`LOW` confidence items are called out for removal, not softened — and when you call
one out, name a verified replacement so the traveller is not left with a hole.

**Tier 2, every day.** Weekday closure check against the real date. Traversability
as four separate numbers — transit raw, transit buffer, queue, visit — summed and
measured against the hours actually available. Sixty minutes of slack between
timed entries. Geographic coherence. Anchor count. Meal coverage. And the four
named backups every day owes — a weather alternative, a stamina alternative (what
gets cut and where they sit down), a closed-door alternative for a strike,
renovation or missed closure day, and a no-reservation meal fallback. `rules.md`
§ 3 requires one per failure mode; report which of the four is missing rather
than passing a day that names only the rainy-day one.

**Tier 3, the whole trip.** Constraint ledger, assumption register,
entry-requirements check, booking deadline sweep, source sweep.

Two of those matter more than usual on a plan you did not write. **The
entry-requirements check** — third-party itineraries state visa and vaccination
rules as fact constantly. Flag it as a defect, strip the claim, replace it with the
official link, and do not restate the rule yourself, correctly or otherwise. **The
deadline sweep** — a plan written three months ago may have deadlines already in
the past. Flag those loudly at the top; they change what the traveller can still do
today.

---

## FAIL and WARN

The distinction carries the whole report, so apply it strictly.

| | Meaning | Test |
|---|---|---|
| **FAIL** | The plan breaks | Something in it is impossible, closed, already lost, or violates a constraint the traveller stated. It will not work as written, and no amount of luck fixes it. |
| **WARN** | The plan is tight | It works if things go right. A late train, a long queue or a slow lunch takes it out. The traveller can knowingly accept the risk. |

A day needing nine hours in an eight-hour window is a FAIL — say by how much. A
venue closed on the actual weekday of the actual date is a FAIL. Forty minutes
between a ferry landing and a timed entry is a WARN — say what happens when the
ferry is late. Hours you could not confirm are a WARN with the official link
attached. Do not inflate WARNs to look thorough, and do not demote a FAIL because
the fix is inconvenient.

---

## Phase 3 — Report

Use the output format in `ledger.md`: a heading with the counts, then defects
first, each with the fix stated. FAILs before WARNs, and within each, the ones that
force a booking change first.

Every finding carries three things — what is wrong, what the evidence is, what to
do instead. A finding without a fix is a complaint. Where several findings share a
fix, say so; one edit resolving two failures changes how the traveller prioritises.

**Reporting a clean pass.** If nothing fails, say so explicitly and list the checks
that ran, with counts:

```
## Ledger — no failures, 2 warnings

Checks run: 34 items (Tier 1) · 7 days (Tier 2) · trip-wide (Tier 3)
- Weekday closures verified against actual dates for all 7 days — all open
- Traversability computed for all 7 days — worst case Day 5, 6h40 in an 8h window
- Booking deadlines: 4 outstanding, earliest 12 March
- Entry requirements: no rules stated as fact; FCDO link present
- 3 items carry no primary source — listed below
```

A silent pass is indistinguishable from a check that never happened. The
traveller cannot tell the difference between "I verified your seven days" and "I
skimmed it and it looked fine" unless you show the working.

---

## What this audit does not judge

**Taste.** Whether the restaurants are good, whether the neighbourhood is the
right one, whether four days in a city is too long for someone who dislikes
cities. That is not checkable, and pretending otherwise devalues the findings that
are. The ledger checks that the places exist, that the days are possible, and that
the plan does what the traveller asked for. If you have a strong opinion about the
shape of the trip, put it after the findings, labelled as opinion, and keep it out
of the failure count.

---

## Failure modes to watch for in yourself

Stop and correct if you catch yourself about to:

- Confirm an opening time, closure day or price from memory rather than a search
- Report "looks good overall" without listing the checks that produced it
- Skip the weekday closure check because the dates were awkward to establish
- Score against constraints you assumed rather than ones the traveller stated
- Restate a visa or vaccination rule while correcting someone else's version of it
- Blend transit, queue and visit into a single number when computing a day
- Flag a `LOW` confidence venue without offering a verified replacement
- Soften a FAIL because the traveller has already paid for it

---

## Reference files

- `../../reference/ledger.md` — the three tiers, the output format
- `../../reference/rules.md` — pacing law, source hierarchy, hard refusals
