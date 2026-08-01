# voyage — the budget and booking sheet

A spreadsheet, produced by default alongside the itinerary. It answers three
questions the markdown cannot: what does this actually cost, what is still
unbooked, and are we over the cap.

It is built with formulas, not typed totals. The traveller will change something —
they always do — and the sheet has to survive it.

---

## 1. Sheets

Four, in this order.

**Summary.** Read-only in spirit; every figure is a formula pointing elsewhere.
Trip title and dates. Party size. Total party cost and total per person, in the
home currency. A category table with each category's total, its share of the whole
and its per-person figure. The stated budget cap, the variance against it in cash
and percent, and a plain verdict cell: under, at, or over. Pre-paid versus
pay-locally split. Estimated cash needed on the ground. The contingency line and
its percentage. Below the numbers, three lines of prose: the biggest single cost,
the softest estimate in the sheet, and the one lever that would move the total
most.

**Line items.** The body of the work. One row per spend, in day order. Columns in
§2. This is the only sheet where a number is ever typed.

**Bookings.** The reservations tracker from `reservations.md`, as a sheet. Carry
its columns and its state codes exactly — `[OPEN]`, `[SENT]`, `[DONE]`, `[GONE]`,
`[LATE]`, `[WAIT]` — so the tracker reads the same
in the spreadsheet as in the itinerary. Do not invent a second vocabulary for the
same thing. Add one column the markdown tracker does not need: the line item ID
(§2), so a booking and its cost are joined. Sorted by deadline, earliest first,
with passed deadlines flagged loudly per `rules.md` §6.

**FX.** One rate per currency pair, and nothing else. See §3.

---

## 2. Line item columns

| Column | Notes |
|---|---|
| ID | short stable key — `D3-LUNCH` — so Bookings can point at it |
| Category | from the fixed list in §4. Use a dropdown; free text breaks the Summary. |
| Item | the named thing. Not "dinner" — the restaurant. `rules.md` §8 applies here too. |
| Day | trip day number, or `PRE` for anything bought before departure |
| Date | the calendar date the money is spent |
| Currency quoted | the currency the price was actually seen in |
| Cost pp (quoted) | per person, in the quoted currency, exactly as seen |
| Party size | how many people this line covers — not always the whole party |
| Party cost (quoted) | formula: `cost pp × party size` |
| Rate | formula: lookup into the FX sheet on the quoted currency |
| Party cost (home) | formula: `party cost (quoted) × rate` |
| Cost pp (home) | formula: `party cost (home) ÷ party size` |
| Payment | `pre-paid` or `pay-locally` — dropdown |
| Cash needed | `yes`/`no`. Feeds the cash estimate on Summary. |
| Status | the `reservations.md` state code, or `[N/A]` where nothing needs booking. Dropdown: those six codes plus `[N/A]`, per `reservations.md` §3. |
| Confidence | `quoted` (a real price seen on a real page), `estimated` (your figure), `allowance` (a daily budget, not a specific purchase) |
| Source URL | where the price came from. Blank is only acceptable when confidence is `estimated`. |
| Price seen | the date you saw it. Required whenever confidence is `quoted`. |
| Notes | what is included, what is not, the assumption behind an estimate |

The `confidence` column matters more than it looks. A sheet where a researched
ferry fare and a guessed lunch sit in identical cells invites the traveller to
trust both equally. Count the `estimated` rows on Summary and say what share of
the total they carry.

---

## 3. Currency

Per `rules.md`: a foreign amount never loses the form it was quoted in.

**The quoted figure is never overwritten.** `cost pp (quoted)` and
`currency quoted` are what the traveller will see on the till, on the booking page
and on the card statement. Conversion happens in a separate column, by formula.

**One rate table on the FX sheet, referenced by every line.** Columns: currency
code, rate to home currency, date the rate was taken, source. Line items look the
rate up by currency code. A rate change is then one cell, and the whole sheet
moves — which is the point. Never type a rate into a line item.

**Every rate carries its date and its source.** A rate with no date is a rate you
cannot audit three weeks later when the total looks wrong.

**State the home currency once, on Summary, as a labelled cell** that the rest of
the sheet refers to. Do not hardcode a symbol into column headers.

Rates move. Put a line on Summary: rates taken on `[date]`, a 5% move against you
would add `[formula]` to the trip. For a trip more than a couple of months out,
that sentence is the difference between a budget and a wish.

---

## 4. Categories

Fixed list. Use exactly these strings so the Summary rollup holds.

| Category | What lands here |
|---|---|
| Getting there | Long-haul and inbound/outbound flights, seat and bag fees, home-airport parking or transfer |
| Internal transport | Rail, ferries, internal flights, car hire and fuel, tolls, city transit passes, airport transfers at the destination |
| Lodging | Room rate, city and tourist taxes, cleaning fees, resort fees |
| Activities and entries | Tickets, guides, courses, hire of equipment, permits |
| Food — booked | Reserved meals with a known or estimable price |
| Food — allowance | The daily per-person figure for everything not booked. One row per day, or one row with a day count. |
| Gear and pre-trip | Boots, waterproofs, adaptors, luggage, vaccinations paid for privately |
| Insurance | Travel, medical, gear, cancellation |
| Visas and fees | Application and processing fees only. **The fee amount, never the requirement.** `rules.md` §2 stands: link the official source, do not state what is required. |
| Tips | Where tipping is customary, at the local norm. Zero it explicitly where it is not, rather than omitting the row. |
| Contingency | §6 |

Splitting food into booked and allowance is deliberate. Booked meals are known;
the rest is a rate multiplied by days, and blending them hides which half of the
food budget is actually a guess.

---

## 5. Pre-paid, pay-locally and cash

Two questions the traveller needs answered before they leave.

**What has already left the account, and what has not.** Summary carries both
totals. Someone who has paid for flights and lodging months ahead experiences the
trip as costing the pay-locally figure, and a sheet that only shows the grand
total does not tell them what they will feel.

**How much cash to carry.** Sum the pay-locally rows flagged `cash needed`, add
the tips row, and present it in the local currency, not the home one — that is the
number they will hand over at a bureau.

For a cash-heavy destination, say so on Summary and give the per-day cash figure as
well as the trip total. `rules.md` §9 requires the cash-heavy check on every trip;
this is where the answer becomes a number. Note the card-acceptance reality where
research found one — rural Japan, Norwegian mountain huts, German restaurants —
and cite it.

---

## 6. Contingency

A line at **10–15%** of everything above it, computed as a formula on the subtotal.

It is a stated, visible choice, not a pad hidden inside the other numbers. Never
inflate individual estimates to build in slack — that corrupts every figure the
traveller might check against a real price, and they will find out.

Pick the rate and say why in one line:

- **10%** — a familiar destination, most of it booked, stable currency
- **12–13%** — the default
- **15%** — shoulder-season weather risk, a long unbooked tail, a volatile
  currency, remote legs where the fallback is expensive

---

## 7. Per person, when the split is uneven

Where the party does not split evenly — a child on a reduced fare, one person
skipping the dive, two rooms of different sizes, separate flights — the sheet
carries a per-traveller breakdown.

Add a block on Summary: one column per named traveller, one row per category,
formulas summing the line items assigned to each. Add a `who` column to Line items
holding names or initials, and let the party-size column stay for the simple rows.

Do this whenever the per-person figures would differ by more than about 10%.
Otherwise a single per-person average is fine and the extra machinery is noise.

---

## 8. The budget check

The sheet is where the budget constraint in `ledger.md` Tier 3 gets its evidence.

Summary carries the cap as the traveller stated it, and — critically — **what they
said it covers**. "£3,000" is not a constraint until you know whether flights are
inside it. The comparison must be like for like: if the cap excludes flights, the
variance formula excludes the getting-there category.

Output a single verdict cell, and feed it into the constraint ledger:

```
| 1 | Budget under £2,400 pp, flights included | PASS | £2,180 pp — budget sheet, Summary |
```

If it fails, do not quietly shave the estimates. Report the overrun and name the
two or three lines that would close it, with what each costs in trip quality. That
is the pushback stance from `rules.md` §7 applied to money.

---

## 9. The daily-spend view

A block on Summary, one row per trip day: planned spend for that day, a column for
actual, and a running variance.

The traveller fills the actual column as they go. `replan-trip` reads it: a
disruption on day 5 lands differently when days 1 to 4 came in £200 over. Without
this, overspend is only discovered on the statement after they get home, which is
too late for it to change anything.

Keep it to three columns. A daily-spend tracker with fifteen fields does not get
filled in.

---

## 10. When code execution is unavailable

Produce exactly the same structure as markdown tables in the conversation, and say
why up front:

> No spreadsheet in this environment — here is the same budget as tables. Totals
> are computed once, so if you change a figure, recompute the ones below it.

Summary as a category table plus the verdict line. Line items as one table, same
columns, trimmed to what fits: ID, category, item, day, cost pp quoted, currency,
cost pp home, payment, status, source. Bookings as its own table, deadline-sorted.
FX as a four-row rate table.

State plainly what has been lost: the formulas. Every figure is a snapshot, edits
do not propagate, and the daily-spend view has nowhere to record actuals. Offer to
regenerate the sheet in an environment that can run code.

---

## 11. Before you hand it over

- Every total is a formula. Nothing is a typed number that should have been
  computed.
- No quoted figure has been overwritten by its conversion.
- Every rate appears once, on the FX sheet, with a date and a source.
- Every `quoted` row has a source URL and a price-seen date. Count the ones that
  do not and say the number, per `ledger.md` Tier 3.
- Categories match the fixed list exactly.
- Contingency is a visible line, not buried in the estimates.
- The cap comparison covers the same things the cap covered.
- No visa, vaccination or entry requirement is stated anywhere — fees only.
- Bookings is sorted by deadline and past deadlines are flagged.
