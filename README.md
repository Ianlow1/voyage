# voyage

**Trip planning that checks its own work.**

Five skills for Claude that turn a vague travel idea into a bookable, sourced,
feasibility-checked itinerary — and then keep it useful while the trip is
happening.

---

## Why this exists

Language models are bad at itineraries in a specific, measurable way.

On the [TREK benchmark](https://arxiv.org/html/2607.26977), the strongest model
tested produces a fully feasible plan **46.2%** of the time. The median across
fifteen models is **6.6%**. On [TravelPlanner](https://arxiv.org/abs/2402.01622),
GPT-4 scored **0.6%** across 1,225 scenarios.
[Iti-Validator](https://arxiv.org/html/2510.24719v1) found **78–82%** of one
model's travel segments were temporally impossible — including an eight-hour
window for an eleven-hour flight.

These are not matters of taste. They are wrong opening hours, venues that closed
years ago, days that cannot physically be walked, and confident answers about visa
rules. People have acted on them: tourists sent to a canyon in Peru that does not
exist, a couple who drove 300km to a cable car that was never built, a traveller
who missed her flight because a chatbot told her she did not need an ESTA.

Every AI travel tool surveyed while building this had the same shape: **generate
once, never check.** No feasibility validation. No re-planning when something
breaks. No audit of the finished plan against what the traveller actually asked
for.

`voyage` is built around the checking.

---

## The five skills

| Skill | When you'd use it |
|---|---|
| **`plan-trip`** | Months out. Interviews you, argues with the bad parts of your brief, researches, builds, verifies, delivers. |
| **`research-destination`** | Before you've committed. "What's Lofoten like in February?" Sourced, no itinerary. |
| **`audit-itinerary`** | Any time. Runs the full verification pass on *any* itinerary — one this built, one you wrote, one a tour operator sent you. |
| **`pack-for-trip`** | About a week out, when a real forecast exists. Builds the list from the actual weather for your actual days, not from the season. |
| **`replan-trip`** | Mid-trip. Flight cancelled, museum shut, it's pouring, you're two hours behind. |

Five skills because there are five genuinely different moments. Rendering,
budgeting and research are phases of planning, not separate errands, so they live
as reference files rather than commands.

---

## How it works

**Research finishes before the itinerary starts.** Six lanes — transport, lodging,
food, activities, weather and clothing, practicalities and risk — run in parallel
and produce a dossier with a bibliography. Only then is the itinerary composed, as
a *transformation* of that dossier. Nothing may appear in the plan that was not
established in research. This is the main defence against a model inventing a
restaurant to fill a slot it has already committed to.

**Every item carries evidence.** Street address, primary-source URL, and the most
recent year with evidence the place is actually trading. Older than eighteen
months and it is marked `UNVERIFIED`. Low-confidence items are *dropped*, not
hedged into the document with a caveat.

**Every day is checked for whether it is possible.** Transit, buffer, queue and
visit are four separate numbers, never one blended figure. Walking estimates are
padded because map apps run 30–40% optimistic. Closure days are checked against
the actual weekday of the actual date. A day that needs nine hours inside an
eight-hour window is reported as a failure, with the fix.

**Every plan is scored against what you said.** The constraint ledger reproduces
each constraint you stated and marks it pass or fail with evidence. Budget,
walking tolerance, dietary needs, mobility. Failures appear at the top of the
document, not buried — because a failure you can see and accept is fine, and one
you discover in Bergen is not.

**It argues with you.** Twelve cities in ten days, a timed ticket on the day you
land, the highlight parked on the final day where a storm can take it — these get
pushed back on once, with the cost named and an alternative proposed. If you still
want it, you get it, and the trade-off is recorded.

**It refuses some things absolutely.** It will never tell you what the visa
requirement is — it links the official government source and tells you to check.
It will never book anything. It will never write a URL it has not seen.

---

## Install

```bash
/plugin marketplace add <this-repo>
/plugin install voyage
```

Then:

```
/voyage:plan-trip
```

Or just say what you want — the skills fire on natural phrasing. "Plan a week in
Norway in September", "is this itinerary realistic?", "what should I pack?",
"our train's been cancelled".

---

## Where it runs

Skills work across Claude — web, mobile, desktop and Claude Code. What changes by
surface is capability, not availability:

| Environment | What you get |
|---|---|
| Cowork desktop / Claude Code | Everything. Parallel research, separate document artefacts, spreadsheet output, HTML render. |
| Cowork web and mobile | Same skills. No local file access unless the desktop app is open. |
| Regular chat | Skills load and run, but research lanes go sequential — slower and shallower, delivered as one response. |

In every mode, `voyage` states which mode it ran in. A thinner plan delivered
silently is worse than a thinner plan announced.

---

## Optional connectors

All offer-only — `voyage` never touches them unasked.

- **Mail** — pull existing booking confirmations into the reservations tracker
- **Calendar** — write the trip as events, in the destination's time zone
- **Flight search** — price ranges and routing options, never a booking path

Missing connectors degrade gracefully and are reported, never fatal.

---

## What it does not do

- **Book anything.** It produces the plan, the deadlines and the links. You press the button.
- **Points and miles.** Out of scope by choice — [borski/travel-hacking-toolkit](https://github.com/borski/travel-hacking-toolkit) does that properly.
- **Judge whether you'll enjoy it.** Taste is not checkable and the ledger does not pretend otherwise. It checks that the places exist, the days are possible, and the plan does what you asked.

---

## Structure

```
voyage/
├── skills/
│   ├── plan-trip/            # the front door
│   ├── research-destination/ # dossier only
│   ├── audit-itinerary/      # the adversarial pass
│   ├── pack-for-trip/        # forecast-driven
│   └── replan-trip/          # mid-trip repair
└── reference/                # read by the skills, never invoked
    ├── rules.md              # pacing law, sources, refusals, pushback
    ├── ledger.md             # the verification spec
    ├── research-method.md    # the six lanes
    ├── itinerary-format.md   # day structure
    ├── reservations.md       # deadline arithmetic
    ├── budget-sheet.md       # the spreadsheet
    ├── render-html.md        # interactive output, on request
    └── connectors.md         # optional integrations
```

---

## Licence and credit

MIT. See [`NOTICE.md`](NOTICE.md) for what was taken from the prior art and on
what terms — particularly [theoms/co-work-travel-planner-claude](https://github.com/theoms/co-work-travel-planner-claude),
whose Leaflet map template is adapted here, and
[apljacob/travel-agent](https://github.com/apljacob/travel-agent), whose design
prompted several mechanisms in this one and which is worth reading in its own
right.
