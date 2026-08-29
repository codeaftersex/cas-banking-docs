---
description: Section I — the summary, the ledger and the printed statement.
---

# The account

The first three entries in the book's index. Nothing here moves money; it is the
record.

## Account Summary

The opening page. Total declared holdings (vault plus what is on hand), vault
capacity against `Config.Account.limit`, the depositor's standing and register, what
came in and went out this month, interest earned, and the most recent entries.

**Standing** and **register** are not fixed text — they are read from the credit
grade the depositor currently holds, so somebody three instalments in arrears is not
told they are in good standing. See [Investment](desks-investment.md#loan-office).

```lua
Config.Account = {
  limit        = 25000.0,  -- deposit ceiling shown on the summary
  interest     = 3.5,      -- % per annum
  ledgerKeep   = 120,      -- entries kept per account
  contactsMax  = 12,       -- recipients listed in the wire book
  openingBonus = 0.0,      -- vault balance granted to a new account
}
```

### Interest

Accrued **lazily, per real day**. There is no thread ticking over every account —
interest is worked out the moment an account is next touched, from how many days
have passed since it was last credited. A server that has been down for a week pays
the week's interest on the first login.

## The Ledger

Every entry the bank has written, newest first, with a running balance. Filterable by
**All entries**, **Received** and **Paid out**, with totals for the page and the
number of entries recorded.

Each row carries the window or office it was booked against — Window 1, Telegraph,
Exchange, Loan Office, Payroll, Vault — so a line can be traced back to the desk that
wrote it.

{% hint style="info" %}
`Config.Account.ledgerKeep` caps how many entries are kept per account (120 by
default). Older lines fall off the end. Raising it makes accounts bigger in storage;
nothing else changes.
{% endhint %}

## Printed Statement

The period summed, sealed and handed over.

The clerk closes the current period, totals it under five headings — deposits
received, withdrawals paid, wires sent, interest credited, charges and fees — and
opens a new period from today's figure. **It is free.**

### The physical copy

The sealed sheet is also handed over as a real inventory item. It lands in the
depositor's satchel, and using it opens the sheet on screen — readable anywhere, and
passable to somebody else.

The figures are **sealed into the item** at the moment it is drawn up. A statement
handed to another player still reads the balances it was printed with, not theirs.

```lua
Config.Statement = {
  document = 'legalpaper',                    -- nil = on screen only
  clerk    = 'E. Whitfield, Chief Teller',
  seal     = 'Valentine Trust & Savings Co.',
}
```

{% hint style="warning" %}
The statement sorts entries into its five headings by matching **words** against
ledger descriptions. If you translate the ledger wording, translate
`statement.buckets[].words` to match — otherwise entries fall outside their headings
and the totals read zero. See [Language](language.md).
{% endhint %}

### Without an inventory

On standalone, or with `document = nil`, the statement is drawn up and shown on
screen exactly the same. Only the carryable copy is missing, and the counter says so
rather than pretending it handed one over.
