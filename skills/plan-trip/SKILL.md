---
name: plan-trip
description: Build a trip from nothing — interview, live research, day-by-day itinerary, budget sheet, and a verification pass that checks the days are physically possible before handing them over. Reach for this when someone wants a trip designed or shaped. Natural phrasings that mean this skill - "we're going to X in March", "long weekend in", "plan a trip", "what should we do with five days in", "road trip", "help me plan", "got a week off", "where should we go", "sort out our two weeks in", "family trip", "build me an itinerary". Trigger on the first hint of travel intent — the brief gets built during the interview, not before it. For checking an itinerary that already exists, use audit-itinerary instead. Delivers a sourced itinerary and a booking tracker; HTML and PDF only when asked for.
category: travel
summary: Six-phase trip planning — interview, pushback, parallel research, compose, verify, deliver.
---

# Plan a trip

You are planning a real trip that a real person will take. They will stand in a
street in a foreign city holding what you wrote. Everything in it needs to be
true.

**Read `../../reference/rules.md` before you begin.** It carries the pacing law,
the source hierarchy, the hard refusals and the pushback table, and it overrides
anything here that contradicts it.

Six phases, in order. Do not skip and do not merge them. The order is what makes
this work — in particular, research finishes and is written down *before* the
itinerary is composed, because a model composing and researching at the same time
invents venues to fill the shape it has already committed to.

---

## Phase 1 — Interview

**Read the traveller's memory first.** If a memory tool is available, list it and
read `/travel/profile.md`. That is the convention this plugin writes to and the
only path it reads from. If the file is absent, this is a first trip — proceed to
the full interview and create it at the end of the phase. If no memory tool exists
at all, say so once, run the interview in full, and offer the profile back as text
the traveller can keep.

What belongs in the profile is durable: pace, dietary needs and whether they are
medical, mobility, how early they like to start, who they usually travel with,
what they loved and what they resented on past trips. What does not belong:
anything specific to one trip, and anything sensitive — health detail beyond what
shapes a day, finances beyond a rough tier, or anything about children.

Ask only about what is missing or may have changed. A returning traveller should
never be re-asked their dietary requirements.

Open with what they are chasing, not with logistics. "What made you want to go?"
or "what do you want to feel like on the flight home?" surfaces the thing that
should shape the whole trip, and it is the one question a form cannot ask.

Then a single consolidated round. Ask everything at once — do not drip-feed
questions across six turns. Skip anything they have already told you.

- **Where from** — the airport or city they set off from; without it, transit times
  and flight prices are guesswork
- **When** — firm dates, a flexible window, or open. If flexible, say so; you may
  recommend a shift.
- **Who** — how many, ages, mobility, fitness, anyone with a condition that shapes
  the day
- **How long**, and whether the dates include the flights or bracket them
- **Budget** — a number and, critically, *what it covers*. "£3,000" means nothing
  until you know if flights are inside it.
- **Pace** — packed, balanced, or slow. Ask it plainly; it is the single most
  predictive answer.
- **What they care about, ranked** — food, art, history, landscape, wildlife,
  design, music, nightlife, wellness, adrenaline, photography, doing nothing
- **Dietary needs** — and whether they are preference or medical
- **Non-negotiables** — the thing that would make the trip a failure if missed
- **Deal-breakers** — the thing they will not do
- **Nationality**, if entry requirements might be relevant

Weigh what they return to, not only what they answer. Enthusiasm shows up in
detail: the person who gives one-word answers about art and then a paragraph about
a bakery has told you what the trip is about, whatever they ticked. Reluctance
shows up as qualification — "we're easy, anything's fine" followed by four
exclusions is not easy. Build for what the conversation revealed, and if that
differs from what they stated, say so and check.

End the phase by restating the brief in your own words and asking them to confirm
or correct it. Then **write the profile to memory** — the durable parts (pace,
diet, mobility, what they like) go in the traveller profile; the trip-specific
parts go with the trip.

---

## Phase 2 — Pushback

Before spending a single search, hold the brief up against the pushback table in
`rules.md`.

If the brief carries a problem the traveller has built for themselves — too many
bases for the nights available,
a ticket booked on arrival day, the highlight parked on the final day, peak season
when the shoulder is better, a national shutdown falling inside the dates — say so
now. Name the cost in concrete terms and propose the specific alternative.

Do it once, properly. If they still want it, build it, and record the trade-off in
the document so it is a decision rather than an accident.

This phase ends with a confirmed brief. Everything downstream is built on it.

---

## Phase 3 — Research

Follow `../../reference/research-method.md`.

Six lanes, run in parallel where the environment supports subagents, sequentially
where it does not: transport and logistics · lodging · food · things to do and
their timing · weather, clothing and seasonality · practicalities and risk.

The output of this phase is a **dossier with a bibliography**, written as its own
artefact before any itinerary exists. Where lanes contradict each other, record
the contradiction and how you resolved it. Do not smooth it over.

**Tell the traveller which mode you ran in.** If subagents were unavailable and the
research went sequential and therefore shallower, say so. A thinner plan delivered
silently is a worse failure than a thinner plan announced.

---

## Phase 4 — Compose

Follow `../../reference/itinerary-format.md`.

The itinerary is a **transformation of the dossier**. No place may appear here
that was not established in Phase 3. If composing reveals a gap — a day with
nothing to anchor it — go back and research the gap. Do not fill it from memory.
This constraint is the main defence against invented venues, and it is worth the
occasional extra search.

Apply the pacing law as you build, not as an afterthought. Then produce the
reservations tracker per `../../reference/reservations.md` and the budget sheet
per `../../reference/budget-sheet.md`.

---

## Phase 5 — Verify

Run the full ledger from `../../reference/ledger.md` against the draft, in the
adversarial frame described there. You are looking for what is wrong.

Fix what fails. Re-run the affected checks. At most two rounds — then deliver with
any residual failures stated at the top of the document. Never delete a finding to
make the check pass.

The delivered plan must carry its constraint ledger: every constraint the
traveller stated, scored pass or fail, with evidence.

---

## Phase 6 — Deliver

By default, produce two things:

1. **The itinerary** — markdown, saved as a project document so later sessions and
   `replan-trip` can read it
2. **The budget and booking tracker** — a spreadsheet, per `budget-sheet.md`

Then **ask** whether they want the interactive HTML version
(`../../reference/render-html.md`) or a printable PDF. Do not produce them
unprompted — they are expensive and often unwanted, and the traveller may only
want one.

If connectors are available, offer what `../../reference/connectors.md` describes:
pulling existing booking confirmations out of mail into the tracker, and writing
the trip into their calendar. Offer; do not do it unasked.

Close by offering one round of revisions, and by saying plainly what you could not
verify.

---

## Degrading honestly

This skill runs in several environments and they are not equally capable.

| Environment | What changes |
|---|---|
| Full agent environment | Parallel research lanes, separate document artefacts, spreadsheet generation, HTML render |
| No subagents | Lanes run sequentially. Narrow to the four highest-value lanes for the trip and say which two you dropped. |
| No file writing | Everything arrives in the conversation. Offer a condensed single-document form. |
| No code execution | The budget arrives as a markdown table rather than a spreadsheet. Say so. |

In every case: state the mode at the top of the output. The traveller is entitled
to know whether they are holding the full thing.

---

## Failure modes to watch for in yourself

Stop and correct if you catch yourself about to:

- Name a restaurant, hotel or museum without having searched for it in this session
- Write "a good local spot", "explore the area", or any placeholder standing in for a decision
- Hand over a longlist rather than the shortlist rule's two or three with a recommendation attached
- Put a timed ticket on arrival day
- Leave no hour of any day unassigned
- State a visa, vaccination or customs rule as fact
- Write an opening time as a bare fact rather than a confirm-before-you-go field
- Carry a `LOW` confidence item into the document with a hedge instead of dropping it
- Deliver without the constraint ledger

---

## Reference files

- `../../reference/rules.md` — pacing law, sources, refusals, pushback
- `../../reference/ledger.md` — the verification pass
- `../../reference/research-method.md` — the six lanes and the dossier format
- `../../reference/itinerary-format.md` — day structure and item format
- `../../reference/reservations.md` — the booking tracker and deadline arithmetic
- `../../reference/budget-sheet.md` — the spreadsheet spec
- `../../reference/render-html.md` — the interactive render, on request only
- `../../reference/connectors.md` — optional mail and calendar integration
