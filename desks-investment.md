---
description: Section III — certificates of deposit, the stock exchange and the loan office.
---

# Investment

## Certificates

Money locked away for a fixed term at a better rate than the ordinary account.

```lua
Config.Certificates = {
  penalty = 0.15,   -- share of interest kept if broken early
  maxOpen = 6,
  minimum = 50.0,
  terms = {
    { days =  30, rate = 2.5 },
    { days =  90, rate = 4.0 },
    { days = 180, rate = 6.5 },
    { days = 365, rate = 9.0 },
  },
}
```

A certificate is not payable at the counter until the date on its face. Broken
sooner, the bank keeps `penalty` of the interest earned so far — the principal always
comes back in full.

Terms are counted in **real days**, and like account interest they are worked out
lazily when the account is next touched.

Add, remove or re-rate terms freely; the page draws whatever the list contains.

---

## Stocks & Bonds

Six securities on a board that moves on the **server**, not in the browser.

```lua
Config.Market = {
  brokerage   = 0.015,  -- 1.5% both ways
  tickMinutes = 15,     -- how often prices move
  drift       = 0.02,   -- most a price may move in one tick, either way
  pull        = 0.06,   -- pull back toward the opening price
  dailyCap    = 250,    -- shares one depositor may buy per real day
  historyKeep = 14,     -- closings kept for the chart
}
```

`pull` is what stops a security running away: every tick the price is nudged gently
back toward where it opened, so a long unlucky streak self-corrects instead of
driving a share to zero.

### The board

| Ticker | Name | Sector | Kind |
| ------ | ---- | ------ | ---- |
| CKT | Cornwall Kerosene & Tar | Oil & Refining | Share |
| CUR | Central Union Railroad | Railroads | Share |
| AMC | Annesburg Mining Co. | Coal & Iron | Share |
| SDC | Saint Denis Cotton Exchange | Commodities | Share |
| LRB | Lemoyne Riverboat Co. | Shipping | Share |
| USB | U.S. Treasury Bond, 1899 | Government Paper | Bond, 4% coupon |

### Shares vs bonds

**Shares** move with the board. The page shows what you hold, your average book, the
value at the current quote, and the gain or loss against book.

**Bonds** are held to their date and redeemed at par — no gain or loss is reckoned.
What they pay is the `coupon`, credited yearly.

### Adding a security

```lua
{ id = 'cornwall', ticker = 'CKT', name = 'Cornwall Kerosene & Tar',
  sector = 'Oil & Refining', icon = 'itemtype-total-take',
  kind = 'share', start = 42.75,
  note = 'Refinery at Annesburg running at capacity.' },
```

`kind = 'bond'` adds a `coupon` field and drops the gain/loss reckoning. `icon` is
any RDR2 interface sprite the bundle ships.

{% hint style="warning" %}
A new security starts trading at `start`. Removing one that depositors already hold
leaves those holdings with nothing to price against — retire it by editing `start`
down instead, or accept that the holding disappears from the page.
{% endhint %}

---

## Loan Office

Secured and unsecured notes, repaid in weekly instalments.

```lua
Config.Credit = {
  autoDebit      = true,   -- false = repayment is purely manual
  instalmentDays = 7,      -- real days between instalments
  arrearsAfter   = 1,      -- missed instalments before the note is in arrears
  seizeAfter     = 3,      -- missed instalments before security is forfeit
  maxOpenNotes   = 3,
  startScore     = 60,
}
```

### The credit score

Every depositor starts at `startScore` and moves with their record. The score
decides which grade they are in, and the grade decides what the bank will advance and
at what rate.

| Grade | Score | Standing | Will advance | Rate |
| ----- | ----- | -------- | ------------ | ---- |
| A | 85+ | Sound Risk | $3,500 | −1.0% |
| B | 60+ | Fair Risk | $2,000 | — |
| C | 35+ | Doubtful Risk | $1,000 | +2.0% |
| D | 0+ | Poor Risk | $400 | +5.0% |

The same grade also words how the **summary page** describes the depositor to
themselves — "Depositor in Good Standing / Gold Register" at grade A, down to
"Standing in Question / Watched Register" at grade D.

### Terms

| Term | Rate |
| ---- | ---- |
| 8 weeks | 8.0% |
| 16 weeks | 9.5% |
| 26 weeks | 11.0% |
| 52 weeks | 13.5% |

The grade's `rateCut` is applied on top, then the security's `surcharge`.

### Security

```lua
collateral = {
  { id = 'none',   name = 'Unsecured',      value =   0.0, surcharge = 3.0, item = nil },
  { id = 'horse',  name = 'Horse Deed',     value = 850.0, surcharge = 0.0, item = 'horsedeed' },
  { id = 'gold',   name = 'Gold Bar',       value = 500.0, surcharge = 0.0, item = 'goldbar' },
  { id = 'jewels', name = 'Jewellery Case', value = 340.0, surcharge = 1.0, item = 'jewelry' },
}
```

An unsecured note costs 3 points more. Security lowers the rate, but it is **really
taken**: after `seizeAfter` missed instalments the item is removed from the
borrower's inventory.

{% hint style="info" %}
On standalone, or where `item = nil`, there is nothing physical to seize — the bank
claims the outstanding balance from the vault instead. It never silently lets the
debt go.
{% endhint %}

### Repayment

With `autoDebit = true` the server takes one instalment out of the vault every
`instalmentDays`. A vault that cannot cover it puts the note into arrears. Instalments
can also be paid by hand at any time from the page.
