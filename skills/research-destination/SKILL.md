---
name: research-destination
description: Research a destination properly and hand back a sourced dossier — no itinerary, no dates needed. Use when someone is reading up on a place, weighing one against another, or working out when to go. Fires on "tell me about", "what's X like in [month]", "what's it like in winter", "is it worth going to", "research this destination", "should we go to X or Y", "X or Y for a week", "what's the best time to visit", "is February a bad time for", "help me decide where to go", "what should I know before booking". Fire on a soft match. Produces a dossier with a bibliography and a verdict with a position taken.
category: travel
summary: The research lanes without the itinerary — a sourced dossier and a straight answer on where and when.
---

# Research a destination

Someone wants to know what a place is actually like before they commit money to
it. They do not want a day-by-day plan and they may not have dates. Give them the
research and a straight answer.

**Read `../../reference/rules.md` before you begin** — the source hierarchy, the
hard refusals and the specificity rules apply here in full. The work itself is
`../../reference/research-method.md`. This file says what changes when there is no
trip attached.

---

## How this differs from `plan-trip`

| | `plan-trip` | here |
|---|---|---|
| Interview | Full, confirmed brief | Destination and rough timing. Three questions, one round |
| Dates | Required | Not required |
| Pacing law | Applied to every day | Not applied — there are no days |
| Ledger | Tiers 1, 2 and 3 | **Tier 1 only** — and in full |
| Output | Itinerary, tracker, budget | Dossier, bibliography, verdict |

Tier 1 of `../../reference/ledger.md` is not relaxed: address, primary-source URL,
evidence-of-operation year, price, confidence, hours as a confirm-before-you-go
field, `LOW` items dropped rather than hedged. The dossier is exactly where
invented venues would originate, so the item standard stays where it was. Tiers 2
and 3 do not apply — nothing to traverse, no constraint set to score.

---

## Phase 1 — the short brief

Do not run the `plan-trip` interview. Most people asking what somewhere is like
want an answer, not a form. Read the traveller's memory first for a stored profile
— pace, diet, mobility, what they like — and use it silently rather than
re-asking. Then ask at most three things, in one message, skipping anything
already known:

- **When, roughly** — a month or a season is enough
- **Who** — how many, and anything that shapes what is worth telling them
- **What they are chasing** — the lens the dossier gets written through

A place and a month is enough to start. If you have that, ask nothing, research,
and raise the gaps alongside the findings — blocking a simple question behind an
interview is the wrong trade.

---

## Phase 2 — research

Run the six lanes per `../../reference/research-method.md`, with § 9 of that file
governing the no-dates case: climate normals rather than a forecast, closure days
rather than weekday checks, booking horizons in days rather than calendar
deadlines. Parallel where subagents exist, four lanes where they do not — and
state the mode at the top of the output either way.

Two lanes shift weight here. **Weather and seasonality** usually carries the
question, because "what's Lofoten like in February" is a seasonality question
wearing a destination's coat: daylight, what is shut, what the road is like, what
the boat does. **Things to do & timing** shifts from booking mechanics towards
whether the thing is worth the journey at all in that month.

---

## Phase 3 — the verdict

The dossier is the evidence. The verdict is the answer, and it goes first — five
to ten lines, above the dossier body:

- Whether the place is worth it for **this** traveller in **that** month, outright
- The one thing that would change their mind, in either direction
- The best window, if the month they named is not it, with the trade-off stated
- What going anyway costs: daylight, a shut cable car, a winter restaurant closure
- The two or three things they must book ahead if they go

Take a position. "Both are lovely in their own way" is not an answer and rules.md
§ 8 forbids it. If February is the wrong month, say so and name the right one.

---

## "X or Y?"

Apply the three-positioned-options rule from rules.md § 8. Research both properly
— not one thoroughly and the other from memory, which is the default failure. Same
lanes, same item standard, same bibliography for each.

Then give three options, not two: **X**, with what it is better at and what it
costs · **Y**, the same · and a **third** — either both, if the nights genuinely
carry it under the two-nights-minimum rule, or a different place that beats both
on what they said they were chasing. If there is no honest third, say so and give
two. Then name the one you would take, and why, in their terms. A comparison with
no position in it tells them nothing a search engine would not have. Where the
answer turns on something they have not told you — whether they will hire a car,
whether the six-year-old is coming — state the branch: "X if you're driving, Y if
you're not, and here is why."

---

## Output and handoff

```markdown
# [Destination] — [month or season]
Mode: [6 lanes parallel | 4 lanes sequential — dropped X, Y]

## Verdict          [position taken, per Phase 3]
## Dossier          [the six lanes, per research-method.md § 6]
## Conflicts · Gaps · Bibliography
```

Deliver as a project document so a later `plan-trip` session can read it rather
than researching from scratch, and close by saying what you could not verify.

When they decide, offer the itinerary — do not start planning unasked. A dossier
already covers Phase 3 of `plan-trip`, so the handoff skips it: run that skill
from Phase 1 and feed the dossier straight into Phase 4. Two things carry across —
re-check anything time-sensitive if the dossier is more than a few weeks old, and
run the weekday closure checks skipped here for want of dates.

---

## Failure modes to watch for in yourself

Stop and correct if you catch yourself about to:

- Slide into an itinerary because the findings suggested a shape
- Answer a comparison without taking a position
- Research the option you prefer and sketch the other from memory
- State a visa, entry or vaccination fact instead of linking the official source
- Name a place you have not searched for in this session
- Skip the bibliography because no itinerary hangs off it
- Interview them for six turns when they asked a three-line question

---

## Reference files

- `../../reference/rules.md` — sources, refusals, specificity, pushback
- `../../reference/research-method.md` — the six lanes, the dossier, the bibliography
- `../../reference/ledger.md` — Tier 1 item standards, which apply here in full
