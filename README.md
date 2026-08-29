---
description: >-
  A complete 1899 bank for RedM — teller, ledger, wires, drafts, certificates,
  a stock exchange, a loan office, safe deposit boxes and company accounts.
---

# CAS Banking

**CAS Banking** is a full bank, not a deposit menu. A depositor walks up to a teller
window, a short cinematic settles the camera over the banking hall, and an account
book opens: thirteen desks covering everything a bank in 1899 actually did.

Four branches are configured and ready on install. Adding your own is a matter of
editing one file.

{% hint style="info" %}
Nothing the interface reports is trusted. Balances, fees, surcharges, credit
grades, share prices and opening hours are all recalculated on the **server**
before a single dollar moves.
{% endhint %}

### Highlights

* **Thirteen desks** in one book — summary, ledger, statement, deposit, withdraw,
  wire transfer, bank drafts, certificates, stocks & bonds, loan office, deposit
  box, company account and the branch network.
* **A cinematic counter** — the customer sets a hand on the desk and the camera
  eases in over the hall, using the interior grade Rockstar authored for that very
  building.
* **A printed statement you can carry** — the clerk seals the sheet and hands over
  a real inventory item that can be read anywhere and given to somebody else.
* **A living exchange** — six securities that move on the server every 15 minutes,
  with brokerage, a daily cap and yearly bond coupons.
* **A loan office with teeth** — weekly instalments taken automatically, a credit
  score that moves with the record, and collateral that is really seized on default.
* **Chartered company accounts** — members, signing rights, a two-signature rule
  above a set sum, and a payroll.
* **A branch network** — each counter holds its own cash, charges its own surcharge
  to strangers, keeps its own hours, and can be shut or robbed.
* **Real RDR2 look** — genuine interface textures and the official RDR typefaces,
  no web chrome.
* **Every line translatable** — one locale file, nothing compiled.

### Framework compatibility

| Framework | Status |
| --------- | ------ |
| VORP Core | Supported |
| RSG Core | Supported |
| Standalone | Supported |

Framework and inventory are **detected at startup**, or can be pinned in
`config.lua`. Anything a framework cannot provide is simply not offered to the
player rather than failing — see [Requirements](requirements.md) for exactly what
each one unlocks.

### Storage

`oxmysql` is used when it is running and JSON files inside the resource when it is
not. Both work identically; see [Data and storage](data.md).

---

**Version 1.0.0** · RedM build 7290+

© CAS. Redistribution of this resource, in whole or in part, is not permitted.
