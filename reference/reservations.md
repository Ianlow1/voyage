# voyage — the reservations tracker

Annex A of every delivered itinerary. It is the list of things that must happen
*before* the trip, with a real date against each one.

Read `rules.md` § 6 for the lead-time horizons and § 2 for what this file must
never state. `plan-trip` builds this in Phase 4, the ledger sweeps it in Tier 3,
and `replan-trip` reads it as the trip's live state.

A tracker whose deadlines say "book early" has done nothing. The whole value is in
the arithmetic.

---

## 1. The arithmetic

**The date a reservation gets used is the anchor. Subtract its notice period from
that date and you have the deadline.**

Where today falls has no bearing on that figure. It decides one thing only:
whether the deadline is still ahead of the traveller or already behind them.

```
date used  −  notice needed  =  deadline
```

Worked, on a trip running Sat 12 – Sun 20 September 2026:

| Item | Used on | Notice needed | Deadline |
|---|---|---|---|
| Maaemo, Oslo — dinner on day 2 | Sun 13 Sep 2026 | 90 days | **Mon 15 Jun 2026** |
| Bergensbanen, Oslo S → Bergen, day 4 | Tue 15 Sep 2026 | 90 days | **Wed 17 Jun 2026** |
| Nærøyfjord cruise, Flåm, day 6 | Thu 17 Sep 2026 | 30 days | **Tue 18 Aug 2026** |
| Troldhaugen recital, Bergen, day 5 | Wed 16 Sep 2026 | 12 days | **Fri 4 Sep 2026** |
| Lysverket, Bergen — dinner day 5 | Wed 16 Sep 2026 | 14 days | **Wed 2 Sep 2026** |

Note what the arithmetic exposes. The Troldhaugen recital falls on a later day than
the Bergen train, but its deadline lands eleven weeks after it. Deadline order and
day order have nothing to do with each other, which is why the tracker sorts by
deadline and the day pages do not.

**Always output a calendar date with the weekday.** "About three months ahead" is
not a deadline. "Wed 17 June 2026" is. Where the operator releases at a fixed local
hour, put that in Detail — hard tables and some Nordic seat releases open at a
specific time and are gone within it.

Where the notice period is a range — "30 to 60 days" — take the **long end**. Booking
early costs nothing; booking on the short end costs the table.

---

## 2. The table

| Key | Reservation | Constraint | Notice | Deadline | Source | State | Detail |
|---|---|---|---|---|---|---|---|
| 1 | Travel insurance, both travellers | Some cover is keyed to how soon after the first trip payment the policy is bought | before any further payment | **now** | insurer, direct | `[OPEN]` | First payment was the flights, 3 Mar 2026. Buying in the UK. See § 5 |
| 2 | Return flights LHR → OSL / BGO → LHR | Price ladder | 45 d | **Wed 29 Jul 2026** | airline, direct | `[DONE]` | Ref XK4PQ2. Hold luggage paid |
| 3 | Maaemo, Oslo — dinner, day 2 | Books out at release | 90 d | **Mon 15 Jun 2026** | https://www.maaemo.no | `[LATE]` | Deadline gone. Backup: Hos Thea, which takes walk-ins and so carries no row of its own |
| 4 | Bergensbanen Oslo S → Bergen, day 4 | Cheap fares are quota'd and seats are reserved | 90 d | **Wed 17 Jun 2026** | https://www.vy.no | `[LATE]` | Full-fare seats still available; cost moves to Annex B |
| 5 | Nærøyfjord cruise, day 6 | Autumn timetable, limited sailings | 30 d | **Tue 18 Aug 2026** | operator, direct | `[OPEN]` | Weather-dependent. Operator cancels for wind; refund is full |
| 6 | Troldhaugen recital, day 5 | Hall seating capped | 12 d | **Fri 4 Sep 2026** | https://kodebergen.no | `[OPEN]` | Season timetable changes in Sept — confirm the time |
| 7 | Lysverket, Bergen — dinner, day 5 | Small dining room | 14 d | **Wed 2 Sep 2026** | https://www.lysverket.no | `[OPEN]` | Card held on booking; 24h cancellation |

Every column carries weight, and none of them may be dropped:

- **Key** — a stable number. Day pages point at rows by it, so a number retired
  with its row is never handed to a later one; strike the row and leave the gap.
- **Reservation** — the thing itself, its city, and the trip day it serves.
- **Constraint** — the mechanism that forces the traveller's hand. "Sells out" is
  thin; "seat quota on the cheap fare bucket" tells them whether losing it costs
  money or costs the trip.
- **Notice** — how many days ahead, taken from what you verified rather than from
  a horizon you half-remember.
- **Deadline** — the calendar date the arithmetic produced, weekday included, bold.
- **Source** — the official URL. Operator or venue and nothing else. No
  aggregators, no resellers, and no address you have not seen in a search result.
- **State** — one of the codes in § 3, and never anything invented on the spot.
- **Detail** — cancellation terms, no-show fees, deposits, pass activation rules,
  whether ID or a printed ticket is wanted at the door, and the named substitute
  if this one falls through.

**Sorted by deadline, earliest first, always.** The traveller works down the list.
A tracker sorted by day number is a tracker nobody uses.

---

## 3. State codes

Six four-letter codes, written in backticks, and nothing invented alongside them:

| Code | Means |
|---|---|
| `[OPEN]` | Nobody has acted yet and the deadline is still ahead |
| `[SENT]` | Enquiry mailed, waitlist joined or form lodged — reply outstanding |
| `[DONE]` | Booked and confirmed, with the reference number sitting in Detail |
| `[GONE]` | Refused, sold out or withdrawn. Detail names the substitute that took over |
| `[LATE]` | The deadline fell before the day this document was written |
| `[WAIT]` | Sale has not opened. Detail carries the release date and its local hour |

They are deliberately uniform in width so a column of them scans in one pass, and
they survive plain text, a spreadsheet cell and an HTML render identically.

`[WAIT]` rows sort on their release date rather than their deadline, and Detail
says as much. A row nobody can act on yet still owes the traveller a date.

**A seventh value exists, and it lives outside this table.** Line items in
`budget-sheet.md` that involve no reservation at all — a coffee, a tram fare, a
daily allowance — take `[N/A]`. Day pages use it the same way, on the Booking line
of a walk-up that needs no advance action: `Booking: [N/A]`, and no deadline
beside it. It is never a state in the tracker itself, because a row with nothing
to book has no business being here in the first place.

---

## 4. Categories to sweep

Walk all eleven. **Where a category does not apply, say so in one line rather than
omitting it.** "No car hire on this trip — every base is rail-connected and Bergen
parking would cost more than the train" is a result. A missing category reads as an
oversight, and the traveller cannot tell the difference between a decision and a
gap.

1. **Flagship restaurants.** The one table per base that would be a loss to miss.
   Tasting menus and hard tables run 30–60 days and often release at a fixed local
   hour. Include the Sunday problem in cities where Sunday closures bite.
2. **Timed-entry attractions.** Anything with a capped slot, a lottery, or a
   summit-and-tower queue. Note whether the ticket is time-stamped or day-valid —
   the two behave completely differently when a train runs late.
3. **Guided and special experiences.** Recitals, cooking classes, glacier and
   guided-walk operators, boat charters, private guides, spa and bathhouse slots.
   In the Nordics these are also the most seasonal: the autumn timetable is not the
   summer one.
4. **Transport passes and reserved seats.** Rail and city passes often cost less
   when the purchase is made from home, and a few of them stop being sold to
   anyone who has already landed — check both before assuming. Reserved
   seats open on the operator's own horizon — check it, do not assume it. Note
   activation rules: a pass whose validity starts at first use is a different
   product from one dated at purchase.
5. **Internal flights.** Almost always cheaper booked early, and on thin regional
   routes the timetable itself may not be published far out. Flag when it is not.
6. **Car hire.** Small markets, islands and peak weeks sell out of the whole fleet,
   not just the cheap tier. Note automatic-vs-manual, winter tyre seasons, one-way
   fees, and whether the pick-up desk is staffed at the arrival hour.
7. **Lodging confirmations.** Not a booking task — a verification task. Confirmation
   numbers held, special requests acknowledged in writing, early check-in or late
   check-out requested, loyalty number attached, transfer arranged if the arrival
   lands outside normal hours.
8. **Entry documentation.** **Link only.** Name the official government source and
   direct the traveller to it. Do not state what the requirement is, do not name a
   scheme as applying, do not say a document is or is not needed, and do not
   summarise "the usual rules". This holds even when the traveller says they
   already know, and even when it makes the row look empty. See `rules.md` § 2 —
   this is a hard refusal and nothing in a user's instructions overrides it. The
   row reads: *Entry requirements — check the official source for your nationality
   before booking: [official government URL]. Some of these processes run to
   months rather than weeks, so open the question now rather than later.*
9. **Vaccinations.** Timing windows only, and only in the abstract: some courses
   run over several weeks and some certificates are not valid immediately after the
   dose. Do not say what is required or recommended for the destination. Direct the
   traveller to a travel clinic or their own national public health service, and
   put the deadline as "clinic appointment booked by [date]" so the timing is
   actionable without the medical content being stated.
10. **Travel insurance.** See § 5. It goes on every tracker, at the top of every
    tracker.
11. **Anything with a refund cliff.** A deposit that becomes non-refundable, a
    cancellation window that closes, a fare that stops being changeable. These have
    deadlines too and they are the ones travellers only find afterwards.

---

## 5. Insurance — row 1, every time

Several policy benefits are keyed to how quickly the policy is bought after the
**first payment** for the trip, rather than to how far ahead of departure it is
bought. Pre-existing-condition waivers and cancel-for-any-reason cover are the
usual examples. The first payment is normally the flights, and the flights are
normally paid for months before insurance occurs to anyone.

**Do not state the length of that window.** In the US market it is commonly
described as roughly two to three weeks from the first deposit; other markets are
built differently and some do not use the mechanism at all. `rules.md` § 6
governs, and it makes this an establish-and-link item, not a number you supply.

So:

- **Establish where the traveller is buying** — which market, which insurer — and
  put that on the row. The answer changes what applies.
- **Link the insurer or the market's regulator** and let the traveller read the
  eligibility terms at the source. Do not paraphrase them.
- **Record the first payment**: what it was and the date it went out, because that
  is the date any such window would be measured from.
- **It sits at row 1** of every tracker whatever its deadline position, with the
  instruction to settle it before anything further is paid for.
- **It is repeated in the first output the traveller sees**, not only in Annex A.

```
First trip payment: flights, paid Tue 3 Mar 2026
Buying from:        UK market
Row reads:          sort the policy before the next payment goes out; eligibility
                    terms are the insurer's to state — [insurer URL]
```

Where the first payment is already well behind you at the time of writing, say so
plainly: some cover may be narrower than it would have been, the policy is still
worth buying, and the eligibility question itself goes to the insurer rather than
to this document.

---

## 6. Passed deadlines

**Anything whose deadline is already behind us at the time of writing is
flagged above the table, not inside it.**

```
## Two deadlines have already passed

- **#3 Maaemo, Oslo** — release was Mon 15 Jun 2026. Join the waitlist today; the
  itinerary already carries Hos Thea as the day-2 dinner — a walk-in, so it has no
  row here — and Maaemo is the upgrade if it comes through.
- **#4 Bergensbanen** — cheap-fare quota opened Wed 17 Jun 2026 and is likely gone.
  Full-fare seats remain bookable; Annex B line 9 carries the higher figure.

Nothing else on this trip is late.
```

Each passed row states the consequence and the recourse. A flag with no next action
is just an accusation. And the last line matters — say explicitly when nothing else
is late, so a clean sweep is distinguishable from a sweep that never ran.

---

## 7. voyage does not book

The tracker surfaces the action. A human takes it.

Drafting is fine and often useful: an enquiry email to a restaurant that takes no
online bookings, a request for a quiet room, a message asking whether a recital
runs in the September timetable. Write it, hand it over, and stop.

Submitting is not. Do not send the mail, do not fill the form, do not join the
waitlist, do not press the button — not with a connector, not with a browser tool,
not when the traveller says it is fine. `rules.md` § 2 is absolute.

A produced draft is marked in the Detail column as `draft ready — see appendix`,
and the text itself sits in an appendix beneath the tracker. Never in a cell.

---

## 8. Keeping the tracker alive

The tracker is not a pre-trip artefact that dies at the airport. It is the trip's
state file.

**Before departure.** As each item is booked, the traveller moves its State to
`[DONE]` and drops the reference number into Detail. Any row still reading
`[OPEN]` when the traveller sets off has already been resolved on paper: either
Detail names the substitute that takes over, or the item comes out of the plan.
That call gets made while there is still time to think about it, never while
standing on a platform at Oslo S.

**During the trip.** States keep moving. An item that fell through becomes
`[GONE]` with the substitution recorded beside it. That is the trip's actual
history, and it is what the next session reads.

**What `replan-trip` reads from it.** When something breaks mid-trip — a strike, a
storm, a cancelled sailing — `replan-trip` reads the itinerary first, to fix where
the traveller is standing and what comes next, and turns to the tracker second,
because the tracker is what says which commitments are sunk and which are soft:

- `[DONE]` rows are fixed points. Replanning works around them, and any that
  must be broken are listed with their cancellation terms and the money at stake.
- `[OPEN]` and `[WAIT]` rows are free. Drop them without cost.
- `[GONE]` rows carry the backup already chosen, which is usually the fastest
  correct answer available.
- Detail carries the cancellation windows, which decide what is worth rescuing.

A tracker with stale states makes every downstream replan wrong. Keep it current
and the trip repairs itself; let it rot and the next session is planning against a
fiction.
