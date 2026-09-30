# Cursor — Hunt Hall

Paste this **on top of** [cursor.md](cursor.md). That file still wins on the gate, the rails, the fee, the faucet, and the do-nots. This file only adds one building and the pack desk inside it.

Repo: STP-KAS/sixpack.wtf. Page: https://sixpack.wtf/1984.html. Money note: STP-KAS/1984-why-what-how. Brief: STP-KAS/ask-and-bot. Theory: STP-KAS/stable-staghunt-theory (read IDEA.md).

## Vision (keep it)

The long goal does not change: Kaspa people and crypto people spend a stable (POC-shape or tether-shape) or tKAS at tills that can receive those rails. Shops that accept the rails are where the community goes. Multiplexing is the pressure. That is still the plan after the gate in ask-and-bot/plan.md opens.

What you build today is the *switch*, not the shop behind the brand. Promise a fixed toy bill. Pick N. Hidden pack. Snap only when a subset meets every member's N. Alone you can still sign. An empty forest with one signed intendo is the correct picture. Naming Grok, Tesla, Starlink, or Netflix as the delivery was cheap talk. Keep the *shape* of those bills on the board. Do not print their logos or a "they shipped" banner.

## Pushback (read before you draw a roof)

You are not shipping Project Staghunt. You are not selling Grok, a Tesla, Starlink, Netflix, or a broker account. You are adding a classroom where the *shape* of a hunt can be walked with the three rails that already exist on this square.

- Village POCencept is a ledger tag. Village KUSDT is a ledger tag. tKAS is Testnet-10. None of those is money a stranger at xAI or a dealer can receive.
- A snap in this hall writes a receipt on the sixpack ledger. It does not call an API at a company. The banner after a snap is "Pack paid on this square." Not "Grok is on."
- Do not show "12% full." Opacity is the rule. The board says Hidden until the snap, then it names who paid.
- Do not invent NPC hunters that auto-fill a pack so one visitor "succeeds." A pack on this square is other addresses on the same ledger, or it does not snap. A visitor who is alone can still *sign*. That is the lesson: the promise sits, the coin stays yours, nothing delivered.
- Do not take mainnet. Do not print a seed or the reserve address. Do not compile a SilverScript covenant into this till. Do not call this an intendo on chain.
- Multiplexing is real inside the hall: one pile of cents can sit in several hunts. The first snap that includes that address takes those cents off the other hunts. Teach that. Do not let the same cents pay twice.

If a sentence on the wall needs Tesla to have an address, delete the sentence.

## What to build

One new building: **Hunt Hall**. Keeper **Reed**. Sign `Hunt`. Roof `#4a3a28` (dark timber, not bank blue, not cinema black).

Job of the room: a catalog of stags priced in toy cents. A visitor signs an intendo on one rail. When a subset of signed addresses meets every member's threshold, the subset pays together on that rail and each member gets a classroom receipt. Everyone else keeps their intendo.

### Map

Map stays 42×32. Do not move the fountain, spawn `(20,20)`, parking bays, Cafe, Table, Bank, Market, Roadster, or Cinema.

Place Hunt Hall on the grass **east of Pike's lot, south of Mara**, so it does not touch Pike `(14,23,14×7)` or Lux `(2,23,10×7)`:

- Building: `{ id: "hunt", shop: "hunt", x: 30, y: 23, w: 10, h: 7, door: { x: 30, y: 26 }, npc: { x: 29, y: 26 }, roof: "#4a3a28", sign: "Hunt" }`
- Door on the west wall so you walk in from the cobble / lot side.
- If the tree at `(36,26)` lands inside the footprint, move that tree to `(36,21)` or `(39,12)`. Do not delete the other trees.
- Path: paint a short `p` path from `(27,18)` or `(27,21)` to the door without cutting the parking stalls.
- `shopVisit` and `counterFace` treat `hunt` like the other rooms. `counterFace("hunt")` is `"hunt"`, a new card, not the cafe shop card.
- Side menu gets Hunt next to Market / Roadster. Opening it walks you inside on foot, card shut, gold ring on Reed.

Indoor room: a board on the north wall (the catalog), Reed at a high desk, a bench, no car. Gold ring: Reed first, then the board.

### Catalog (toy cents, classroom delivery)

Add to `world.mjs` as `HUNTS`, not as ordinary `SHOPS` items. A hunt is not an instant Buy. Names on the board are shapes, not companies.

| id | Stag on the board | Cents | Default N | Rails allowed | Classroom delivery |
| --- | --- | ---: | ---: | --- | --- |
| `model` | A month of a model desk | 500 | 3 | poc, kusdt, kas | Banner + receipt. No API call. |
| `cars` | N roadsters as one order | 100 | 3 | poc, kusdt | Same keys flag as Pike if this address does not already own one. Hunt does not replace Pike's single buy. |
| `dish` | Dish + first month | 1200 | 3 | poc, kusdt, kas | Banner + receipt. Saturn hop is still Orbit's till. |
| `stream` | A video month | 500 | 5 | poc, kusdt | Banner + receipt. Not Lux's ticket. |
| `music` | A music month | 500 | 5 | poc, kusdt | Banner + receipt. |
| `basket` | A week of food | 1400 | 3 | poc, kas | Banner + receipt. Not Mara's single loaf. |
| `liquidity` | Deepen the toy dollar | 2000 | 2 | poc | Liquidity hunt spends nothing on snap; it only records that N addresses promised. Do not mint dollars. |

Keep N small so two or three real tabs on this server can snap. Do not put a 500-person hunt on a village ledger.

Each hunt row on the board shows:

- Name and toy-dollar price (cents / 100).
- Allowed rails.
- "Threshold is yours." Input default from the table, min 2, max 20.
- Rail picker: only allowed rails. Open on POCencept if allowed, else first allowed.
- Button: `Promise`, not `Buy`.
- Line: `Hidden pack. Pays if enough promises clear. This square's ledger. Not that company.`

### Ledger

Extend `freshState()` with `hunts: {}` if missing. Do not break old `ledger.json` files: missing `hunts` means empty.

An intendo in state:

```
{
  hunt: "model",
  address,
  rail,
  cents,
  need,
  at
}
```

Functions in `ledger.mjs`:

1. `publicHunts(state, address)` — catalog plus this address's own promise/paid state. **Do not return pack size.** After a snap that included this address, return that snap's member count only to those members.
2. `applyPromise` — store one live intendo per address per hunt. Do not reserve cents yet. Frozen KUSDT blocks kusdt only. Confirm-over same as shops. Receipt kind `promise`.
3. `applyWithdrawPromise` — drop until snapped. Receipt kind `withdraw`.
4. `applySnap` — largest subset S where every member has need <= |S|. Charge each once (takeToken or accepted tKAS payment). If someone cannot pay, drop them and recompute; no partial charge. After a charge, unwind this address's other intendos that the remaining balance can no longer cover (`unwound`, note `First snap took the pile`). `cars` may set `roadster`. Banner: Pack paid on this square. No company call.

POST snap after every promise is fine. Answer `No subset clears.` with no numbers.

### HTTP

- `GET /api/1984/hunts?address=`
- `POST /api/1984/hunt/promise` `{ address, hunt, rail, need, confirmed, network }`
- `POST /api/1984/hunt/withdraw` `{ address, hunt, network }`
- `POST /api/1984/hunt/snap` `{ address, hunt, network, txid? }`
- Guest: `/api/1984/guest/hunt/promise` and `/api/1984/guest/hunt/snap` (tKAS pay only for this guest, same as guest/spend).

Home payload also lists the hunts catalog.

### Card

New face `hunt`. Wall line always: `Classroom pack desk. Tags on this ledger. Not Tether. Not a company till. Hidden until it pays.` Promise, not Buy. Ask on the page first. Reed: `Promise a month if others do. I will not tell you how many already did.`

### Tests

`1984/hunt.test.mjs` plus existing `node --test`:

- Frozen KUSDT blocks kusdt promise, not POC.
- Two addresses, need=2, snap charges both.
- Multiplex: model snap unwinds stream on the same 500 cents.
- Solo need=2: no snap, no pack size in the body.
- tKAS snap without payment is not Paid.
- Buildings do not overlap.

Bump `1984.css` and `1984/client.mjs` query strings. No company logos. Faucet, seeds, reserve, mainnet: untouched.

## Done means

Walk in from the lot. Promise on POCencept. Two test addresses snap. No live pack total. No company-delivered banner. Bot still checks Testnet-10 before push. Then stop.
