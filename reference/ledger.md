# voyage — the verification ledger

The ledger is the quality mechanism of this plugin. Everything else arranges
information; the ledger is what stops the information being wrong.

Read this alongside `rules.md`. The ledger is applied by `plan-trip` before
delivery and by `audit-itinerary` on demand.

---

## The principle

Generation and verification are different jobs and must not happen in the same
pass. A model checking its own work as it writes will rationalise; a model
checking work presented to it cold will find fault. So the ledger runs *after* the
itinerary exists, in an adversarial frame:

> You are trying to find what is wrong with this plan. A plausible plan that
> breaks in the field is a worse outcome than a plan you criticised too harshly.
> Be skeptical, not validating. Default to flagging.

---

## Tier 1 — every item

Every named place in the delivered document carries these. An item that cannot
carry them does not survive to delivery.

**Address.** Street address, in local script where the local script is what the
taxi driver reads. Coordinates where there is no useful street address.

**Primary source URL.** The venue's or operator's own site. Not a reseller, not a
review aggregator, not a blog. If the only source is a blog, the item cannot rise
above `LOW` confidence — and `LOW` items are dropped, not hedged (see
**Confidence**, below). Find a primary source or find another place.

**Evidence-of-operation year.** The most recent year for which you have actual
evidence the place is trading — a dated review, a current-year events page, a
timetable for this season. If that year is more than eighteen months before
today's date, the item is marked `UNVERIFIED`.

**Confidence.** One of:

- `HIGH` — official source, current-year evidence, address confirmed
- `MEDIUM` — credible source but no current-year evidence, or address unconfirmed
- `LOW` — single weak source, or details that do not reconcile between sources

`LOW` items are **dropped, not softened**. Do not hedge them into the document
with a caveat; remove them and put something you can stand behind in the slot.
This is the countermeasure to the documented cases of travellers being sent to
places that do not exist.

**Price.** Per person, as a range, with the currency and the date the price was
seen.

**Booking lead time**, and the computed calendar deadline for this trip.

**Opening hours** — recorded as a *confirm before you go* field with the official
link beside it. Never written as a bare fact.

---

## Tier 2 — every day

**Weekday closure check.** For each dated day, take the actual weekday and check
every ticketed venue on it against that weekday. Not the generic "closed
Mondays" — the specific date. Report the check, including when it passes.

**Traversability.** Walk the day in sequence and compute, for each leg:

```
transit (raw)  +  transit buffer  +  queue  +  visit  =  block total
```

Four numbers, never one. Then check the day's total against the hours actually
available between the first start and the last finish. If it does not fit, the
day is a fail — say by how much.

**Slack check.** No two timed entries with under sixty minutes between the end of
the first and the start of the second, in any city with traffic.

**Geographic coherence.** Does the day's sequence double back? Map the legs. If
the traveller crosses the same river, ring road or bay more than twice, the day
needs re-clustering.

**Anchor count.** Exactly one, per the pacing law. Zero on arrival day. Two only
where adjacent and one is short.

**Backups present.** Four named things per day. `rules.md` § 3 requires a fallback
for each of the three ways a day fails, and every day also needs somewhere to eat
without a booking:

1. **Weather alternative** — a named indoor or sheltered substitute for the
   anchor, reachable from where the traveller will actually be standing.
2. **Stamina alternative** — for when the party runs out of road: what to cut,
   and the named place they go instead to sit down.
3. **Closed-door alternative** — a named substitute for the anchor being shut by
   strike, renovation, private hire or a closure day nobody caught.
4. **No-reservation meal fallback** — a named place that takes walk-ins, near
   where the day actually ends.

Each of the four is a specific named place carrying Tier 1 evidence and a travel
time measured from where the traveller will actually be standing when the plan
fails — not from the anchor, not from the hotel (`rules.md` § 3). "Somewhere
nearby" is not a backup, and a fallback you cannot evidence is not a fallback. A
day missing any of the four is incomplete — report which one.

**Meal coverage.** Every day accounts for every meal — booked, planned, or
explicitly left free. A day with an unexplained gap where dinner should be is an
incomplete day.

**Deliberately not checked here.** The day header's theme, energy level and
weather note are editorial — the render shows them because they orient the
reader, but they are judgements rather than checkable facts, so the ledger does
not score them. Their absence from Tier 2 is a decision, not an oversight.

---

## Tier 3 — every trip

**The constraint ledger.** This is the countermeasure to the documented collapse
of these models under multiple simultaneous constraints, and to the finding that
implicit needs — accessibility, children's nap windows, real dietary limits — are
satisfied under half the time even by the best models.

At the end of the document, reproduce every constraint the traveller stated as a
numbered line, and mark each `PASS` or `FAIL` with the evidence:

```
| # | Constraint (as stated) | Verdict | Evidence |
|---|------------------------|---------|----------|
| 1 | Budget under £2,400 pp all-in     | PASS | Total £2,180 pp — see budget sheet |
| 2 | No more than 2 hours walking/day  | FAIL | Day 4 totals 3h10 — see note on Day 4 |
| 3 | Coeliac — reliable GF at dinner   | PASS | All 6 dinners confirmed GF, sources cited |
| 4 | Mum can't manage stairs           | PASS | All lodging lift-served; Day 6 cable car substituted for the steps |
```

Re-read the traveller profile to build this. Do not reconstruct it from memory of
what you wrote — read the stated constraints back and score against them.

**Any FAIL is surfaced at the top of the delivered document, not buried.** A
failure the traveller can see and accept is fine. A failure they discover in
Bergen is not.

**Assumption register.** Everything you decided because the traveller did not
tell you — budget tier, walking tolerance, how early they like to start, whether
they drink. List it. This is where wrong plans quietly originate.

**Entry-requirements check.** Confirm the document states no visa, vaccination or
customs fact, and that it links the official source instead. If any such fact has
crept in, strip it.

**Booking deadline sweep.** Every deadline computed backwards from the date of
use, expressed as a calendar date, sorted earliest first. Anything already in the
past at the time of writing is flagged loudly.

**Source sweep.** Every expiring claim has a URL and a checked-on date. Count the
items that do not and report the number.

---

## Output format

The ledger reports as a table, defects first, with the fix stated:

```
## Ledger — 3 failures, 2 warnings

### FAIL · Day 4 · traversability
Day 4 needs 9h20 of transit, queue and visit inside an 8h window.
Fix: drop the afternoon secondary, or move the 14:00 entry to 15:30.

### FAIL · Constraint 2 · walking tolerance
Stated limit 2h/day. Day 4 totals 3h10.
Fix: as above — the same edit resolves both.

### FAIL · Kaffistova, Oslo · evidence
Last evidence of operation 2023. Exceeds the 18-month rule.
Fix: dropped. Replaced with [named alternative], official site, 2026 menu.

### WARN · Day 6 · slack
42 minutes between the fjord cruise landing and the timed museum entry.
Fix: acceptable if the cruise is punctual; note the risk on the day page.
```

Where nothing fails, say so explicitly and show the checks that ran. A silent
pass is indistinguishable from a check that never happened.

---

## Running the ledger

1. Read the traveller profile and the stated constraints **first**, before reading
   the itinerary. Establish what the plan is meant to satisfy before you see what
   it does.
2. Run Tier 1 across every item. Search to verify; do not check from memory.
3. Run Tier 2 day by day, in date order.
4. Run Tier 3 across the whole document.
5. Report defects with fixes.
6. If invoked from `plan-trip`: apply the fixes, then re-run the affected checks.
   Repeat at most twice. If failures survive two rounds, deliver anyway with the
   residual failures at the top of the document — do not loop indefinitely, and do
   not quietly delete the finding to make the check pass.

## What the ledger does not do

It does not verify that the traveller will *enjoy* the trip. Taste is not
checkable and the ledger should not pretend otherwise. It checks that the places
exist, that the days are possible, and that the plan does what the traveller
asked for.
