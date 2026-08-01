---
name: pack-for-trip
description: Build a packing list from the actual forecast and the actual itinerary, roughly 7–10 days before departure. Pulls the real forecast from the national meteorological service for the real dates and the real sequence of places, derives kit from what each day requires — hiking, boats, cold water, dress codes, fine dining, snow — and explains why every unusual item is on the list. Fires on "what should I pack", "packing list", "what to bring", "what's the weather going to be", "do I need a coat", "do I need hiking boots", "how cold will it be", "what should I wear", "help me pack", "am I forgetting anything". Fire on a soft match. Covers documents, medication, adapters, connectivity, and the cabin/hold split.
category: travel
summary: Forecast-driven packing list derived from the itinerary, with a reason attached to every unusual item.
---

# Pack for a trip

This is a separate skill for one reason: **the forecast does not exist when the
trip is planned.** A plan written in January for an April trip can only use climate
normals, which describe an average April nobody actually travels in. Ten days out
there is a real forecast for real dates, and that is what packing is built on.

Run this 7–10 days before departure — earlier and the forecast is noise, later and
there is no time to buy what is missing.

**Read `../../reference/rules.md` first.** The source hierarchy and the hard
refusals apply here, and the refusals bite hardest in the medication section.

---

## Phase 1 — The forecast

Get the forecast from the **national meteorological service of the destination**,
per the source hierarchy: Met Office, Météo-France, met.no, DWD, JMA, NOAA, AEMET.
Not a weather app, not an aggregator, not a search snippet — the national services
publish their own model output and their own warnings, and everything downstream is
a re-render on an unclear update cadence. Pull it for the **actual sequence of
locations**, not for the country: a trip that starts on a coast, spends three
nights inland at altitude and ends in a city has three forecasts, and the mountain
one determines what gets packed. Record, per location and per date block:

| Field | Why it matters |
|---|---|
| Daily high and low | The low is what the evening layer is for; the range is what layering is for |
| Precipitation probability and type | Rain, sleet and snow need different footwear, not different jackets |
| Wind | 40 km/h turns an umbrella into litter and a mild day into a cold one |
| Humidity | Decides whether cotton is comfortable or miserable |
| UV index | Sun protection at altitude and on water, in any season |
| Active warnings | From the met service's own warning feed |

**State the forecast's confidence honestly.** At ten days out you have a trend, not
a promise. Say which days are firm and which are speculation, and give the link so
they can re-check the evening before departure.

**Daylight hours.** Sunrise and sunset for the actual dates and latitude, with what
they mean in practice. Fifteen hours of Norwegian June daylight means a sleep mask.
Seven hours of Edinburgh December means a head torch, and it means the itinerary's
afternoon walk finishes in the dark. Both change the bag.

---

## Phase 2 — Read the itinerary

Packing is derived from the days, not the destination. Read the itinerary — the
plugin's if it exists, whatever the traveller has otherwise — and walk it day by
day, asking of each entry: *what does this require that ordinary clothes do not?*

| In the itinerary | What it forces into the bag |
|---|---|
| Hiking, any grade | Broken-in boots, non-cotton socks, a real waterproof, a small pack, blister plasters |
| Boat, ferry, RIB, wildlife trip | Windproof outer layer, warm hat, dry bag, motion sickness tablets |
| Cold water, swimming, thermal baths | Quick-dry towel, swim shoes, a change of everything; some pools require a cap or slides — the venue's site says which |
| Snow, ice, winter mountains | Traction devices, waterproof gloves, goggles or sunglasses, base layers |
| Religious sites | Covered shoulders and knees, sometimes a headscarf, sometimes socks — check the site's own dress code and link it |
| Fine dining, opera, a wedding | The one smart outfit and the shoes for it; check the venue's stated code |
| Long-haul or overnight transit | Eye mask, earplugs, refillable bottle, a layer that works as a blanket |
| Self-catering, long train days | Cutlery, a proper container, a corkscrew that will fail cabin screening |

Tie each derived item to the day that produces it. That link is what makes the list
persuasive rather than generic, and what lets the traveller drop the item when they
drop the day.

---

## Phase 3 — Layers, driven by the forecast

Layer for the **range you actually pulled**, not for the season. A Mediterranean
October with 8°C nights and a 23°C afternoon needs more layering than a steady
British 12°C week; season-based advice gets that backwards every time.

Work from the forecast's coldest realistic hour — the low, plus wind chill, plus
whatever the itinerary does after dark — and build up. **Base**: wicking, wool or
synthetic; cotton against the skin is fine warm and dry, bad cold and wet. **Mid**:
the insulating layer, on and off through the day, and the one people under-pack.
**Shell**: wind and water, and waterproof and water-resistant are different
products. Then state the arithmetic out loud — *the range is 6–19°C with rain on
three days, so base plus mid plus shell, mid off by mid-morning* — because a
traveller who understands the logic packs better than one handed a list.

**Count outfits against laundry, not against days.** Establish whether the lodging
has a machine, or a launderette near a base with two nights in it. If it does, five
days of clothing covers a fortnight. If it does not, say so — that is the difference
between a cabin bag and a hold bag.

---

## Phase 4 — The fixed sections

### Documents

Passport, and the traveller's own check of its expiry against the destination's
rule — link the official source, do not state the rule. Boarding passes,
accommodation confirmations, booking references, driving licence and any permit the
hire company requires, insurance policy number and the insurer's emergency line, a
card the bank has been told about, local cash if the destination is cash-heavy.
Photograph the lot, store it somewhere reachable offline, leave a copy at home.

### Medication

List by **generic name** — brand names do not travel and a foreign pharmacist will
not recognise them. Carry the **full quantity for the trip plus a few days** in
**original labelled packaging**, with a copy of the **prescription** or a letter
from the prescriber. Split across cabin and hold where quantity allows; anything
critical goes in the cabin, always. A small kit worth carrying regardless:
rehydration salts, painkillers, plasters, antihistamine, plus whatever the
itinerary's activities make likely.

**Do not advise on what is legal to carry into a country.** Ordinary prescription
and over-the-counter medicines are controlled substances elsewhere and people have
been arrested over them. Link the destination's own health ministry or embassy
page and tell the traveller to check their specific medicines themselves. Hard
refusal, per `rules.md` — it holds even when the medicine seems unremarkable.

### Power and connectivity

State the destination's **socket type and voltage**, and how many adapters — one
per traveller is usually wrong, because the bedside is not the only socket. Check
dual-voltage: almost all modern chargers are, hair tools frequently are not, and a
single-voltage dryer on 230V fails loudly. A multi-port USB charger and one long
cable replaces four adapters. Power banks go in the **cabin**, under a watt-hour
limit — link the airline's page for the figure.

Then eSIM or roaming, decided on the numbers rather than by habit. Check what the
traveller's own operator charges — post-Brexit UK roaming in the EU is no longer
reliably free and varies by network. An eSIM is usually cheaper for a week or more;
roaming is simpler for a weekend and keeps the number live for the bank's SMS
codes. Either way, download offline maps and transit apps before leaving.

### Buy there, do not carry

Say what to leave out: sunscreen, toiletries over the liquid limit, bottled water,
umbrellas, beach kit, most of what a supermarket sells — heavy, and available
everywhere. Name the exceptions — a specific prescription, a brand the traveller
depends on, anything genuinely hard to find at the destination.

### Cabin, hold, and what goes on the body

Split the bag on the principle that **the hold bag may not arrive**. In the cabin:
medication, documents, valuables, electronics, power banks, one change of clothes,
anything the first 24 hours cannot happen without.

For liquid limits, sizes and weights, **link the airline's baggage page and the
departure airport's security page**. Do not assert the figures — the 100ml rule is
being replaced unevenly across airports and allowances differ by carrier, fare class
and route, and a stale number gets something confiscated at the gate.

**Wear the bulk on the plane**: boots, heavy coat, thick jumper. Cheapest hold
allowance there is, balanced against comfort — layers that come off beat one heavy
garment that does not.

---

## Output

A checklist, grouped so it can be worked through while packing: **Documents ·
Medication · Electronics · Clothing (by layer) · Footwear · Activity-specific ·
Toiletries · Cabin bag · Wear on the plane · Buy there**.

- **Every unusual item carries one line saying why, naming the day.** "Traction
  spikes — Day 4, the ridge walk above Bergen, forecast low −3°C with rain the day
  before." Ordinary items — socks, chargers — need no justification.
- **Quantities, not just names.** "Socks" is not a packing list.
- Open with a two-line forecast summary and the daylight window, so the traveller
  sees what the list was built from; close with the met service link and an
  instruction to re-check the evening before departure.

Save it alongside the itinerary if that lives as a project document, in a form the
traveller can tick off on a phone.

---

## Failure modes to watch for in yourself

Stop and correct if you catch yourself about to:

- Use climate normals or an aggregator when a real met-service forecast exists
- Produce one forecast for a trip that visits three climates
- Build layers from the season instead of from the pulled temperature range
- List activity kit that no day in the itinerary actually requires
- State a medicine's legal status in the destination country
- Assert a liquid, weight or watt-hour limit instead of linking the airline
- Write a list with no quantities and no reasons

---

## Reference files

- `../../reference/rules.md` — source hierarchy, hard refusals, tone
- `../../reference/ledger.md` — the verification standard for any linked claim
