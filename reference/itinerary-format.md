# voyage — itinerary format

This is the shape of the delivered document. `plan-trip` composes into it,
`audit-itinerary` reads it, `replan-trip` edits it in place.

Read `rules.md` first — the pacing law and the hard refusals are what this format
exists to enforce. Where the two disagree, `rules.md` wins. Every named item here
must already carry its Tier 1 fields from `ledger.md`; this file only says where
they go on the page.

The format has one job: make an unsurvivable day visible before the traveller is
standing in it.

---

## 1. Document shape

In this order, always:

1. **Cover block** — who, when, where, and how many days are actually usable
2. **Shape of the trip** — one paragraph saying what each phase is *for*
3. **Residual failures**, if the ledger left any — at the top, never buried
4. **Day pages** — one per date, in date order
5. **Annexes** — A to H, listed in § 11

Nothing goes between the cover and the shape paragraph. A traveller opening this
on a phone at a gate should know what they booked within two screens.

---

## 2. Cover block

```
# Norway — Oslo, Bergen and the Sognefjord
**Travellers:** Ian and Marit, 2 adults, no mobility constraints
**Dates:** Sat 12 – Sun 20 September 2026 (9 days, 8 nights)
**Usable days:** 6½ — day 1 is transit, day 9 is back-timed to a 14:20 departure
**Bases:** Oslo (3 nights) · Bergen (4 nights) · Flåm (1 night, transit node)
**Pace:** balanced · **Lens:** music and landscape
**Mode:** full agent environment — six research lanes, all run
```

**Usable days is a computed number and it is not optional.** Count nights, subtract
the arrival day, subtract the departure day, subtract half a day for each internal
move over three hours. Show the arithmetic in the line, as above. This is the
single field that stops a traveller believing nine days means nine days.

State the mode. If research ran sequentially and two lanes were dropped, the cover
block says which two.

---

## 3. Shape of the trip

One paragraph, prose, no bullets. It says what each phase is *for* — not what
happens, which is what the day pages are for.

> Oslo is the soft landing and the museum half of the trip: three nights, one of
> them wasted on arrival, with the Munch collection as the reason you are there at
> all. The Bergen Railway on day 4 is deliberately a whole day and not a chore.
> Bergen is four nights because the fjord day needs a spare day behind it, and
> September gives you a one-in-three chance of needing it. Day 8 is empty on
> purpose. Day 9 gets you to Flesland with room to spare.

Write it after the days are built, not before. If you cannot say what a day is
for, that day is padding — cut it or merge it.

---

## 4. Day page anatomy

Every day page opens with the same header. Seven fields, no more:

```
## Day 5 · Wednesday 16 September 2026
**Base:** Bergen · **Day type:** full · **Theme:** Grieg and the Bybanen line south
**Energy:** medium · **Anchor:** Troldhaugen lunchtime recital
**Weather note:** [forecast, checked date, and what it changes]
```

- **Date and weekday together.** Never the date alone. The weekday is what the
  closure check runs against, and a reader who cannot see it cannot check you.
- **Base** — where the bags are that night. On a move day, name both: `Oslo → Bergen`.
- **Day type** — one of `arrival` / `full` / `transit` (a move day) /
  `decompression` / `departure`, per § 8. Stated, not inferred: the type sets the
  anchor count and the template the day is built from, and a reader who can see it
  can tell a deliberately empty day from a thin one.
- **Theme** — one phrase. If you cannot write it, the day has no spine.
- **Energy** — `low` / `medium` / `high`. A budget, not a description. Never two
  consecutive `high` days.
- **Anchor** — named in the header so it is visible without reading the blocks.
- **Weather note** — the seasonal expectation plus the specific consequence.
  "Bergen averages rain on roughly two days in three in September; the Ulriken
  cable car is the first thing to go" is useful. "Bring a coat" is not.

Then the day runs as time blocks — morning, afternoon, evening — with real clock
times on anything timed and no clock times at all on anything that is deliberately
loose. A block with an invented precision is worse than a block with none.

The day closes on **two totals, stated separately**:

- **Day total** — transit, queue and visit, against the hours actually available
  between the first start and the last finish, with the arithmetic shown.
- **Walking total** — the day's time on foot, buffers included, summed from the
  walking legs on the page. It is not a share of the day total and it cannot be
  read out of it. This is the figure a stated walking limit is scored against in
  `ledger.md` Tier 3, so a day that does not carry it cannot be checked, and it is
  the number the render puts in the day-card header.

---

## 5. The item format

Every named thing on a day page — venue, meal, walk, boat, funicular — carries all
of these. An item that cannot carry them does not go on the page; see `ledger.md`,
Tier 1.

| Field | Rule |
|---|---|
| Name | The actual name, in local form where that is what a taxi driver reads |
| Why this one | One line, tied to something the traveller said. Not a description of the place |
| Address | Street address or coordinates. Always |
| Official URL | The venue's or operator's own site. Never a reseller or aggregator |
| Time | Four numbers: `transit raw + buffer · queue · visit` and the block total |
| Price | Per person, range, currency, date seen |
| Booking | State code per `reservations.md` § 3, and the deadline if one exists. A walk-up with nothing to book takes `[N/A]` and no deadline |
| Confidence | `HIGH` / `MEDIUM` per `ledger.md`. `LOW` items are dropped, not hedged |

Rendered:

```
**Troldhaugen — Edvard Grieg's villa and composer's hut** · anchor
Why this one: you said the trip was for the music before you said anything about
fjords. This is the piano, in the room, ten metres from where he wrote it.
Troldhaugvegen 65, 5232 Paradis · https://kodebergen.no/troldhaugen
Time: Bybanen 22 min + 8 buffer · walk 20 min + 7 buffer · 15 queue · 2h00 visit
      → 3h12 door to door
Price: NOK 160–200 pp (seen 2026-08-01) · Booking: `[OPEN]` — recital seats are
      capped — book by Fri 4 Sep 2026
Confidence: HIGH · evidence 2026 · hours confirm before you go, link above
```

**The "why this one" line is load-bearing.** It is the difference between a plan
and a search result. If the line would read the same for any traveller, delete the
item and find one where it would not.

**Never blend the four time numbers.** `2 hours at Troldhaugen` hides forty-five
minutes of Bybanen and twenty of walking uphill from Hop. Show the raw estimate
and the buffer separately so the traveller can judge the padding themselves; map
apps run 30–40% optimistic and a party of four runs worse than a party of two.

---

## 6. The anchor

**Exactly one per day.** The anchor is the timed, ticketed, marquee thing the day
is built around. Everything else on the page is arranged relative to it. It
qualifies if it is timed, ticketed or expensively weather-dependent; if it runs
over ninety minutes door to door; and if missing it would be the thing the
traveller mentions on the flight home.

A second anchor is allowed only where the two are geographically adjacent and one
runs under ninety minutes — a stave church twelve minutes back down the same tram
line, not a second museum across the city.

**Every anchor carries three named backups** — one for each of the three ways a
day fails, per `rules.md` § 3: the weather turns, the party runs out of road, the
door is shut. Label them `weather`, `stamina` and `closed-door`, in those words,
because that is the set `ledger.md` Tier 2 counts and the render lays out. Each is
a real place, verified to the same standard as the anchor itself, and each carries
its travel time *from where the traveller will actually be standing* when the plan
fails — not from the hotel.

```
Backup · weather: Fantoft stavkyrkje is outdoors and pointless in horizontal rain.
  Swap to KODE 3 (Rasmus Meyer collection) — Bybanen back to Nonneseter, 18 min,
  covered, and the Munch rooms are the reason you came to Bergen anyway.
Backup · stamina: drop the afternoon entirely. Bybanen to Byparken, 25 min, hotel,
  and move dinner from 20:00 to 18:30.
Backup · closed-door: if the recital is cancelled the house and grounds still
  open — or Siljustøl, Harald Sæverud's house, 15 min further out on the line.
```

"Find something indoors" is not a backup. If you cannot name it, you have not done
the work. Three is the floor and the ceiling: a day that names two has left one of
the three failure modes unanswered, and `ledger.md` Tier 2 will fail it. The
evening's no-reservation meal fallback (§ 7) is a fourth named thing, counted
separately from the anchor's three.

---

## 7. Meals

**Every meal on every day is accounted for.** Booked, planned, or explicitly left
free. A day page with a silent gap where dinner should be is an incomplete day and
the ledger will fail it.

Three states, and the page says which:

- **Booked** — named place, deadline, cancellation terms, state code
- **Planned** — named place, walk-in expectation, and the hour that expectation
  holds until ("walk-in until about 18:30 on a weeknight, per the venue's own page")
- **Free** — said out loud. "Lunch deliberately unplanned — you will be in the
  Fisketorget and you should eat whatever looks right." Silence is not freedom.

Lead times go on the meal line, not only in the tracker: a traveller reading day 6
should see that the table needed booking in June without turning to Annex A.

**One no-reservation fallback per day**, named, near the evening's location, chosen
for being reliably open rather than for being good. This is the line that saves a
Tuesday in a small town.

---

## 8. Day-type variants

Each type has its own rules and its own header. Do not apply the full-day template
to a day that is not a full day.

| Type | Anchors | Rules |
|---|---|---|
| Arrival | **0** | Transfer, bag drop, one unhurried orienting walk, early dinner within walking distance, bed at local bedtime. No timed tickets. Not negotiable |
| Full | 1 | One or two low-commitment secondaries, one loose evening, one clustered geography |
| Move / transit | 1 | Morning **or** evening, never both. The travel block is stated as a duration with its own buffer |
| Decompression | 0–1 | Every four or five active days, and always after a long internal flight, an overnight train or a multi-day walk. Name it as deliberate on the page |
| Departure | 0 | Back-timed from the cutoff |

**Arrival day.** The header still carries a theme — "land soft, walk the harbour" —
so the page does not read as an omission. Users ask for a ticket on arrival day
more than anything else on the pushback table. Argue once, then record the
trade-off on the page if they insist.

**Move days.** The travel block gets the four-number treatment like anything else,
and the day is honest that it is half a day: `Bergensbanen, Oslo S 08:25 → Bergen
14:59, 6h34 + 45 min buffer either end`. A move day with a morning museum and an
evening concert is the commonest way a generated itinerary becomes impossible.

**Decompression days.** Write what the day is protecting — "this is what makes days
6 to 9 work" belongs on the page. Otherwise the traveller deletes it.

**Departure day.** Compose it backwards and show the arithmetic:

```
Flight SK4128 BGO → LHR, departs 14:20
− 3h00 international check-in cutoff        → 11:20 at the desk
− 30 min Flybussen Bergen sentrum → Flesland → 10:50 boarding the bus
− 20 min buffer                              → 10:30 leaving the hotel
Available morning: breakfast, and one walk that ends by 10:00.
```

Three hours for an international departure, two for a domestic one — `rules.md`
§ 3. Use the higher number when you are unsure which the flight is.

Never schedule the last activity to end at the same time the flight leaves. The
last item on a departure day ends at least thirty minutes before the leave-hotel
time.

---

## 9. Hours — confirm before you go

Opening hours are never written as a fact. They are written as a field the
traveller closes themselves, with the official link beside them.

On the page:

```
Hours: confirm before you go → https://kodebergen.no/troldhaugen
       (last checked 2026-08-01; the recital season and the closing time both move
       between summer and winter timetables)
```

Where the check matters more than usual — a Monday in a closure-heavy city, a
seasonal operator, a weekly closing day falling inside the trip — add the specific
worry: `Monday 14 Sept: closed Mondays on the current published schedule. This day
is built outdoors for that reason. Re-check a week out.`

Never write "open 10:00–17:00" and never write "usually open on Sundays". A model
put a museum's closing day on the wrong weekday and sent someone to a market that
shut in 2018. This convention is the countermeasure and it applies to every venue
on every page.

---

## 10. Cross-references

A day page does not restate the tracker or the budget. It points.

- Anything with a booking deadline carries `→ Annex A #7` beside its state code
- Anything costing more than the day's incidentals carries `→ Annex B` and its line
- Any day carrying an accepted trade-off from Phase 2 pushback carries a one-line
  note and `→ Annex H` for the constraint it touches

The tracker is the single source of truth for deadlines and the budget sheet is
the single source of truth for money. When a date changes, it changes in the
annex and the day page follows. Two copies of a deadline is one copy too many.

---

## 11. Annexes

| | Annex | Contains | Spec |
|---|---|---|---|
| A | Reservations tracker | Every advance action, sorted earliest deadline first | `reservations.md` |
| B | Budget summary | Per-person totals, per-day burn, what is and is not included | `budget-sheet.md` |
| C | Packing pointer | A pointer to `pack-for-trip`, plus the three trip-specific items the itinerary implies — waterproof shell, dry bag, cash for the one cash-only place | — |
| D | Practicalities | Payment norms, tipping, tap water, plugs, transport ticketing, language, opening-hours culture, the local emergency numbers |  — |
| E | Emergency page | Local emergency numbers, the traveller's insurer and policy number, nearest hospital to each base, consulate, lost-card numbers | — |
| F | Risk watchlist result | Every line of `rules.md` § 9 answered, including the ones that come back clean | `rules.md` |
| G | Bibliography | Every source, with the date checked | `research-method.md` |
| H | Constraint ledger | Every stated constraint, `PASS`/`FAIL`, with evidence | `ledger.md` |

**Annex E repeats.** In the rendered HTML and the printable PDF, the emergency
block appears in the footer of every page, not once at the back. The traveller who
needs it is not in a position to page to the end of a PDF.

**Annex F reports clean checks explicitly.** "No public holidays fall inside these
dates" is a result. Silence is indistinguishable from a check that never ran.

---

## 12. Worked example — one complete day page

Illustrative of the *format*. In a real document every field carries live evidence
gathered in Phase 3, and every price and time is one you actually saw.

---

### Day 5 · Wednesday 16 September 2026

**Base:** Bergen · **Day type:** full · **Theme:** Grieg and the Bybanen line south
**Energy:** medium · **Anchor:** Troldhaugen lunchtime recital
**Weather note:** Bergen averages rain on roughly two days in three in September.
This day is built to survive it — one outdoor item, and it is the droppable one.
Forecast checked 2026-08-01 for climate normals; re-check 48h out.

**Weekday check:** Wednesday. Nothing on this page closes on Wednesdays on the
current published schedules. Hours still confirm-before-you-go, links below.

---

**08:00 · Breakfast — hotel** · included in the room rate → Annex B
Planned, not booked. Out of the door by 09:00.

**09:05 · Fløibanen funicular to Fløyen** · secondary, low commitment
Why this one: you wanted the shape of the city in your head before spending a day
outside it, and this is eight minutes of doing that instead of an hour of walking.
Vetrlidsallmenningen 23A, 5014 Bergen · https://www.floyen.no
Time: walk 9 min + 4 buffer · 15 queue · 8 min up · 45 at the top → 1h21, ends 10:26
Price: NOK 180–220 pp return (seen 2026-08-01) · Booking: `[N/A]`, walk-up
Confidence: HIGH · evidence 2026 · Hours: confirm before you go, link above
Walk *down* to Bryggen — 35 min + 12 buffer, gravel path, fine in rain, and it
lands you at the Bybanen stop you need next at 11:13.

**11:20 · Bybanen line 1, Byparken → Hop** · transit
22 min + 8 buffer, Hop at 11:50. Ticket via the Skyss app, buy before boarding →
https://www.skyss.no. Then 20 min + 7 buffer uphill to the villa, at the door 12:17.

**12:30 · ANCHOR — Troldhaugen, Edvard Grieg's villa, and the lunchtime recital**
Why this one: you said the trip was for the music before you mentioned a single
fjord. This is the Steinway, in the composer's hut, ten metres above the water he
looked at while writing. The recital hall was built into the hillside so the stage
window frames the hut — it is the only concert venue in the country arranged
around a view of the man who wrote the programme.
Troldhaugvegen 65, 5232 Paradis · https://kodebergen.no/troldhaugen
Time: 15 queue · 2h00 visit and recital → 2h15 on site (12:30–14:45), 3h12 door
to door with the 57 min of Bybanen and walking above
Price: NOK 160–200 pp house and grounds; recital ticket separate, NOK 250–300 pp
(seen 2026-08-01) · → Annex B, line 14
Booking: `[OPEN]`. Recital seating is capped and the season timetable changes
in September — **book by Friday 4 September 2026** → Annex A #6
Confidence: HIGH · evidence 2026 · address confirmed
Hours and recital times: confirm before you go → https://kodebergen.no/troldhaugen

> **Backup · weather:** the grounds are the weak point, not the house. In real rain
> do the villa and the hut, skip the garden, and go straight on to KODE 3 (Rasmus
> Meyer collection) — from the villa, 20 min walk down to Hop, Bybanen Hop →
> Nonneseter 20 min, then 6 min covered walk. Roughly 50 min door to door, and
> indoors from the platform onwards.
> **Backup · stamina:** from the villa, 20 min down to Hop, then Bybanen Hop →
> Byparken 22 min + 8 buffer — about 50 min to the hotel door, and the afternoon
> is deleted. Move dinner from 20:00 to 18:30 and eat early.
> **Backup · closed-door:** if the recital is off, Siljustøl — Harald Sæverud's
> house — is 15 min further out on the same line and open the same afternoons.
> https://kodebergen.no/siljustol

**14:45 · Walk back down to Hop** · transit
20 min + 7 buffer, on the platform at 15:12. Same path as the climb, reversed, and
timed at the same 20 min rather than shaded down for the gradient — map apps run
optimistic downhill as well. The 53 min before the next block is uncommitted on
purpose: that is where a coffee at the stop or ten more minutes in the grounds
comes from, not out of the recital.

**16:05 · Fantoft stavkyrkje** · secondary, and the first thing to cut
Why this one: it is a twelve-minute stop on the line you are already on, and after
three hours of nineteenth-century domestic interiors it is useful to stand in
something that is pretending to be from 1150. Reconstructed after the 1992 fire —
worth knowing before you arrive rather than after.
Fantoftvegen 38, 5075 Bergen · https://www.fantoftstavkirke.com
Time: Bybanen 6 min + 3 buffer · walk 12 min + 5 buffer · 0 queue · 30 visit → 56 min
Price: NOK 80–100 pp (seen 2026-08-01) · Booking: `[N/A]`
Confidence: MEDIUM · evidence 2025, no current-year page update found. Flagged in
the ledger; the item is cheap and droppable, which is why it survived at MEDIUM.
Hours: confirm before you go — seasonal, closes for the winter →
https://www.fantoftstavkirke.com

**17:15 · Bybanen back to Byparken** · 18 min + 7 buffer, in at 17:40. Hotel,
9 min walk. Free block 17:50–19:30, deliberately unplanned.

**20:00 · Dinner — Lysverket, Bergen**
Why this one: the kitchen cooks the west-coast catch landed that morning and does
it without the tasting-menu theatre you said you did not want. Ten minutes' walk
from the hotel, in the old power-station building next to KODE.
Rasmus Meyers allé 9, 5015 Bergen · https://www.lysverket.no
Time: walk 10 min + 4 buffer · 2h00 → 2h14
Price: NOK 600–850 pp food, excluding drinks (seen 2026-08-01) · → Annex B, line 22
Booking: `[OPEN]` · **book by Wednesday 2 September 2026** → Annex A #7
Cancellation: confirm terms at booking; some Bergen kitchens hold a card
Confidence: HIGH · evidence 2026
Hours: confirm before you go → https://www.lysverket.no

> **No-reservation fallback:** Pingvinen, Vaskerelven 14, 8 min walk, no bookings
> taken, serves Norwegian home cooking until late. It is the reliable answer, not
> the exciting one, which is the point of a fallback.
> https://www.pingvinen.no

**Day total:** 7h17 of transit, queue and visit inside an 11h window, 08:00–19:00,
before dinner. The arithmetic: 1h21 funicular block + 47 min walk down + 57 min
Bybanen and climb + 2h15 on site at Troldhaugen + 27 min walk back down to Hop +
56 min Fantoft block + 34 min back to the hotel = 437 min. That leaves 3h43 of the
window uncommitted, which is the point.

**Walking total:** 2h34 on foot, buffers included — 13 min to the funicular, 47
down from Fløyen to Bryggen, 27 uphill from Hop to the villa, 27 back down to Hop,
17 at Fantoft, 9 to the hotel, 14 to dinner. Stated separately from the 7h17
because a walking limit is scored against this number and cannot be read out of
the day total: more than a third of this day is on foot, and the return leg down
to Hop is exactly the sort of thing a day total swallows.

**Slack:** measured gross throughout this document — from the end of one timed
entry to the start of the next, transit included, per `ledger.md` Tier 2. Between
the two timed items here, the funicular block ends 10:26 and the recital starts
12:30: **2h04**. Passes the sixty-minute rule. Be honest about what is inside it —
1h44 of that gap is the walk down, the Bybanen and the climb, so the true cushion
is 20 min and it lives in the 8- and 7-minute buffers. That is thin but it is
stated, and a traveller who misses the 11:20 tram can see they have one more.

**Geography:** the hill, then one line out and back. Fløyen → Bryggen → Byparken →
Hop → Fantoft → Byparken. No doubling.

---

## 13. Self-check before a day is done

Run this on every day page. Any `no` is fixed before you move to the next day, not
noted for later.

- [ ] Day type stated in the header, and the day built to that type's template
- [ ] Exactly one anchor — zero on arrival and departure, one on a move day
- [ ] Anchor has three named backups, labelled `weather`, `stamina` and
      `closed-door` — each with travel time from where the traveller will
      actually be
- [ ] Every item has a name, an address, an official URL and a confidence tag
- [ ] Every item has a "why this one" line that could not be said of anywhere else
- [ ] No `LOW` confidence item survives on the page
- [ ] Every transit leg shows raw time and buffer separately
- [ ] Queue and visit are separate numbers from transit
- [ ] Sixty minutes minimum between any two timed entries, measured gross — end of
      the first to the start of the second, transit included (`ledger.md` Tier 2).
      Where the transit eats most of the gap, say so on the page
- [ ] Day total fits the available window, with the arithmetic shown
- [ ] Walking total stated separately, buffers included, ready for the Tier 3
      check against any stated walking limit
- [ ] Geography clusters — the day does not double back across the same water,
      ring road or ridge more than twice
- [ ] Every meal is booked, planned, or explicitly free — no silent gaps
- [ ] A named no-reservation fallback exists for the evening
- [ ] The weekday closure check ran against the *actual weekday*, and is reported
      even when it passes
- [ ] Every set of hours is a confirm-before-you-go field with a live link
- [ ] No entry, visa, vaccination or customs fact appears anywhere on the page
- [ ] Energy level is honest, and does not follow another `high` day
- [ ] Booking deadlines on the page match Annex A exactly
- [ ] The theme line describes something. If it is generic, the day is generic

When the whole document is built, one more pass: at least one free half-day per
three-day stretch, and the emotional peak on day *n-1* or *n-2* — never the last
day, which has no room to retry.
