---
description: Common problems, and what each one usually means.
---

# Troubleshooting

## No prompt at the counter

* Stand closer. `Config.PromptDistance` is `2.2` metres by default.
* Check the branch's `coords` — that is the prompt anchor, and it is separate from
  where the clerk stands (`npc.pos`).
* Check `hours`. A branch outside its posted hours does not take business.

## The cinematic plays but the book never opens

The book waits on two things: the camera settling, and the server's answer. If the
camera has finished and nothing opens, the payload never arrived.

* Check the server console for a Lua error at the moment of the walk-up.
* On VORP, make sure `vorp_core` started **before** `cas-banking`.

## The book opens but everything reads zero

The account was created but the payload is empty. Almost always storage:

* With `oxmysql`, check the three tables exist and the connection string is right.
* Without it, check `cas-banking/data/` is writable.

## Interest is never credited

Interest accrues **lazily, per real day** — it is worked out when the account is next
touched, not on a timer. A day has to pass in real time before there is anything to
credit.

## A withdrawal is refused with money in the vault

The **branch** is out of cash. A counter can only pay what it holds.

* Check that branch's `reserve` on the branch network page.
* Deposits at that counter refill it.
* Or top it up in world state — see [Data and storage](data.md#what-is-safe-to-edit-by-hand).

## Wires and drafts are refused

That branch has `telegraph = false`, so it has no wire. Ride to one that does; the
branch network page marks which counters are on the wire.

## The Deposit Box entry is missing

It is hidden when the bank cannot offer one:

* No inventory is running — `vorp_inventory` on VORP, `rsg-inventory` on RSG.
* Running standalone, which has no inventory to keep a box in.
* The branch has `safebox = nil` — Rhodes ships that way.

This is the only desk removed from the index; see
[Config reference](configuration.md#turning-a-desk-off).

## The statement item does nothing when used

* Check `Config.Statement.document` names an item that **exists in your inventory
  table**. An item the inventory does not know cannot be registered as usable.
* Standalone has no inventory, so no item is handed over at all — the statement is
  shown on screen instead.

## The satchel stays open on top of the statement

Fixed in 1.0.0 — the bridge closes the inventory before opening the sheet. If you
still see it, an older build is running; check `fxmanifest.lua` reads
`version '1.0.0'`.

## Statement totals read zero after translating

The statement sorts entries by matching **words** against ledger descriptions. Once
the ledger is translated, `statement.buckets[].words` has to be translated to match.

Keep the English fragments in the list too, so books written before the translation
still add up. See [Language](language.md#two-things-to-watch).

## Interest on the summary reads zero after translating

Same cause, different key: `ui.overview.interestPrefix` has to match your
`ledger.interest`.

## Collateral is not seized on default

* Check the item name in `Config.Credit.collateral` exists in your inventory.
* On standalone there is nothing to seize — the balance is claimed from the vault
  instead, which is the intended fallback.

## A branch went missing after I edited the config

Changing a branch's `key` removes one branch and adds another. Home branches and safe
boxes stored against the old key are orphaned. Rename `name` and `branch` freely;
leave `key` alone. See [Branches](branches.md#removing-a-branch).

## Prices never move

The exchange ticks on the server every `Config.Market.tickMinutes` (15 by default).
Prices also move by at most `drift` per tick, so a single tick is a small change —
watch the chart over an hour rather than a minute.

## Everything looks unstyled, or textures are missing

The interface loads from `web/dist`. If you rebuilt it, make sure the build actually
succeeded and that `web/dist/index.html` and `web/dist/assets/` both exist, then
restart the resource.

---

## Still stuck?

Contact CAS with:

* Your framework and whether `oxmysql` is running
* The server console output around the failure
* What `Config.Framework` is set to

© CAS. Redistribution of this resource, in whole or in part, is not permitted.
