---
description: Moving a counter, adding one, or building your own network.
---

# Branches

Four branches ship configured, with coordinates, clerks and hours taken from
`vorp_banking` so the counters line up with a stock VORP setup.

| Key | Bank | Region | Surcharge | Reserve | Boxes |
| --- | ---- | ------ | --------- | ------- | ----- |
| `valentine` | Valentine Trust & Savings | The Heartlands | — | $42,800 | ✅ |
| `blackwater` | Blackwater Federal | Great Plains | 2.0% | $96,200 | ✅ |
| `saintdenis` | Lemoyne National | Bayou Nwa | 1.5% | $186,500 | ✅ |
| `rhodes` | Rhodes Savings Bank | Scarlett Meadows | 1.5% | $18,400 | — |

## Anatomy of a branch

```lua
{
  key = 'valentine',
  name = 'Valentine Trust & Savings',
  branch = 'Valentine · Heartlands',
  region = 'The Heartlands',

  coords = vector3(-308.02, 773.82, 116.7),
  npc = { model = 's_m_m_bankclerk_01',
          pos = vector4(-308.02, 773.82, 116.7, 18.69) },
  blip = true,

  hours = { open = 7, close = 22 },
  fee = 0.0,
  reserve = 42800.0,
  telegraph = true,
  safebox = { slots = 10, maxSlots = 100, slotCost = 10, weapons = true },
  timecycle = 'val_bank_int',
},
```

<table>
  <thead><tr><th width="150">Key</th><th>What it does</th></tr></thead>
  <tbody>
    <tr><td><code>key</code></td><td>Internal id. Must be unique and must not change once accounts exist — it is what a home branch and a safe box are stored against</td></tr>
    <tr><td><code>name</code></td><td>The house name, printed across the top of the book and on every draft</td></tr>
    <tr><td><code>branch</code></td><td>The printed address. The part before <code>·</code> is used as the town on a draft</td></tr>
    <tr><td><code>region</code></td><td>Shown on the branch network page</td></tr>
    <tr><td><code>coords</code></td><td>The teller counter — where the prompt appears</td></tr>
    <tr><td><code>npc</code></td><td>Clerk model and position. <code>nil</code> = no clerk spawned</td></tr>
    <tr><td><code>blip</code></td><td><code>true</code> puts a bank blip on the map</td></tr>
    <tr><td><code>hours</code></td><td><code>{ open, close }</code> in game hours. <code>nil</code> = always open</td></tr>
    <tr><td><code>fee</code></td><td>% surcharge for anyone whose home branch this is not</td></tr>
    <tr><td><code>reserve</code></td><td>Cash the counter starts with. Withdrawals draw it down</td></tr>
    <tr><td><code>telegraph</code></td><td><code>false</code> = no wire, so no wires or drafts are drawn here</td></tr>
    <tr><td><code>safebox</code></td><td>Box terms, or <code>nil</code> for a branch that keeps none</td></tr>
    <tr><td><code>timecycle</code></td><td>Interior grade pushed during the cinematic</td></tr>
  </tbody>
</table>

## Adding a branch

Copy an entry, give it a fresh `key`, and set `coords` to your teller counter.

```lua
{
  key = 'annesburg', name = 'Annesburg Colliery Bank',
  branch = 'Annesburg · Roanoke Ridge', region = 'Roanoke Ridge',
  coords = vector3(2931.5, 1279.9, 44.5),
  npc = { model = 's_m_m_bankclerk_01',
          pos = vector4(2931.5, 1279.9, 44.5, 180.0) },
  blip = true,
  hours = { open = 9, close = 17 },
  fee = 2.5, reserve = 11900.0, telegraph = false,
  safebox = nil,
  timecycle = nil,
},
```

That branch is now on the network page, on the map, and open for business. Nothing
else needs registering.

{% hint style="info" %}
The clerk's `pos` is separate from `coords` on purpose. `coords` is where the player
must stand for the prompt; `pos` is where the clerk stands and which way they face.
At Blackwater and Saint Denis the two are metres apart.
{% endhint %}

### Where the camera looks

Shot offsets are expressed in the **clerk's own frame**, so a new branch is framed
correctly by the existing shot as long as `npc.pos` faces the customer. If a hall is
unusually tight or open, nudge `Config.Cinematic.shots` — see
[The cinematic](cinematic.md#shot-positions).

## Blips

```lua
Config.BlipSprite = -2128054417  -- blip_proc_bank
```

One sprite for every branch with `blip = true`.

## Removing a branch

Delete the entry. Accounts whose home branch it was still work — every desk falls
back to the first branch in the list — but nobody can walk up to that counter any
more, and safe box contents stored against that key become unreachable.

{% hint style="warning" %}
Change a `key` and you have effectively removed one branch and added another. Home
branches and safe boxes stored against the old key are orphaned. Rename `name` and
`branch` freely; leave `key` alone.
{% endhint %}

## Reserves and the economy

A branch's reserve is real. It goes down as depositors withdraw and up as they pay
in, and it is stored in world state so it survives a restart.

Set a low reserve on a remote counter and it genuinely runs dry, which is what the
network page is for — it tells a player where the cash is. Set a high one on a city
branch and it will always pay.
