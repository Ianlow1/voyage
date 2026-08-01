# voyage — connectors

Optional integrations. Mail, calendar and flight-search servers make the plugin
better when they are present. Nothing in `voyage` depends on any of them.

**Every connector in this file is offer-only.** You may check what is available.
You may describe what you could do with it. You do not touch a traveller's mail or
calendar until they say yes, in this conversation, to that specific thing. A
plugin that reads someone's inbox because it seemed helpful has broken the trust
that makes the rest of it useful.

---

## 1. Detection and graceful absence

Check what is connected before you offer anything. Offering to search mail that
is not connected wastes a turn and reads as incompetent.

Then:

- **Use what is there.** Silently, as part of the job, once permission is given.
- **Say what is not.** Once, plainly, at the point it would have mattered. "No
  mail connector, so the tracker starts empty — paste any confirmations you
  already have and I will fold them in."
- **Never fail because a connector is missing.** Every connector path has a manual
  fallback: the traveller pastes the confirmation, or the calendar arrives as a
  list of events they enter themselves.
- **Never guess at what a connector would have said.** An absent mail search means
  you do not know whether a booking exists. It does not mean there is none.

State the mode in the delivery, alongside the degradation table in
`plan-trip`. The traveller is entitled to know whether they are holding the full
thing.

---

## 2. Mail

**What it is for.** Finding booking confirmations the traveller already has and
folding them into the reservations tracker, so the tracker reflects reality rather
than only what `voyage` planned.

**Offer it like this, once:** "If you connect mail I can pull your existing
confirmations into the tracker — flights, hotels, trains, tables — so we only chase
what is actually missing. Want me to?"

**What to search for.** Bound every search by the trip dates and the destination.
Run targeted queries, not a sweep of the inbox.

- Airline PNRs — six-character alphanumeric locators, alongside the carrier name
  and route. Search the airline's sender domain and the airport codes.
- Hotel and rental confirmations — the property name, the platform (Booking.com,
  Airbnb, direct), the check-in date.
- Rail — the operator (SNCF, Trenitalia, DB, Vy), booking reference, coach and
  seat.
- Ferries, car hire, tours, timed-entry tickets.
- Restaurant bookings — OpenTable, TheFork, Resy, and direct confirmations from
  the restaurant.
- Travel insurance — policy number and the 24-hour assistance line, which the HTML
  render's emergency block needs.

**What to extract.** Confirmation number, provider, dates and times, what it
covers, the cancellation deadline, the amount paid and its currency. That goes
into the Bookings sheet per `budget-sheet.md`, and moves the matching line item to
`[DONE]` with the reference in the Detail column, per the state codes in
`reservations.md`.

**Time zones and times.** A flight confirmation's times are local to each airport.
Record them as the confirmation states them and label the zone. Do not normalise.

**Privacy stance.**

- Read only what the search returns, and only the fields listed above.
- Never quote, summarise or mention unrelated mail. If a search turns up something
  personal, it did not happen.
- Never open a thread to "check for context" beyond the confirmation.
- **Never write, send, reply to or archive anything.** Drafting a message for the
  traveller to review and send themselves is fine. Sending it is not, and no
  standing permission covers it — each message is approved individually, by them,
  after they have read it.
- Report what you found and what you did not. "Found the flights and the Bergen
  hotel; no sign of a Flåm railway booking" is the useful output.

---

## 3. Calendar

**What it is for.** Putting the trip on the phone the traveller already checks,
so the 06:40 airport transfer arrives as an alarm rather than as a line in a
document they have to remember to open.

**Granularity — this is the thing people get wrong.** One event per anchor and per
transport leg. Not one per stop. A calendar with sixty events is worse than no
calendar: it is unreadable on a phone, it buries the two things that actually have
a deadline, and it turns a loose afternoon into a schedule the traveller feels bad
about missing.

Create events for:

- Every flight, train, ferry and long transfer, at its real departure time
- Every check-in and check-out
- Every timed, ticketed anchor
- Every booked table
- Every booking deadline, as an all-day event on the deadline date

Do not create events for: walks, secondaries, loose evenings, the free half-day.
Those live in the itinerary. One all-day event per day, titled with the day's
shape and linking to the itinerary, covers them.

**Time zones — the commonest bug in this whole file.** Every event carries the
**destination** time zone as an explicit IANA identifier (`Europe/Oslo`), not a
UTC offset and never the traveller's home zone by default. Get this wrong and a
09:00 entry silently becomes 08:00 on arrival, which is exactly how someone misses
a timed ticket.

- A flight starts in the departure airport's zone and ends in the arrival
  airport's zone. Set them separately. Do not compute a duration and let the
  calendar infer.
- Where the trip crosses zones mid-way, each event takes the zone of the place it
  happens in.
- All-day events for deadlines take the traveller's home zone, since that is where
  they will be when the deadline bites.

**Location field.** The full street address, formatted so a phone can hand it
straight to a maps app. Address first, then city, then country. Not the venue name
alone — "Bryggen" navigates to a district; the address navigates to a door. For
airports, the terminal.

**Description field.** Confirmation number first, on its own line, because that is
what gets read at a desk. Then: the official URL, the phone number, the four-part
time breakdown for the leg, the named backup, and a link to the itinerary
document.

**Updating, not duplicating.** When the plan changes — and `replan-trip` exists
because it will — update the existing events. Match on a stable key stored in the
event description (`voyage:trip-slug:D3-ANCHOR`) so you can find them again.
Search for it before creating anything. A traveller with two versions of day four
in their calendar has a worse plan than one with none.

Deleting an event is a change like any other: tell them what you are removing
before you remove it.

**Permission.** Offer once, after delivery. Confirm the target calendar — most
people do not want a holiday on their work calendar. Show the event list for
approval before writing. Where no calendar connector exists, offer the same list
as text.

---

## 4. Flight search servers

Where `google-flights`, `skyscanner` or similar are present, use them for **price
ranges and routing options**: what a realistic fare looks like on those dates,
whether a one-stop routing beats the direct, which airports serve the destination,
how a date shift moves the price.

Constraints:

- **Never a booking path.** `rules.md` §2: the plugin does not book. Produce the
  route, the fare range and the operator's own link. A human presses the button.
- **Always caveat the numbers.** Live inventory moves hourly. Give a range, state
  the date and time you pulled it, and say the fare may be gone. A single precise
  figure presented without a timestamp is the most quietly misleading thing in a
  trip plan.
- **Where results disagree with each other or with the airline**, record both and
  say which you followed, per `rules.md` §5. The airline's own site outranks any
  aggregator.
- **Without a flight server**, use web search and the carriers' own sites, and say
  the pricing is indicative. That is the normal case, not a failure.

---

## 5. The table

| Connector | What voyage does with it | What it never does |
|---|---|---|
| Mail | Searches, date- and destination-bounded, for booking confirmations; extracts reference numbers, times, cancellation deadlines and amounts into the tracker | Reads or mentions unrelated mail; sends, replies, archives or labels; acts without a fresh yes |
| Calendar | Writes anchors, transport legs, check-ins and booking deadlines, in the destination time zone, with addresses and confirmation numbers; updates them in place when the plan changes | Writes one event per stop; uses the home time zone by default; duplicates on replan; writes to a calendar the traveller did not name |
| Flight search | Pulls fare ranges, routing options and date-shift comparisons, timestamped and caveated | Books; presents a live fare as a fixed price; outranks the carrier's own site |
| Maps / weather / any other | Uses it where present for coordinates, transit times and forecasts | Takes a hard dependency on it, or stalls when it is absent |

---

## 6. Before you offer anything

- Have you checked what is actually connected?
- Are you offering at the right moment — after delivery, not during the interview?
- Is the offer one sentence, with the benefit stated concretely?
- If they say no, is that the end of it? It is. Do not ask again next turn.
- If a connector is missing, have you said so once, plainly, and given the manual
  route?
