# voyage — the interactive HTML render

A single self-contained web page holding the whole trip: map, days, stops,
evidence, route links, emergency numbers. It is what the traveller actually opens
on a phone in the street.

**Produced on request only.** `plan-trip` asks; it never renders unprompted. The
render is expensive, and a traveller who wanted a markdown itinerary and a
spreadsheet does not want a third artefact they did not ask for.

The map and the Google Maps URL rules below are adapted from
`theoms/co-work-travel-planner-claude` (MIT). Carry the attribution comment in
every page you generate — it is in the skeleton at the end of this file.

---

## 1. The render is a transformation, not a new document

Same rule as the dossier-to-itinerary boundary in `plan-trip`. **No place, price,
time or claim may appear in the HTML that is not already in the delivered
itinerary.** If a field is missing — no coordinates for a stop, no photo, no
price — render the gap honestly or omit the field. Do not go back to memory to
fill it, and do not search mid-render to patch it. If you find a real gap, fix the
itinerary, re-run the affected ledger checks, then render.

Anything the ledger dropped stays dropped. `LOW`-confidence items do not
reappear here with a shrug.

---

## 2. Hard constraints

These are not preferences. A page that breaks one of them is not delivered.

**One file.** All CSS in a single inline `<style>`, all JS in a single inline
`<script>`. The external requests are the Leaflet CSS/JS from cdnjs, the OSM
tiles, and any photographs — plus, *only* if you take the §12 fallback,
`api.qrserver.com` for the QR images. That fallback is a fourth external origin
and the inline QR encoder is preferred precisely so the list stays at three. If
you do use it, the page must say so where it says which QR method it used. The
page must open from a file:// URL either way.

**No `localStorage`. No `sessionStorage`. Ever.** They are unavailable in the
artefact environment and calling them throws, which kills the rest of your script
with it. Hold every piece of state — theme choice, which cards are open, which day
is current — in plain JS variables. This means state resets on reload. That is the
correct trade-off; do not try to be clever about it.

**Dark and light both, genuinely designed.** `prefers-color-scheme` sets the
default, a manual toggle overrides it for the session. Neither mode is an
inversion of the other: dark mode gets its own surface ramp, its own map
treatment, and its own pin-on-tile contrast check. A page that is only readable in
one of the two is half-finished.

**iPhone, iPad, desktop.** Mobile-first CSS. Every interactive element — toggle,
day header, disclosure, route button — has a hit area of at least 44 × 44 CSS
pixels. Reason through the layout at 375px, 768px and 1440px before you write it,
and make sure nothing overflows horizontally at 375px. Nothing relies on hover.

**Every render is a new version.** Never overwrite. Filename:

```
{trip-slug}-itinerary-v{n}.html      norway-fjords-itinerary-v3.html
```

Increment `n` on every render, including after a trivial fix. The previous file
stays on disk. Print the version and the build date in the page footer so a
traveller holding two files knows which is current.

---

## 3. Page structure, in order

1. Skip link, then header — trip title, dates, night/day count, usable days, base
   cities, theme toggle
2. Any residual ledger `FAIL` lines, in a warning banner. Per `ledger.md` these
   are surfaced at the top, not buried. If the ledger passed clean, omit the
   banner entirely — do not print a green "all good" badge for decoration.
3. Map
4. Day legend
5. Emergency block (see §10 — it stays reachable, not buried at the bottom)
6. Day cards, in date order
7. Footer — version, build date, sources note, the honest statement of what could
   not be verified

---

## 4. Colour

Two schemes, one set of custom properties, swapped at the `:root` level.

### Surfaces and text

| Token | Light | Dark | Use |
|---|---|---|---|
| `--bg` | `#F7F6F3` | `#12141A` | page background |
| `--surface` | `#FFFFFF` | `#1B1E26` | cards |
| `--surface-2` | `#F0EEE9` | `#232833` | nested blocks, disclosures |
| `--border` | `#DDD9D1` | `#2E3440` | hairlines |
| `--text` | `#1C1D20` | `#E8E9EC` | body |
| `--text-dim` | `#5A5C63` | `#A0A4AE` | secondary, meta |
| `--accent` | `#1F6FB2` | `#6FB3E8` | links, buttons |
| `--warn-bg` | `#FCE9E7` | `#3A1D1B` | ledger failure banner |
| `--warn-text` | `#8C1D14` | `#F5B3AC` | ledger failure text |

`--text` on `--bg` clears 15.6:1 in light and 15.2:1 in dark. `--text-dim` on
`--surface` clears 6.7:1 in both. `--accent` on `--surface` clears 5.3:1 and
7.4:1. If you change a value, re-check the pair — do not assume, and do not carry
a stated ratio across from an earlier palette.

### Day pins

Mid-tone and saturated, so they hold against both a pale OSM tile and a darkened
one. Pastels vanish on light tiles; near-blacks vanish on dark ones. These do
neither.

| Day | Hex | Reads as | Numeral | Contrast |
|---|---|---|---|---|
| 1 | `#D7263D` | crimson | `#FFFFFF` | 4.96:1 |
| 2 | `#1B998B` | teal | `#0B0D12` | 5.53:1 |
| 3 | `#2E86AB` | blue | `#0B0D12` | 4.73:1 |
| 4 | `#E8871E` | orange | `#0B0D12` | 7.33:1 |
| 5 | `#6A4C93` | violet | `#FFFFFF` | 6.85:1 |
| 6 | `#4C9F38` | green | `#0B0D12` | 5.85:1 |
| 7 | `#C64191` | magenta | `#FFFFFF` | 4.60:1 |
| 8 | `#B08900` | mustard | `#0B0D12` | 5.95:1 |

Beyond eight days, cycle the palette and lean on the day number in the pin.

**The numeral is not always white.** White 12px bold on the lighter half of this
palette fails badly — `#E8871E` gives 2.65:1, `#B08900` 3.27, `#4C9F38` 3.32,
`#1B998B` 3.51, all under the 4.5:1 that small bold text needs. The rule:

> Compute the day colour's relative luminance. **L ≤ 0.18 → white numerals.
> L > 0.18 → near-black `#0B0D12` numerals.** Whichever you pick must clear
> 4.5:1 against the day colour; if it does not, change the day colour, not the
> threshold.

That threshold splits this palette cleanly: days 1, 5 and 7 are dark enough for
white (L = 0.162, 0.103, 0.178), days 2, 3, 4, 6 and 8 take the near-black
(L = 0.249, 0.206, 0.346, 0.266, 0.271). The numeral colour is a per-day value
carried beside the day colour in the script — never a blanket `color:#fff` on
`.pin`. If you change a day colour, recompute both the luminance and the pairing.

**Every pin carries its day number as text.** Colour alone fails for the ~8% of
men with red–green deficiency — days 1 and 6 are the collision. Use a Leaflet
`divIcon` with the numeral inside, on the day colour, with a 2px outer ring in
`var(--surface)` — white in light, `#1B1E26` in dark. One token, so the ring
follows the theme without a second rule. The ring separates the pin from the
tile; the numeral colour above is what makes the number readable. The number is
what makes the map legible; the colour is the shortcut.

---

## 5. Dark mode and the map

OSM standard tiles are light. Do not ship them raw against a dark page.

Apply the filter to the **tile pane only**:

```css
[data-theme="dark"] .leaflet-tile-pane {
  filter: invert(1) hue-rotate(180deg) brightness(0.92) contrast(0.9);
}
```

Filtering `.leaflet-container` instead inverts your pins, polylines and popups
along with the tiles, which looks like a bug because it is one.

An alternative is swapping the basemap to CARTO `dark_all` — no key needed, but it
is a second tile provider with its own required attribution, and you must switch
the attribution string with it. The filter is the default; use CARTO only if the
filtered tiles read badly for that particular geography.

---

## 6. The map

Leaflet 1.9.4 from cdnjs, OSM tiles, no API key.

- **Filter to plottable stops once, before anything touches the map.** A stop may
  legitimately have no coordinates (§1), and `L.marker([undefined, undefined])`
  throws. Because markers, polylines and `fitBounds` all run inside one `try`, a
  single incomplete stop would otherwise take the entire map down — no pins, no
  routes, no bounds. So derive one list, `PLOTTABLE`, with
  `Number.isFinite(s.lat) && Number.isFinite(s.lng)`, and build every map layer
  from that. This is deliberate, not a silent drop: the excluded stops still
  appear in full in the day list below the map, and the map status line says how
  many were left off.
- `L.map('map')` then `fitBounds` over `PLOTTABLE`, padding `[36, 36]`. Do not
  hardcode a centre and zoom — a hardcoded view is wrong the moment the itinerary
  changes. With one plottable stop `fitBounds` snaps to maximum zoom, so
  `setView` at a readable zoom instead; with none, skip the map layers and say so
  on the status line.
- **`#map` is empty in the markup.** Leaflet appends its panes to the container
  and never clears it, so a "Loading map…" child is not replaced — it sits on top
  of the tiles for the life of the page. Any loading or failure text is a sibling
  element that the script hides on the tile layer's `load` event (§13).
- One numbered `divIcon` marker per stop, coloured by day, with the numeral
  colour taken from the day's pairing in §4 — not a blanket white.
- **The day legend is populated by script** from the same stop data: one swatch
  and label per day that actually appears on the map. An empty legend container
  in the markup is a defect, not decoration.
- One dashed polyline per day, in that day's colour, in visit order. Days are
  drawn as separate polylines so nothing connects the end of day 2 to the start of
  day 3.
- Popup per marker: name (linked to the official site), type, time block, price,
  and a directions link. Keep it short — the day card below holds the detail.
- Clicking a day card header pans and zooms to that day's bounds. Clicking a stop
  row opens its popup.
- Map height: `320px` at 375px wide, `420px` at 768px, `520px` at 1440px.
- Stops with no coordinates are still listed in the day card, in sequence, with
  everything §8 requires; they simply have no pin. Say so in the card rather than
  silently dropping them, and never let a missing coordinate cost the traveller
  the stop.

---

## 7. Day cards

A `<details>` element per day, or a button-plus-region pair with proper `aria`.
Header shows: day number, date and weekday, base city, the day type, the day's
theme, the energy level, the day's anchor, the day's walking total, and the
weather note. All of them come straight from the day header in
`itinerary-format.md` § 4 — the theme, energy and weather note are what let a
traveller judge a day at a glance without opening it, which is the whole job of a
collapsed card.

**The weather note goes in the summary, not the body.** It is the field that
decides whether the day happens as written, so it cannot sit behind a disclosure
the traveller has to open first. Verbatim from the day header, checked date
included.

**Auto-expand the current day if the trip is in progress.** Compare `new Date()`
against the trip dates in JS at load. Trip not started or already finished: all
cards collapsed, day 1 expanded. Mid-trip: today's card expanded and scrolled to,
the rest collapsed. This is the single feature that makes the page useful in the
field rather than at the planning desk.

Each day card contains, in order:

1. The day's shape in one line — the stated day type and theme, carried across
   from the itinerary header: arrival day, transit day, decompression day
2. The **checks line** — a compact badge row showing that the two Tier 2 day
   checks ran and what they returned: `Weekday closures · Wednesday: clear` and
   `Geography · one line out and back, no doubling`. Both are checked in
   `ledger.md` Tier 2 and specced in `itinerary-format.md`, and neither is
   visible anywhere else on the page. A silent pass is indistinguishable from a
   check that never happened, so the render says it out loud. Keep it to one line
   per check, in dim meta text — it is reassurance, not a headline. Where a check
   carries a specific worry, that worry is the line: `Weekday closures · Monday:
   KODE 3 shut, day built outdoors for that reason`
3. Stops, in sequence
4. The day's named backups — all four that `ledger.md` Tier 2 requires, each
   labelled by the failure it answers: **weather** (indoor or sheltered
   substitute), **stamina** (what to cut, and where they sit down instead),
   **closed door** (substitute for a strike, renovation, private hire or missed
   closure day), and the **no-reservation meal** fallback. Each travel time is
   measured from where the traveller will actually be standing when the plan
   fails — not from the anchor and not from the hotel (`rules.md` § 3) — and the
   card says which place it is measured from. If any of the four is missing the
   itinerary is incomplete, not the render — go back and fix the itinerary rather
   than rendering three and hoping
5. The route button and QR

---

## 8. The stop block

Every stop carries, and the render shows:

- **Name**, linking to the official site. Never a reseller.
- **Type** and **time block** — `10:00–12:15`
- **The four-part time breakdown**, shown as four numbers, never blended:
  `transit 22 min + buffer 10 min + queue 25 min + visit 90 min = 2h27`.
  This is `ledger.md` Tier 2 rendered; the traveller must be able to see which
  number is the guess.
- **Price** — per person, with currency, as quoted
- **The "why this one" line**, carried across verbatim from the itinerary. Per
  `itinerary-format.md` it is load-bearing; do not paraphrase it shorter to fit
  the card.
- **Confirm hours** — a link to the official hours page labelled
  "Hours — confirm before you go". Never render hours as a bare fact. `rules.md`
  §2 makes this absolute, and the render is the place it is most tempting to
  break, because a time in a box looks authoritative.
- **Directions** — Google Maps deep link to that single stop
- **Sources** — a collapsed `<details>` disclosure carrying the Tier 1 evidence:
  street address (local script where that is what a taxi driver reads), the
  primary-source URL, the evidence-of-operation year, the confidence grade, the
  date the price was seen, and the booking deadline where one exists.

The disclosure is collapsed by default and it is not optional. It is what makes
the page auditable by the person relying on it.

---

## 9. Photographs

Where research sourced a real photograph, use it. No AI-generated substitutes,
ever.

- `loading="lazy"` and explicit `width`/`height` so the layout does not jump
- Attribution line beneath every image — photographer or source, and the licence
  where one is stated
- `onerror` handler that replaces the failed image with the stop's map link and a
  short text label. A broken-image icon on a phone with bad signal is worse than
  no image.
- `alt` text describing the place, not the word "photo"
- No image is load-bearing. Every stop must read completely with all images
  failed.
- **Where research sourced no photograph, omit the `<img>` and its credit line
  altogether.** Do not ship the skeleton's image element with an unfilled
  placeholder in `src`: it resolves to nothing, 404s, and fires the fallback for a
  photograph that never existed. §1 applies — render the gap or omit the field.

---

## 10. The emergency block

Always reachable, never behind a disclosure. On mobile, a fixed bar at the foot of
the viewport that expands on tap; on desktop, a card pinned near the top.

Contents:

- Local emergency number for the destination — and where it differs by service or
  region, all of them
- The traveller's embassy or consulate: address, phone, out-of-hours line
- Insurer name, policy number and the 24-hour assistance line
- Every lodging's phone number, in stay order
- The destination's non-emergency medical line where one exists

Every phone number is a `tel:` link. Someone using this is not going to type it.

---

## 11. Google Maps route links

Adapted from the theoms template (MIT). No API key.

```
https://www.google.com/maps/dir/?api=1
  &origin=ORIGIN
  &destination=DESTINATION
  &waypoints=STOP1|STOP2|STOP3
  &travelmode=walking
```

Rules:

- `api=1` is required. Without it the URL falls into a legacy format that behaves
  differently across platforms.
- URL-encode every address. Spaces to `%20`. The waypoint separator `|` encodes to
  `%7C`.
- Coordinates beat addresses. `59.9139,10.7522`, no space after the comma. Names
  are ambiguous; coordinates are not, and they keep the URL short enough to fit in
  a QR code.
- **Nine waypoints maximum**, plus origin and destination — eleven stops in one
  link. Beyond that Google silently drops the overflow, which is the dangerous
  failure because the link still works and quietly omits stops.
- **To split:** chunk the day into segments of at most nine waypoints. The last
  stop of a segment becomes the origin of the next. Label the buttons
  "Day 4 route — part 1 of 2" and "part 2 of 2". Never emit a truncated single
  link.
- `travelmode` matches how the day is actually done: `walking`, `transit`,
  `driving`, `bicycling`.
- **`travelmode=transit` does not support waypoints.** Google routes origin to
  destination and drops the rest. For a transit day, emit one link per leg instead
  of one route link, and say in the card that transit routing is leg by leg.
- Do not use `dir_action=navigate`. It starts turn-by-turn immediately on tap,
  which is hostile when someone is just looking at the plan.

---

## 12. QR codes

One per day, beside the route button, so the page on a laptop gets onto the phone
in the street.

**Preferred: an inline JS encoder.** Embed a minimal QR encoder in the page script
and render to inline SVG. No external dependency, works offline, works in a
sandbox with no network.

**Fallback: a public QR image service.** Note what this costs: it is a fourth
external origin, beyond the three §2 allows, so it only applies when you have
genuinely not embedded an encoder — and the page must declare it.

```html
<img src="https://api.qrserver.com/v1/create-qr-code/?size=180x180&data=ENCODED_URL"
     alt="QR code for the day 3 route" loading="lazy">
```

Free, no key, but it is a third-party request that can be blocked or can
disappear.

**Final fallback: the link alone.** A visible, tappable, copyable URL written into
the QR container itself. This one is not optional and it is not conditional: if no
inline encoder is embedded and `ALLOW_EXTERNAL_QR` is false — which is the default,
and therefore the common case — the first two branches never run, and a container
that only ever holds a QR ships empty. §17 forbids that. So the chain ends in an
unconditional `else` that writes the route URL as text, and the external branch
falls back to the same text when the image fails to load rather than deleting the
container. Whatever happens, the container holds something.

Say plainly in the page which of the three you used. "QR generated inline" or
"QR served by api.qrserver.com — needs a connection" or "QR unavailable, use the
link". A traveller who knows the QR depends on a network will not stand in a
tunnel wondering why it is blank.

Keep the encoded URL short — use coordinates, not names. Past roughly 300
characters the code gets dense enough that a phone camera struggles at the sizes
that fit on a page.

---

## 13. Offline

Assume the tiles fail and the photographs fail. The page must still be a complete,
readable itinerary as text.

- The map gets a styled status line, not a grey void: "Map tiles unavailable —
  every stop below has an address and a directions link." Put it **beside** the
  map container, not inside it — Leaflet appends to `#map` without clearing what
  is already there, so a placeholder child stays visible on top of the tiles.
  Hide the line on the tile layer's `load` event and rewrite it to the failure
  text if the tiles have not arrived within a few seconds. The one case where it
  stays visible after a clean load is the §6 coordinate exclusion: if any stop
  could not be pinned, the line says how many and points at the day cards.
- Images fall back per §9.
- No content is injected by fetch. Everything is in the file at build time.
- Addresses and phone numbers are text in the document, not only inside popups —
  a popup you cannot open is a dead end.

---

## 14. Accessibility

- Semantic structure: `header`, `nav`, `main`, `section`, `footer`. One `h1`,
  days as `h2`, stops as `h3`. No heading levels skipped.
- Skip link to `main` as the first focusable element.
- `<details>`/`<summary>` for disclosures — free keyboard behaviour. Custom
  collapsibles need `aria-expanded` and `aria-controls`, and must respond to Enter
  and Space.
- Visible focus ring on every interactive element, in both schemes. Do not remove
  the default outline without replacing it.
- Theme toggle is a `<button>` with `aria-pressed`, announcing "dark" or "light".
- Contrast per §4 in both modes, including the pin numerals — which follow the
  luminance rule there and are near-black `#0B0D12` on the light day colours,
  white only on the dark ones. Every pairing clears 4.5:1; a blanket white
  numeral does not.
- `prefers-reduced-motion: reduce` disables card animations and map fly-to; use an
  instant `setView` instead.
- Map is decorative-plus: everything on it exists as text below it.

---

## 15. Skeleton

Structure and the custom-property setup. Fill it from the itinerary; do not treat
the styling here as finished design.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="color-scheme" content="light dark">
<title>[TRIP_TITLE] — [DATES]</title>
<!--
  Map, day-colour and Google Maps directions-URL approach adapted from
  theoms/co-work-travel-planner-claude (MIT Licence, Copyright (c) 2026 Omri Ben).
  Generated by voyage. Version [N], built [BUILD_DATE].
-->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.css">
<style>
  :root {
    --bg:#F7F6F3; --surface:#fff; --surface-2:#F0EEE9; --border:#DDD9D1;
    --text:#1C1D20; --text-dim:#5A5C63; --accent:#1F6FB2;
    --warn-bg:#FCE9E7; --warn-text:#8C1D14;
    --d1:#D7263D; --d2:#1B998B; --d3:#2E86AB; --d4:#E8871E;
    --d5:#6A4C93; --d6:#4C9F38; --d7:#C64191; --d8:#B08900;
    --tap:44px; --radius:12px;
  }
  @media (prefers-color-scheme: dark) {
    :root:not([data-theme="light"]) {
      --bg:#12141A; --surface:#1B1E26; --surface-2:#232833; --border:#2E3440;
      --text:#E8E9EC; --text-dim:#A0A4AE; --accent:#6FB3E8;
      --warn-bg:#3A1D1B; --warn-text:#F5B3AC;
    }
  }
  /* Manual override — the toggle sets data-theme; state is a JS variable only. */
  :root[data-theme="dark"] {
    --bg:#12141A; --surface:#1B1E26; --surface-2:#232833; --border:#2E3440;
    --text:#E8E9EC; --text-dim:#A0A4AE; --accent:#6FB3E8;
    --warn-bg:#3A1D1B; --warn-text:#F5B3AC;
  }
  /* Tiles are darkened on the tile pane only, never the map container.
     One selector is enough: the script always sets data-theme, and without
     the script there is no Leaflet and so no tiles to darken. */
  :root[data-theme="dark"] .leaflet-tile-pane {
    filter:invert(1) hue-rotate(180deg) brightness(.92) contrast(.9);
  }

  *,*::before,*::after { box-sizing:border-box; }
  body { margin:0; background:var(--bg); color:var(--text); -webkit-text-size-adjust:100%;
         font:16px/1.5 -apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif; }
  a { color:var(--accent); }
  :focus-visible { outline:3px solid var(--accent); outline-offset:2px; }
  .skip { position:absolute; left:-9999px; }
  .skip:focus { left:8px; top:8px; padding:12px; background:var(--surface); z-index:9999; }
  /* inline-flex, not the default inline: min-height does nothing to an inline
     <a>, and .btn goes on links — the route button and the photo fallback.
     Without this the route button measures 39px and misses §2's 44px. */
  button,.btn { min-height:var(--tap); min-width:var(--tap); font:inherit; padding:10px 16px;
                display:inline-flex; align-items:center; justify-content:center;
                border:1px solid var(--border); border-radius:var(--radius);
                background:var(--surface); color:var(--text); cursor:pointer;
                text-decoration:none; }

  .wrap { max-width:100%; padding:16px; }
  #map { height:320px; border-radius:var(--radius); background:var(--surface-2); }
  /* The status line is a SIBLING of #map, never a child. Leaflet does not clear
     its container's existing children, so a "Loading map…" div inside #map sits
     on top of the tiles for the life of the page. */
  .map-status { color:var(--text-dim); font-size:.875rem; margin:8px 2px 0; }
  .map-status[hidden] { display:none; }
  .legend { display:flex; flex-wrap:wrap; gap:6px 14px; margin:8px 0; padding:0; list-style:none; }
  .legend li { display:inline-flex; align-items:center; gap:6px; }
  .legend i { width:14px; height:14px; border-radius:50%; flex:none; }
  /* No blanket colour here — the numeral colour is per day, set from DAY_INK,
     because white fails on the light half of the palette (§4). */
  .pin { display:grid; place-items:center; width:26px; height:26px; border-radius:50%;
         font-size:12px; font-weight:700; border:2px solid var(--surface); }

  .day { background:var(--surface); border:1px solid var(--border);
         border-radius:var(--radius); margin:12px 0; }
  .day > summary { min-height:var(--tap); padding:14px 16px; cursor:pointer;
                   list-style:none; font-weight:600; }
  /* Summary metadata stacks — the weather note lives here, on the collapsed
     card, because it is what decides whether the day happens as written (§7). */
  .day > summary .meta { display:block; font-weight:400; margin-top:2px; }
  .stop { border-top:1px solid var(--border); padding:14px 16px; }
  .stop h3 { margin:0 0 4px; font-size:1rem; }
  .stop img { width:100%; height:auto; border-radius:8px; }
  .meta,.credit { color:var(--text-dim); font-size:.875rem; }
  .credit { font-size:.75rem; }
  .timebreak { font-variant-numeric:tabular-nums; font-size:.875rem; color:var(--text-dim); }
  /* Tier 2 checks, said out loud — a silent pass reads as a check that never ran. */
  .checks { display:flex; flex-wrap:wrap; gap:6px; margin:10px 16px 0; padding:0; list-style:none; }
  .checks li { background:var(--surface-2); border:1px solid var(--border); border-radius:8px;
               padding:4px 10px; font-size:.8125rem; color:var(--text-dim); }
  .sources { background:var(--surface-2); border-radius:8px; padding:8px 12px;
             margin-top:10px; font-size:.85rem; }
  /* The sources disclosure is an interactive element too — a bare <summary> is
     one line tall and fails the 44px rule the day header already meets. */
  .sources > summary { min-height:var(--tap); display:flex; align-items:center;
                       cursor:pointer; }
  /* The QR box always ends up holding something — an SVG, an image, or the
     route URL as tappable text (§12). It is never left empty. */
  .qr { margin:10px 0; }
  .qr svg, .qr img { display:block; width:180px; height:180px; }
  .qr-link { display:inline-flex; align-items:center; min-height:var(--tap);
             font-size:.8125rem; word-break:break-all; }
  .ledger-fail { background:var(--warn-bg); color:var(--warn-text);
                 border-radius:var(--radius); padding:14px 16px; }
  .emergency { position:sticky; bottom:0; background:var(--surface);
               border-top:1px solid var(--border); padding:12px 16px; }
  .emergency a { min-height:var(--tap); display:inline-flex; align-items:center; }

  @media (min-width:768px) { #map { height:420px; } .wrap { padding:24px 32px; } }
  @media (min-width:1200px) {
    #map { height:520px; }
    .wrap { max-width:1120px; margin:0 auto; }
    .emergency { position:static; margin:16px 0;
                 border:1px solid var(--border); border-radius:var(--radius); }
  }
  @media (prefers-reduced-motion: reduce) {
    * { animation:none !important; transition:none !important; }
  }
</style>
<script>
// Defined in the head: an image can fail before the end-of-body script parses,
// and an onerror that calls an undefined function leaves a broken-image icon.
function mapFallback(url, name) {
  const a = document.createElement('a');
  a.href = url; a.className = 'btn';
  a.textContent = 'View ' + name + ' on the map';
  return a;
}
</script>
</head>
<body>
<a class="skip" href="#main">Skip to itinerary</a>

<header class="wrap">
  <h1>[TRIP_TITLE]</h1>
  <p class="meta">[DATES] · [N] nights · [M] usable days · [BASE_CITIES]</p>
  <button id="theme" aria-pressed="false">Dark mode</button>
</header>

<main id="main" class="wrap">

  <!-- Only if the ledger left residual failures. Omit entirely when clean. -->
  <section class="ledger-fail" aria-label="Outstanding issues">
    <h2>Known issues with this plan</h2>
    <ul><li>[FAIL_LINE]</li></ul>
  </section>

  <section aria-label="Trip map">
    <!-- #map stays empty in the markup. Leaflet appends to its container without
         clearing it, so any placeholder put here survives on top of the tiles. -->
    <div id="map"></div>
    <p id="map-status" class="map-status" role="status">Loading map…</p>
    <ul id="legend" class="legend meta"></ul>
  </section>

  <section class="emergency" aria-label="Emergency contacts">
    <strong>Emergency</strong>
    <a href="tel:[EMERGENCY_NUMBER]">[COUNTRY] emergency — [EMERGENCY_NUMBER]</a>
    <a href="tel:[EMBASSY_PHONE]">[EMBASSY_NAME]</a>
    <a href="tel:[INSURER_PHONE]">[INSURER] — policy [POLICY_NO]</a>
    <a href="tel:[LODGING_PHONE]">[LODGING_NAME]</a>
  </section>

  <!-- One per day -->
  <details class="day" id="day-1">
    <summary>
      <h2>Day 1 · [WEEKDAY] [DATE] · [BASE_CITY]</h2>
      <span class="meta">[DAY_TYPE] · [THEME] · energy [LOW|MEDIUM|HIGH]</span>
      <span class="meta">Anchor: [ANCHOR] · walking [WALKING_TOTAL]</span>
      <!-- Inside the summary, not below it: the weather note is what decides
           whether the day happens as written, so it has to be legible on a
           collapsed card (§7). -->
      <span class="meta">Weather: [WEATHER_NOTE] (checked [DATE])</span>
    </summary>

    <!-- Tier 2 checks. Neither result appears anywhere else on the page. -->
    <ul class="checks">
      <li>Weekday closures · [WEEKDAY]: [RESULT]</li>
      <li>Geography · [RESULT]</li>
    </ul>

    <div class="stop">
      <h3><a href="[OFFICIAL_URL]">[STOP_NAME]</a></h3>
      <p class="meta">[TYPE] · [TIME_BLOCK] · [PRICE_PP]</p>
      <p class="timebreak">transit [T] + buffer [B] + queue [Q] + visit [V] = [TOTAL]</p>
      <p>[WHY_THIS_ONE]</p>
      <!-- Only where the stop has no coordinates. It keeps its place in the day,
           it simply has no pin (§6). -->
      <p class="meta">Not pinned on the map — no coordinates for this stop. The
        address and directions link below still work.</p>
      <!-- Photo and credit: omit both entirely where research found no
           photograph. Do not ship the element with the placeholder in src (§9). -->
      <img src="[PHOTO_URL]" alt="[DESCRIPTION]" loading="lazy"
           width="800" height="533"
           onerror="this.replaceWith(mapFallback('[MAPS_PIN_URL]','[STOP_NAME]'))">
      <p class="credit">Photo: [CREDIT]</p>
      <p>
        <a href="[HOURS_URL]">Hours — confirm before you go</a> ·
        <a href="[MAPS_PIN_URL]">Directions</a>
      </p>
      <details class="sources">
        <summary>Sources</summary>
        <p>[ADDRESS_LOCAL_SCRIPT]</p>
        <p>Primary source: <a href="[PRIMARY_URL]">[PRIMARY_URL]</a></p>
        <p>Evidence of operation: [YEAR] · Confidence: [HIGH|MEDIUM]</p>
        <p>Price seen [DATE] · Book by [DEADLINE]</p>
      </details>
    </div>

    <div class="stop">
      <h3>Backups</h3>
      <!-- Travel times run from where the traveller will be standing when the
           plan fails, not from the anchor or the hotel. Name that place. -->
      <p class="meta">Weather (turns): [NAMED_ALTERNATIVE] · [TRAVEL_TIME] from [FROM_WHERE]</p>
      <p class="meta">Stamina (out of road): cut [WHAT_TO_CUT] → [NAMED_PLACE_TO_SIT]</p>
      <p class="meta">Closed door: [NAMED_ALTERNATIVE] · [TRAVEL_TIME] from [FROM_WHERE]</p>
      <p class="meta">No-booking meal: [NAMED_FALLBACK]</p>
    </div>

    <div class="stop">
      <a class="btn" href="[GOOGLE_MAPS_DAY_URL]">Open day 1 route in Google Maps</a>
      <!-- Populated by renderQR() below. Every id here must be in DAY_ROUTES. -->
      <div id="qr-1" class="qr"></div>
      <!-- Default text covers the script-disabled case; buildQRs() overwrites it
           with the method that actually ran. -->
      <p class="meta qr-method">The route link above works without the QR.</p>
    </div>
  </details>

  <footer class="meta">
    <p>voyage · version [N] · built [BUILD_DATE]</p>
    <p>Could not verify: [HONEST_LIST]</p>
  </footer>
</main>

<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.js"></script>
<script>
// State lives here and nowhere else. No localStorage, no sessionStorage.
let theme = window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';

const DAY_COLOURS = ['#D7263D','#1B998B','#2E86AB','#E8871E',
                     '#6A4C93','#4C9F38','#C64191','#B08900'];
// Numeral colour per day, paired with the above by the §4 luminance rule
// (L <= 0.18 -> white, otherwise near-black). Every pairing clears 4.5:1.
const DAY_INK     = ['#FFFFFF','#0B0D12','#0B0D12','#0B0D12',
                     '#FFFFFF','#0B0D12','#FFFFFF','#0B0D12'];

// [{lat,lng,name,day,type,time,price,url,mapsUrl}] — from the itinerary only.
// lat and lng may be absent: §1 allows a stop with no coordinates, and §6 keeps
// it in the day card regardless.
const STOPS = [];
const DAY_LABELS = {};              // { 1:'Mon 4 May · Oslo', ... }
const DAY_ROUTES = {};              // { 1:'[GOOGLE_MAPS_DAY_URL]', ... }
const TRIP = { start:'[ISO_START]', end:'[ISO_END]' };
const ALLOW_EXTERNAL_QR = false;    // true only if you took the §12 fallback

// Every map layer is built from this list, never from STOPS directly.
// L.marker([undefined, undefined]) throws, and markers, polylines and fitBounds
// share one try — so a single stop without coordinates would otherwise cost the
// whole map. Excluded stops are still rendered in full in the day cards.
const PLOTTABLE = STOPS.filter(s => Number.isFinite(s.lat) && Number.isFinite(s.lng));
const UNPLOTTED = STOPS.length - PLOTTABLE.length;
// Day number drives colour and grouping; fall back to 1 rather than indexing
// DAY_COLOURS with NaN and painting a pin "undefined".
const dayIndex = s => ((Number(s.day) || 1) - 1) % DAY_COLOURS.length;

function setTheme(next) {
  theme = next;
  document.documentElement.setAttribute('data-theme', next);
  const b = document.getElementById('theme');
  if (!b) return;                   // one missing element must not kill the script
  b.setAttribute('aria-pressed', String(next === 'dark'));
  b.textContent = next === 'dark' ? 'Light mode' : 'Dark mode';
}
const themeBtn = document.getElementById('theme');
if (themeBtn) themeBtn.addEventListener('click',
  () => setTheme(theme === 'dark' ? 'light' : 'dark'));
setTheme(theme);

// Expand today's card if the trip is running; otherwise day 1.
(function openCurrentDay(){
  // Local date, assembled by hand. toISOString() is UTC, which opens the wrong
  // card for a traveller standing in Tokyo in the morning.
  const now = new Date();
  const today = now.getFullYear() + '-'
    + String(now.getMonth() + 1).padStart(2,'0') + '-'
    + String(now.getDate()).padStart(2,'0');
  const inTrip = today >= TRIP.start && today <= TRIP.end;
  const n = inTrip
    ? Math.round((Date.parse(today) - Date.parse(TRIP.start)) / 86400000) + 1
    : 1;
  const el = document.getElementById('day-' + n);
  if (el) { el.open = true; if (inTrip) el.scrollIntoView(); }
})();

// Day legend — one swatch per day that actually appears on the map, so it is
// built from PLOTTABLE. An empty #legend is a missing feature, not a
// decoration; with nothing to show, remove it rather than ship an empty list.
(function buildLegend(){
  const el = document.getElementById('legend');
  if (!el) return;
  const days = [...new Set(PLOTTABLE.map(s => Number(s.day) || 1))].sort((a,b) => a - b);
  if (!days.length) { el.remove(); return; }
  el.innerHTML = days.map(d =>
    '<li><i style="background:' + DAY_COLOURS[(d - 1) % DAY_COLOURS.length] + '"></i>'
    + 'Day ' + d + (DAY_LABELS[d] ? ' · ' + DAY_LABELS[d] : '') + '</li>').join('');
})();

// QR per day (§12). Inline encoder if one is embedded above, then the external
// service only when explicitly allowed, then — unconditionally — the link as
// text. The default page has no encoder and ALLOW_EXTERNAL_QR false, so that
// last branch is the common case, not an edge case: without it every QR box
// ships empty and §17 is broken.
(function buildQRs(){
  const LINK_ONLY = 'QR unavailable — the full route link is printed under the button';
  const days = Object.keys(DAY_ROUTES);
  let method = days.length ? LINK_ONLY : 'The route link above works without the QR.';

  function linkOnly(el, url) {
    el.textContent = '';
    const a = document.createElement('a');
    a.className = 'qr-link';
    a.href = url;
    a.textContent = url;
    el.append(a);
    const caption = el.parentElement && el.parentElement.querySelector('.qr-method');
    if (caption) caption.textContent = LINK_ONLY;
  }

  days.forEach(d => {
    const el = document.getElementById('qr-' + d);
    if (!el) return;
    const url = DAY_ROUTES[d];
    if (typeof qrSvg === 'function') {            // inline encoder, embedded above
      el.innerHTML = qrSvg(url);
      method = 'QR generated inline — works offline';
    } else if (ALLOW_EXTERNAL_QR) {
      const img = new Image(180, 180);
      img.alt = 'QR code for the day ' + d + ' route';
      img.loading = 'lazy';
      // Fall back to the link rather than deleting the box — a removed QR
      // leaves the caption claiming a QR that is not there.
      img.onerror = () => linkOnly(el, url);
      img.src = 'https://api.qrserver.com/v1/create-qr-code/?size=180x180&data='
              + encodeURIComponent(url);
      el.append(img);
      method = 'QR served by api.qrserver.com — needs a connection';
    } else {
      linkOnly(el, url);
    }
  });
  document.querySelectorAll('.qr-method').forEach(n => n.textContent = method);
  // A .qr container with no matching DAY_ROUTES entry would ship as an empty
  // box. Nothing empty ships (§17) — the route button above it still stands.
  document.querySelectorAll('.qr').forEach(el => {
    if (!el.firstChild) el.remove();
  });
})();

// Map. Guard it — a tile or CDN failure must not take the page with it.
const mapStatus = document.getElementById('map-status');
function setStatus(text) {
  if (!mapStatus) return;
  mapStatus.hidden = false;
  mapStatus.textContent = text;
}
// Said out loud, not hidden: a stop with no coordinates has no pin, and the
// traveller should know the map is not the full list.
const unpinned = UNPLOTTED
  ? UNPLOTTED + (UNPLOTTED > 1 ? ' stops have' : ' stop has')
    + ' no coordinates and no pin — listed in full in the day cards below.'
  : '';

try {
  const map = L.map('map');
  const tiles = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; OpenStreetMap contributors', maxZoom: 18
  }).addTo(map);

  // Hide the status line once tiles actually arrive; if they never do, say so
  // rather than leaving "Loading map…" on screen for ever.
  let tilesLoaded = false;
  tiles.once('load', () => {
    tilesLoaded = true;
    if (unpinned) setStatus(unpinned);
    else if (mapStatus) mapStatus.hidden = true;
  });
  setTimeout(() => {
    if (!tilesLoaded) setStatus(
      'Map tiles unavailable — every stop below has an address and a directions link.'
      + (unpinned ? ' ' + unpinned : ''));
  }, 8000);

  PLOTTABLE.forEach(s => {
    const i = dayIndex(s);
    const c = DAY_COLOURS[i], ink = DAY_INK[i];
    // Build the popup from the fields the itinerary actually carries. A missing
    // price or URL must not print the word "undefined" at a traveller (§1).
    const popup = [
      s.url ? '<strong><a href="' + s.url + '">' + s.name + '</a></strong>'
            : '<strong>' + s.name + '</strong>',
      [s.type, s.time].filter(Boolean).join(' · '),
      s.price,
      s.mapsUrl ? '<a href="' + s.mapsUrl + '">Directions</a>' : ''
    ].filter(Boolean).join('<br>');
    L.marker([s.lat, s.lng], { icon: L.divIcon({
      className: '', iconSize: [26,26], iconAnchor: [13,13],
      html: '<div class="pin" style="background:' + c + ';color:' + ink + '">'
          + (Number(s.day) || 1) + '</div>'
    }), title: s.name }).addTo(map).bindPopup(popup);
  });

  const byDay = {};
  PLOTTABLE.forEach(s => {
    const d = Number(s.day) || 1;
    (byDay[d] = byDay[d] || []).push([s.lat, s.lng]);
  });
  Object.keys(byDay).forEach(d => L.polyline(byDay[d], {
    color: DAY_COLOURS[(d - 1) % DAY_COLOURS.length],
    weight: 3, opacity: .8, dashArray: '8 5'
  }).addTo(map));

  // fitBounds on a single point snaps to maximum zoom, which lands the traveller
  // on a street corner with no context. One stop gets a readable setView.
  if (PLOTTABLE.length > 1) {
    map.fitBounds(L.latLngBounds(PLOTTABLE.map(s => [s.lat, s.lng])), { padding: [36,36] });
  } else if (PLOTTABLE.length === 1) {
    map.setView([PLOTTABLE[0].lat, PLOTTABLE[0].lng], 14);
  } else {
    map.setView([0,0], 2);
  }
} catch (e) {
  // Leaflet itself failed to load, or threw. Report it on the status line, which
  // sits outside #map, and leave the (empty) map box as a styled blank.
  setStatus('Map unavailable — every stop below has an address and a directions link.');
}
</script>
</body>
</html>
```

Note how the two schemes are wired. The token block appears twice: once under
`@media (prefers-color-scheme: dark)`, scoped to `:root:not([data-theme="light"])`
so the media query yields to an explicit choice, and once under
`:root[data-theme="dark"]` for the toggle. That is what lets the toggle override
the system preference in *both* directions without touching storage, and it keeps
the page correctly themed before the script runs.

---

## 16. The printable PDF

`plan-trip` offers a printable PDF alongside the interactive page. It is the same
document under different physical constraints: no links, no JavaScript, no
scrolling, and a reader who may be holding it in the rain.

**Approach: a print stylesheet on the HTML render, not a second artefact.** Add an
`@media print` block to the page you already built and let the traveller print to
PDF from the browser. One source, one version number, no drift between the screen
copy and the paper copy. Generate a separate PDF file only when the traveller
cannot print for themselves; if you do, generate it *from* the rendered HTML, and
give it the same `{trip-slug}-itinerary-v{n}` stem so the two cannot be confused.

**What changes for print:**

- **Everything is open.** `details { display:block }` and `details > summary
  { display:none }` — or set `open` on every disclosure before printing. A
  collapsed sources block prints as a single word.
- **URLs become text.** A link is not tappable on paper:
  `a[href^="http"]::after { content:" (" attr(href) ")"; font-size:.8em;
  word-break:break-all; }`. Suppress it on links whose text already *is* the URL,
  and keep the "confirm before you go" hours links — they are the ones the
  traveller will type.
- **Force the light scheme.** Print the light tokens regardless of
  `prefers-color-scheme` or the toggle; dark surfaces waste ink and print grey.
- **One day per page.** `.day { break-inside:avoid; break-before:page; }` with
  `break-before:auto` on the first, and `break-inside:avoid` on each `.stop` so a
  stop does not split across a fold.
- **Repeat the emergency block on every page.** `position:fixed; bottom:0` inside
  `@media print` puts it in the page margin on every sheet — this is the one
  block that must be findable without reading. Drop the sticky positioning it
  uses on screen.
- **Drop what does not print.** The theme toggle, the skip link and the map
  interaction. Keep the map only if tiles have rendered; otherwise print the
  status line's text. QR codes stay — they are the bridge from paper back to a
  phone.
- **Backups, addresses and phone numbers stay as text**, per §13. Paper is the
  offline case taken to its limit.

**Page setup.** `@page { size:A4; margin:14mm; }` — A4 unless the traveller is
US-based, where Letter is right. Body 11pt, headings scaled down from screen
sizes. Print the trip title, the version and the page number in the running
footer. Photographs go to `max-height:60mm` or are dropped entirely; they are the
first thing to cut when the document runs long.

Check the result the same way as the screen page: print to PDF, read every page,
confirm no day splits mid-stop and that the emergency block appears on all of them.

---

## 17. Before you hand it over

- Opens from `file://` with no console errors
- No `localStorage` or `sessionStorage` anywhere in the file
- Readable and complete with JavaScript disabled? If not, at minimum readable with
  tiles and images blocked
- Both schemes checked, including pin numerals on tiles — each numeral is the
  colour §4 pairs with its day, and each pairing clears 4.5:1
- No placeholder left inside `#map`; the legend is populated or removed, and
  every QR container holds a code or the route URL as tappable text — nothing
  empty ships
- Any stop without coordinates still appears in full in its day card, is marked
  there as unpinned, and the map status line says how many were left off. One
  incomplete stop must not remove the markers, the routes or the bounds
- Nothing overflows horizontally at 375px
- Every interactive target clears 44px
- Every phone number is a `tel:` link
- Every stop has its sources disclosure
- Every day card carries its day type, theme, energy, walking total and weather
  note on the collapsed summary, and the two Tier 2 check results are on the page
  rather than assumed
- No hours stated as fact anywhere
- No entry, visa or vaccination requirement stated as fact anywhere
- Filename carries a version number, and it is higher than last time
- Tell the traveller which QR method the page uses and what it depends on —
  including when the answer is that there is no QR and the link is the fallback
- If a printable version was asked for, §16 checked on paper: every disclosure
  open, URLs printed as text, one day per page, emergency block on every page
