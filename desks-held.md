---
description: Section IV — the safe deposit box, company accounts and the branch network.
---

# Held for you

## Deposit Box

A strongbox in the branch's vault: a real `vorp_inventory` custom inventory, one per
character per branch.

```lua
safebox = { slots = 10, maxSlots = 100, slotCost = 10, weapons = true },
```

| Key | What it does |
| --- | ------------ |
| `slots` | Slots the box starts with |
| `maxSlots` | Ceiling the depositor may rent up to |
| `slotCost` | Dollars per extra slot, paid over the counter |
| `weapons` | Whether the box will take weapons |

Rented space is **yours at that branch for good** — it is bought once, not leased.
The charge is taken from the vault and written into the ledger.

Setting `safebox = nil` on a branch means that counter keeps no boxes. Rhodes ships
that way, matching the stock VORP setup. The page then names the branches that do
have them, so a depositor knows where to ride.

{% hint style="warning" %}
This desk is **VORP + `vorp_inventory` only**. There is no equivalent to register on
RSG or standalone, so rather than faking it the desk is hidden and the page explains
that the bank keeps no boxes.
{% endhint %}

---

## Company Account

A chartered outfit with its own book, its own members and a payroll.

```lua
Config.Company = {
  charterFee     = 250.0,
  twoSignatures  = 500.0,   -- above this a plain signatory may not draw alone
  wagesEveryDays = 7,
  autoWages      = false,   -- true = the server pays wages on schedule
  bookKeep       = 40,      -- entries kept in the company book
  rights = {
    deposit = 'Deposit only',
    draw    = 'Draw & deposit',
    full    = 'Full authority',
  },
}
```

### Chartering

Any depositor may name an outfit and open a book in its name, paying `charterFee`
over the counter. They become its **principal**. One charter to a name — a depositor
who already signs for an outfit cannot charter another, and cannot be put on a second
one.

### Members and rights

Hands are hired off the bank's own register — a name must hold an account here. A new
hand starts as **deposit only, on no wage**; open their row to change either.

| Right | What it allows |
| ----- | -------------- |
| `deposit` | Pay into the book. Nothing may be drawn out |
| `draw` | Pay in and draw out, subject to the two-signature rule |
| `full` | Full authority on the book |

### The two-signature rule

Above `twoSignatures`, a plain signatory may not draw alone. Below it, their
signature is sufficient. A principal or full signatory is not limited.

{% hint style="info" %}
The **last full signature cannot leave** the book. If it could, the money would be
locked in for good with nobody able to sign for it.
{% endhint %}

### Wages

Each hand carries a weekly wage. **Pay the Wages** settles them all at once out of
the company book. With `autoWages = true` the server does it on schedule every
`wagesEveryDays`; with it off, somebody has to walk in and press the button.

---

## Branch Network

Every counter on the wire, what it holds, what it charges you, and whether it is open.

Each row shows the town, the region, its hours, its cash on hand, its surcharge, and
its standing. Selecting one works out exactly what a withdrawal of a test sum would
cost you there — what is paid over the counter, what the surcharge adds, and what
comes off the account.

### Branch standing

| Status | What it means |
| ------ | ------------- |
| `open` | Open for business at the posted hours |
| `guarded` | Hired men at the door. Expect to be looked over |
| `robbed` | Lately robbed. The book is unsettled and no cash is paid out |
| `closed` | Shut until further notice |

A robbed or shuttered counter pays out nothing. The page then offers to **put the
counter back on its feet** — but only to somebody acting for the bank:

```lua
Config.Manager = {
  right = 'manager',              -- 'anyone' lets any depositor do it
  aces  = { 'vts.manager', 'command' },
  jobs  = { 'banker', 'bankmanager' },
}
```

An ace on the server or one of the listed framework jobs opens it. Standalone servers
have no job book, so the ace is the only key.

See [Integrating other scripts](integration.md) for how a robbery script fits in.
