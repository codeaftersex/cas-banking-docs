---
description: Three steps, and nothing to configure before the first start.
---

# Installation

### 1. Drop the resource in

Put the `cas-banking` folder into your resources directory. Keep the folder name
exactly as it is — the resource refers to itself by name when it reads and writes
its own files.

### 2. Start it

```cfg
ensure cas-banking
```

Start it **after** your framework and `oxmysql`, so both are already running when
the bank detects them.

### 3. Start the server once

Tables are created automatically on first start. Nothing needs to be configured for
the script to run.

{% hint style="success" %}
Walk up to the Valentine teller and hold **Space**. If the book opens, you are done.
{% endhint %}

---

## What gets created

### With `oxmysql`

Three tables, created on first start if they are not already there:

| Table | What it holds |
| ----- | ------------- |
| `vts_accounts` | One row per character: holder, account number, vault, pocket, ledger, safe box slots |
| `vts_extra` | Per-account extras — notes, certificates, share holdings, drafts made out to them |
| `vts_world` | One row: the market, branch reserves and standings, company charters, live drafts |

### Without `oxmysql`

The same three things, as two JSON files inside the resource:

```
cas-banking/data/accounts.json
cas-banking/data/world.json
```

{% hint style="info" %}
The JSON path is the fallback, not a lesser mode — every desk behaves identically.
It exists so the script runs on a server with no database at all.
{% endhint %}

---

## The statement item

The printed statement is handed over as an inventory item. By default it uses
`legalpaper`:

```lua
Config.Statement = {
  document = 'legalpaper',
  ...
}
```

Point it at any item that exists in your inventory table, or set it to `nil` to keep
the statement on screen only. Nothing else needs registering — the script registers
the item as usable itself.

See [Items and inventory](items.md).

---

## Upgrading

Replace the folder and restart. Account data lives in the database or in
`data/`, not in the resource files, so nothing is lost.

{% hint style="warning" %}
If you edited `config.lua` or a file in `locales/`, keep a copy before replacing the
folder.
{% endhint %}
