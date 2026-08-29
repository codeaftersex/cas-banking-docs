---
description: Section II — deposits, withdrawals, wire transfers and bank drafts.
---

# The counter

Where money actually moves.

## Deposit & Withdraw

A shared window: an amount slate, a period keypad, quick sums, and a stamped receipt.

A **withdrawal is paid out of the branch's own cash reserve**. A counter that is
empty cannot pay, no matter what the depositor's book says — it will say so and name
what it holds. Deposits refill it.

### Branch surcharge

Banking away from your home branch costs that branch's `fee`, as a percentage of the
sum moved. Your home branch is always free.

```lua
{ key = 'blackwater', fee = 2.0, reserve = 96200.0, ... }
```

The surcharge is worked out on the server, shown before the order is placed, and
written into the ledger as its own line.

## Wire Transfer

Pay another depositor by telegraph.

The recipient field searches **every account on the bank's books**, by holder name or
account number — not just the handful that arrived with the ledger. A note of up to
30 characters can be sent with the money, either typed or picked from the one-tap
phrases in your locale file.

```lua
Config.TransferFee = 0.02   -- 2% of the amount sent
```

Both sides get a ledger line. The branch surcharge applies on top if you are away
from home.

{% hint style="warning" %}
A branch with `telegraph = false` has no wire. Wires and drafts cannot be sent from
it, and the counter says so.
{% endhint %}

## Bank Drafts

A written order to pay, made out to another depositor and cashed at any branch on the
wire.

The money **leaves your book the moment the draft is written** and the bank holds it.
The payee cashes it at any counter with a telegraph line. Until they do, it is
outstanding — and you can stop it, which returns the face value to your vault.

A draft that is never cashed **expires back to you** after `expiryDays`.

```lua
Config.Drafts = {
  fee            = 1.5,   -- counter charge for writing one
  expiryDays     = 30,
  maxOutstanding = 10,    -- drafts one depositor may have live at once
}
```

### The sheet

The draft is written on a real cheque: the house, the town, the day, the payee, the
figure, the sum written out in words, what it is for, and the drawer's signature.

The sum is spelled out from your locale file, so a translated draft reads in the
translator's own numerals:

```lua
draft = {
  words    = { 'One', 'Two', 'Three', ... },
  tens     = { 'Twenty', 'Thirty', ... },
  hundred  = 'Hundred',
  thousand = 'Thousand',
  andWord  = 'and',
  dollar   = 'Dollar',
  dollars  = 'Dollars',
}
```

### Drafts made out to you

Drafts other depositors have written to you appear at the top of the page as
**Payable to You**, with the drawer, what it is for, and when it expires. Cashing one
credits your vault at whatever counter you are standing at.

{% hint style="info" %}
A stopped or expired draft is not deleted — it stays on the page under **Stopped**
so there is a record of what happened to the money.
{% endhint %}
