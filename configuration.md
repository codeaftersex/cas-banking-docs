---
description: Every setting in config.lua, in the order it appears.
---

# Config reference

Everything lives in `config.lua`, commented section by section. Nothing has to be
changed for the script to run.

## General

```lua
Config.Locale    = 'en'
Config.Framework = 'auto'   -- 'vorp' | 'rsg' | 'standalone'
```

| Key | Default | What it does |
| --- | ------- | ------------ |
| `Locale` | `'en'` | Which file in `locales/` supplies every line of text |
| `Framework` | `'auto'` | Detects `vorp_core` / `rsg-core` at startup, falling back to standalone. Pin it to skip detection |

## The prompt

```lua
Config.PromptControl  = 0x5181713D   -- INPUT_CONTEXT_A (Space)
Config.PromptLabel    = 'Bank Business'
Config.PromptHoldMs   = 800          -- 0 = plain press
Config.PromptDistance = 2.2
```

## Branches

`Config.BlipSprite` and `Config.Banks`. See [Branches](branches.md).

## The account

```lua
Config.Account = {
  limit        = 25000.0,
  interest     = 3.5,
  ledgerKeep   = 120,
  contactsMax  = 12,
  openingBonus = 0.0,
}

Config.TransferFee      = 0.02
Config.StandalonePocket = 100.0
```

| Key | Default | What it does |
| --- | ------- | ------------ |
| `limit` | `25000.0` | Deposit ceiling shown on the summary as vault capacity |
| `interest` | `3.5` | % per annum, accrued lazily per real day |
| `ledgerKeep` | `120` | Ledger entries kept per account |
| `contactsMax` | `12` | Recipients listed in the wire book before a search is needed |
| `openingBonus` | `0.0` | Vault balance granted to a brand-new account |
| `TransferFee` | `0.02` | Telegraph fee, as a fraction of the sum sent |
| `StandalonePocket` | `100.0` | Standalone only — pocket cash a fresh character starts with |

## The cinematic

`Config.Cinematic`. See [The cinematic](cinematic.md).

## The loan office

```lua
Config.Credit = {
  autoDebit      = true,
  instalmentDays = 7,
  arrearsAfter   = 1,
  seizeAfter     = 3,
  maxOpenNotes   = 3,
  startScore     = 60,
  grades     = { ... },
  terms      = { ... },
  collateral = { ... },
}
```

See [Investment → Loan Office](desks-investment.md#loan-office) for the grade,
term and collateral tables.

## Certificates

```lua
Config.Certificates = {
  penalty = 0.15,
  maxOpen = 6,
  minimum = 50.0,
  terms   = { ... },
}
```

## The exchange

```lua
Config.Market = {
  brokerage   = 0.015,
  tickMinutes = 15,
  drift       = 0.02,
  pull        = 0.06,
  dailyCap    = 250,
  historyKeep = 14,
  securities  = { ... },
}
```

## Bank drafts

```lua
Config.Drafts = {
  fee            = 1.5,
  expiryDays     = 30,
  maxOutstanding = 10,
}
```

## Company accounts

```lua
Config.Company = {
  charterFee     = 250.0,
  twoSignatures  = 500.0,
  wagesEveryDays = 7,
  autoWages      = false,
  bookKeep       = 40,
  rights = { deposit = ..., draw = ..., full = ... },
}
```

{% hint style="info" %}
`Config.Company.rights` supplies the printed words for a signing right. Those win
over the locale, so a server that renames "Full authority" to "Partner" only edits
this one table. Leave a key out and the locale wording is used instead.
{% endhint %}

## Acting for the bank

```lua
Config.Manager = {
  right = 'manager',   -- 'anyone' lets any depositor reopen a branch
  aces  = { 'vts.manager', 'command' },
  jobs  = { 'banker', 'bankmanager' },
}
```

## Statements

```lua
Config.Statement = {
  document = 'legalpaper',   -- nil = statement stays on screen only
  clerk    = 'E. Whitfield, Chief Teller',
  seal     = 'Valentine Trust & Savings Co.',
}
```

---

## Turning a desk off

There is no master switch per desk, and only one desk is ever removed from the
index.

**Deposit Box** is hidden outright when the bank cannot offer one — no inventory
running (`vorp_inventory` on VORP, `rsg-inventory` on RSG), standalone, or the branch
has `safebox = nil`. It is the only entry the index filters out.

Everything else stays in the index and is refused at the counter, with the counter
saying why:

| Situation | What the player gets |
| --------- | -------------------- |
| Branch has `telegraph = false` | Wire Transfer and Bank Drafts open, but the order is refused — "no telegraph office at this counter" |
| `Config.Statement.document = nil`, or no inventory | The statement is still drawn up and shown; only the carryable copy is missing, and the clerk says so |
| A list left empty (`securities`, `terms`, `collateral`) | The page opens on its own empty state rather than a blank panel |

{% hint style="info" %}
Refusing at the counter is deliberate. A desk that vanishes tells a player nothing;
a desk that opens and explains that this branch has no wire tells them to ride to one
that does.
{% endhint %}
