---
description: What the script needs, and what each optional dependency unlocks.
---

# Requirements

### Required

* A **RedM** server, build **7290** or newer.

That is the whole list. Every other dependency below is optional and detected at
startup — what is missing is simply not offered to the player.

### Optional

<table>
  <thead>
    <tr><th width="200">Resource</th><th>What it adds</th><th width="150">Without it</th></tr>
  </thead>
  <tbody>
    <tr>
      <td><code>oxmysql</code></td>
      <td>Accounts, world state and the company book stored in MySQL</td>
      <td>Stored in JSON files inside the resource</td>
    </tr>
    <tr>
      <td><code>vorp_core</code> or <code>rsg-core</code></td>
      <td>Character identity and pocket cash read from the framework</td>
      <td>Runs standalone and keeps pocket cash itself</td>
    </tr>
    <tr>
      <td><code>vorp_inventory</code></td>
      <td>Safe deposit boxes, loan collateral, the printed statement item</td>
      <td>Those three are hidden or fall back</td>
    </tr>
  </tbody>
</table>

### What each framework unlocks

| Feature | VORP | RSG | Standalone |
| ------- | ---- | --- | ---------- |
| Every account desk | ✅ | ✅ | ✅ |
| Pocket cash from the framework | ✅ | ✅ | — (kept internally) |
| Gold | ✅ | ✅ | — |
| Printed statement as an item | ✅ | ✅ | — (on screen only) |
| Loan collateral really seized | ✅ | ✅ | — (cash claim instead) |
| Safe deposit box | ✅ | — | — |

{% hint style="warning" %}
The **safe deposit box** is the one desk that is VORP-only. It is a real
`vorp_inventory` custom inventory, and there is no equivalent to register on RSG or
standalone, so the desk is hidden rather than faked. Everything else works on all
three.
{% endhint %}

### On standalone

With no framework running, the script keeps its own pocket cash. A fresh character
starts with `Config.StandalonePocket` (default `100.0`). Nothing else changes — the
ledger, the exchange, the loan office and the branch network all behave the same.

### Collateral items

The loan office seizes a real item on default. The four shipped options reference
`horsedeed`, `goldbar` and `jewelry`. If your inventory does not have those, either
rename them in `Config.Credit.collateral` or set `item = nil` on that row — a note
whose security has no item is claimed in cash instead.

See [Items and inventory](items.md) for the full list of items the script touches.
