# Notices and attribution

`voyage` is released under the MIT licence. It was written after surveying the
open-source prior art in this space, and it owes those projects a debt. This file
records what was taken and on what terms.

---

## Code and templates adapted

### theoms/co-work-travel-planner-claude — MIT

<https://github.com/theoms/co-work-travel-planner-claude>
Copyright (c) 2026 Omri Ben

`reference/render-html.md` adapts the working Leaflet + OpenStreetMap map
template and the Google Maps directions URL construction rules from that
project's `map-template.md`. The MIT licence permits this; the attribution is
recorded here and in a comment in the file itself.

Changes made: dark/light dual-scheme rewrite using CSS custom properties;
responsive breakpoints for phone, tablet and desktop; accessibility work
including day-number text inside pins rather than colour-only encoding; the
waypoint limit corrected to Google's documented nine with a rule for splitting
beyond it; a note that `travelmode=transit` silently drops waypoints, which
requires per-leg links on transit days; and a fallback chain for tiles, images
and QR codes.

---

## Ideas adopted, expression original

The following projects were read for their design. Mechanisms and structural ideas
are not subject to copyright, but credit is owed regardless.

`voyage`'s prose was written independently and then reviewed line by line against
the prior art specifically to find passages that had converged on someone else's
phrasing. Several had, and were rewritten. If you spot one that survived the
sweep, please open an issue — it is a defect, not a decision.

### apljacob/travel-agent — CC BY-NC 4.0

<https://github.com/apljacob/travel-agent>
Copyright (c) 2026 Jacob Cassar

The single best piece of prior art in this space. `voyage` is MIT-licensed and
therefore cannot incorporate any CC BY-NC material, so nothing was copied — but
several mechanisms in `voyage` were prompted by that project and it deserves
naming:

- The idea that a planner's duty includes **arguing with a bad brief** rather than
  complying with it, expressed as a table of named bad patterns
- **One named anchor per day with a named backup**, rather than a list of things
  to do
- **Booking deadlines computed from the traveller's real dates** and stated as
  calendar dates, rather than generic "book a few weeks ahead" advice
- A **per-day self-check** run before a day is considered finished

Its `elite-planner-principles.md` synthesises the working methods of Wendy Perrin,
Black Tomato, Indagare and the bespoke-travel field with sources attached. Anyone
building in this area should read it.

### ErlebnisW/travel-planner — MIT

<https://github.com/ErlebnisW/travel-planner>
Copyright (c) 2026 Mingzhi Wang

Source of two ideas: **parallel research lanes** dispatched as independent agents,
and a **bibliography as a first-class artefact** so every claim is traceable. The
second of these is, as far as the survey found, unique to that project among
travel tools.

### borski/travel-hacking-toolkit — MIT

<https://github.com/borski/travel-hacking-toolkit>
Copyright (c) 2026 Michael Borohovski

Source of the **progressive disclosure** plugin pattern — skill descriptions load
at startup, bodies only on invocation — which is what allows a pack of skills to
exist without consuming context. Its domain (points, miles and award fares) is
deliberately out of `voyage`'s scope; anyone wanting that should use it directly.

### tonykipkemboi/trip_planner_agent — MIT

<https://github.com/tonykipkemboi/trip_planner_agent>

Source of the **dossier-before-itinerary** split: research completes as a
standalone artefact, and the itinerary is a transformation of it rather than a
parallel act of invention.

### skarlekar/mcp_travelassistant — MIT

<https://github.com/skarlekar/mcp_travelassistant>

Source of the rule that **currency normalisation is an explicit step** with a
recorded rate and date, rather than a footnote on a total.

---

## Research and benchmarks cited

The verification ledger exists because of published evidence that language models
fail at itinerary construction in measurable, specific ways. The design cites:

- **TREK** — <https://arxiv.org/html/2607.26977> — nine deterministic feasibility
  dimensions; best model 46.2% fully feasible, median across fifteen models 6.6%
- **TravelPlanner** — <https://arxiv.org/abs/2402.01622> — 1,225 scenarios,
  GPT-4 final pass rate 0.6%
- **Iti-Validator** — <https://arxiv.org/html/2510.24719v1> — 78–82% of one
  model's travel segments temporally impossible

Documented real-world failures that shaped the hard refusals in `reference/rules.md`
are cited in that file.

---

## Third-party runtime dependencies

The HTML render loads, from CDN, at the traveller's browser:

- **Leaflet** 1.9.4 — BSD 2-Clause — <https://leafletjs.com/>
- **OpenStreetMap** tiles — ODbL — <https://www.openstreetmap.org/copyright> —
  attribution is rendered on the map as that licence requires

No API keys are needed and no data is sent anywhere by the rendered page.
