---
description: The walk-up sequence between the counter prompt and the ledger opening.
---

# The cinematic

A depositor holds **Space** at a teller window. The customer sets a hand on the
desk, the camera eases in over the banking hall, the interior grade fades up, and
the book opens on that.

It runs **once, short, on purpose**. The counter is walked up to many times a
session, so the sequence is a single shot rather than a montage.

## Configuring it

```lua
Config.Cinematic = {
  enabled = true,

  skipControl  = 0x156F7119,  -- INPUT_FRONTEND_CANCEL (Backspace / Esc)
  skipGraceMs  = 350,

  letterbox    = true,
  barHeight    = 0.115,       -- fraction of screen height per bar
  postfx       = 'CamTransitionBlink',
  tcStrength   = 0.55,        -- interior grade blend; 1.0 is the full look

  clerkScenario = 'WORLD_HUMAN_VAL_BANKTELLER',
  ...
}
```

| Key | What it does |
| --- | ------------ |
| `enabled` | `false` opens the book instantly, with no camera work |
| `skipControl` | Key that skips the sequence. The prompt key itself also skips, but only once released |
| `skipGraceMs` | How long before the prompt key is armed as a skip |
| `letterbox` / `barHeight` | The bars, and how deep they come in |
| `postfx` | Screen effect played on the cut in |
| `tcStrength` | How strongly the branch's interior timecycle is blended |
| `clerkScenario` | Scenario the teller plays while the camera is up |
| `playerAnim` | The customer's idle at the counter and the stand-up on the way out |
| `sounds` | RDR2's own `Ledger_Sounds` cues for the cut, the settle and the exit |

## Shot positions

Camera positions are **not absolute**. Every offset is expressed in the clerk's own
frame:

* `f` — metres in **front** of the clerk, toward the customer
* `r` — metres to the clerk's **right**
* `u` — metres **above** the clerk's feet

One set of numbers therefore frames all four branches correctly, and re-aiming a
shot is a matter of nudging three floats.

```lua
shots = {
  { -- WIDE — high three-quarter over the banking hall, slow dolly in.
    ms   = 1600,
    from = { f = 4.60, r = 2.90, u = 2.55, fov = 45.0 },
    to   = { f = 3.70, r = 2.30, u = 2.25, fov = 42.0 },
    look = { f = 0.55, r = 0.00, u = 1.05 },
  },
}
```

| Key | What it does |
| --- | ------------ |
| `ms` | How long the shot runs |
| `from` / `to` | Where the camera starts and finishes, plus its field of view |
| `look` | The point it is aimed at, in the same frame |

### Adding a cut back

The runner plays as many shots as it is given. Add a second entry to `shots` and a
cut appears between them, with `sounds.cut` playing on it.

{% hint style="info" %}
Lower `fov` for a longer lens and a tighter, more compressed frame; raise it for a
wider, roomier one. RedM binds no depth-of-field natives, so focal length is what
shapes the look.
{% endhint %}

## Per-branch grading

Each branch names the interior timecycle Rockstar authored for that very building:

```lua
timecycle = 'val_bank_int',   -- Valentine
timecycle = 'bla_bank_int',   -- Blackwater
timecycle = 'new_com_bank_int', -- Saint Denis
timecycle = 'rho_bank_int',   -- Rhodes
```

That is why the same shot looks different in each hall. A branch with no
`timecycle` simply gets no grade.

## The prompt

```lua
Config.PromptControl  = 0x5181713D  -- INPUT_CONTEXT_A (Space)
Config.PromptLabel    = 'Bank Business'
Config.PromptHoldMs   = 800         -- 0 = a plain press instead of a hold
Config.PromptDistance = 2.2         -- how close before it appears
```

{% hint style="info" %}
`Config.PromptLabel` is the fallback. The label actually shown comes from
`prompt` in your locale file — see [Language](language.md).
{% endhint %}
