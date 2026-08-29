---
description: Where the bank keeps its books, and what is in them.
---

# Data and storage

`oxmysql` is used when it is running and JSON files inside the resource when it is
not. The choice is made once at startup and never mixed.

## With MySQL

### `vts_accounts`

One row per character.

| Column | Type | What it holds |
| ------ | ---- | ------------- |
| `id` | `VARCHAR(80)` | Character id, primary key |
| `holder` | `VARCHAR(64)` | Name printed on the book |
| `number` | `VARCHAR(16)` | Account number |
| `opened` | `VARCHAR(24)` | The day the account was opened |
| `vault` | `DOUBLE` | Balance in the vault |
| `pocket` | `DOUBLE` | Standalone pocket cash (unused on VORP/RSG) |
| `last_interest` | `BIGINT` | When interest was last credited |
| `ledger` | `LONGTEXT` | The book, as JSON |
| `safebox` | `LONGTEXT` | Slots rented per branch, as JSON |

### `vts_extra`

Per-account extras, keyed by the same character id: open notes, certificates, share
holdings and drafts made out to that depositor.

### `vts_world`

A single row holding everything shared between players:

* **market** — current prices, previous closings, chart history, tick counter
* **branches** — each counter's cash reserve and standing
* **companies** — every charter, its members and its book
* **drafts** — every live draft on the wire
* **seq** — the running numbers drafts, charters, notes and certificates are issued from

## Without MySQL

The same three things, as two files inside the resource:

```
cas-banking/data/accounts.json   -- accounts and their extras
cas-banking/data/world.json      -- the shared world state
```

{% hint style="info" %}
This is a fallback, not a lesser mode. Every desk behaves identically. It exists so
the script runs on a server with no database at all.
{% endhint %}

## Backing up

Both paths are plain JSON at heart — the `LONGTEXT` columns hold the same structures
the files do.

```sql
-- Everything the bank knows
SELECT * FROM vts_accounts;
SELECT * FROM vts_extra;
SELECT * FROM vts_world;
```

Copying `data/*.json`, or dumping those three tables, is a complete backup.

## Moving between the two

There is no migration built in. Switching from JSON to MySQL starts fresh books; the
JSON files are left untouched, so switching back restores them.

{% hint style="warning" %}
Decide which one you are running **before** players open accounts. Adding `oxmysql`
to a live server silently starts a second, empty set of books.
{% endhint %}

## What is safe to edit by hand

With the resource **stopped**, all of it. World state in particular is a reasonable
thing to edit — topping up a branch reserve, or setting a counter's standing:

```json
"branches": {
  "valentine": { "reserve": 42800.0, "status": "open", "note": "" }
}
```

Start the resource again and it reads what you wrote.

{% hint style="warning" %}
Do not edit while the resource is running. World state is held in memory and written
out on change, so your edit will be overwritten by the next save.
{% endhint %}
