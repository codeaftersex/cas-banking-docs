---
description: Every item the bank hands out, takes away or stores.
---

# Items and inventory

The bank touches four kinds of item. All of them are optional — each one degrades to
something sensible when the inventory cannot provide it.

## The printed statement

```lua
Config.Statement = {
  document = 'legalpaper',   -- nil = statement stays on screen only
  clerk    = 'E. Whitfield, Chief Teller',
  seal     = 'Valentine Trust & Savings Co.',
}
```

The item must **exist in your inventory table**. `legalpaper` ships with
`vorp_inventory`; point `document` at anything else you already have.

Nothing else needs setting up — the script registers the item as usable itself at
startup, on both VORP and RSG.

### How it behaves

Using the item shuts the satchel and opens the sealed sheet on screen. The figures
were **sealed into the item's metadata** when it was drawn up, so:

* A statement given to another player still reads the balances it was printed with.
* An old statement in a drawer still reads its own period, not today's.
* A statement drawn before a rename still carries the old house name.

That is the point of it — it is a document, not a live view.

{% hint style="info" %}
A depositor can hold as many statements as their satchel allows. Each is its own
sealed period.
{% endhint %}

## Loan collateral

```lua
collateral = {
  { id = 'none',   name = 'Unsecured',      value =   0.0, surcharge = 3.0, item = nil },
  { id = 'horse',  name = 'Horse Deed',     value = 850.0, surcharge = 0.0, item = 'horsedeed' },
  { id = 'gold',   name = 'Gold Bar',       value = 500.0, surcharge = 0.0, item = 'goldbar' },
  { id = 'jewels', name = 'Jewellery Case', value = 340.0, surcharge = 1.0, item = 'jewelry' },
}
```

`item` is the inventory item **really removed** from the borrower when a note is
forfeit. If your inventory does not have `horsedeed`, `goldbar` or `jewelry`, either
rename them here or set `item = nil`.

| `item` | On default |
| ------ | ---------- |
| An item you have | The item is removed from the borrower |
| `nil` | The outstanding balance is claimed from the vault |
| Set, but the framework cannot remove it | Falls back to the cash claim |

The debt is never silently forgiven.

{% hint style="warning" %}
`value` is what the bank reckons the security is worth when deciding what to advance.
It is not checked against a market price — set it to what that item is actually worth
on your server, or the loan office will over-lend against a trinket.
{% endhint %}

## The safe deposit box

Not an item but a container: a `vorp_inventory` custom inventory registered per
character per branch, with an id of the form `vts_safebox_<branch>_<account>`.

Slots are bought with `slotCost` and are permanent. `weapons = true` lets the box take
weapons. See [Held for you → Deposit Box](desks-held.md#deposit-box).

{% hint style="warning" %}
The box is registered against the branch **key**. Renaming a branch's `key` orphans
every box stored under the old one — see [Branches](branches.md#removing-a-branch).
{% endhint %}

## Cash and gold

Not items the bank creates, but what it moves.

| Framework | Pocket cash | Gold |
| --------- | ----------- | ---- |
| VORP | `vorp_core` character money | `vorp_core` gold |
| RSG | `rsg-core` player money | `rsg-core` gold |
| Standalone | Kept by the bank itself, starting at `Config.StandalonePocket` | Not offered |

Everything in the vault is the bank's own record either way — the framework holds
only what is in the player's pocket.
