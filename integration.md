---
description: What other resources can and cannot reach, and how a robbery script fits.
---

# Integrating other scripts

## What the bank exposes

{% hint style="warning" %}
**Version 1.0.0 registers no exports.** Every event the server listens on is a
player-facing one, triggered by the interface and validated against the caller's own
account. There is deliberately no event another resource can fire to move somebody
else's money.
{% endhint %}

That is a security decision, not an oversight: a bank whose balances can be changed
by any resource on the server is not a bank. The seam that *is* open is the shared
world state.

## The branch network

A branch carries two things another script has a legitimate interest in:

| Field | What it is |
| ----- | ---------- |
| `reserve` | Cash the counter holds. Withdrawals draw it down, deposits refill it |
| `status` | `open` · `guarded` · `robbed` · `closed` |

A counter that is `robbed` or `closed` **pays out nothing**, and the branch network
page then offers to put it back on its feet to anyone holding the `vts.manager` ace or
a job in `Config.Manager.jobs`.

Both live in world state — `vts_world` in MySQL, or `data/world.json` — under the
branch key:

```json
"branches": {
  "valentine": { "reserve": 42800.0, "status": "robbed", "note": "Vault emptied Aug 9th." }
}
```

`note` overrides the standing's default line on the network page. Leave it empty and
the page words the standing itself, in the server's own language.

### Driving it from a robbery script

{% hint style="danger" %}
There is **no supported runtime hook** for this in 1.0.0. World state is held in
memory by `cas-banking` and written out on change, so a robbery script that edits
`vts_world` or `data/world.json` while the resource is running will have its edit
overwritten by the next save.
{% endhint %}

Until an export ships, the workable options are:

1. **Set the standing between sessions.** Edit world state with the resource stopped,
   or on a scheduled restart. Suitable for scripted events and downtime, not for a
   robbery happening live.
2. **Ask CAS for the export.** Reading a branch and setting its standing is a small
   addition and can be provided on request — get in touch with what your robbery
   script needs and it can be added to the next build.

## Framework bridge

The bank talks to VORP and RSG through a single layer in `server/main.lua`, wrapping:

* character identity
* pocket cash and gold
* adding, removing and registering usable items

Adapting to another framework means editing that layer rather than hunting through
every desk. Nothing framework-specific lives in `credit.lua`, `market.lua` or
`institution.lua`.

## The interface

The interface is a React app in `web/`. The built bundle in `web/dist` is what ships
and what the game loads — **you do not need Node to run this script.**

To change layout, colours or anything beyond wording:

```bash
cd web
npm install
npm run build
```

For wording, use `locales/` instead — no build required. See
[Language](language.md).

### How wording reaches the screen

The server sends the whole `ui` branch of your locale in the payload that opens a
depositor's book. The interface renders from that, falling back key by key to a
generated English copy at `web/src/locale.en.json`. Nothing in the bundle is
hardcoded English.
