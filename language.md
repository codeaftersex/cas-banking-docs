---
description: Every line a player can see is in one file, and nothing is compiled.
---

# Language

Every line a player can see — what the bank writes in their book, what the counter
says when it refuses an order, and every label in the interface — is in
`locales/en.lua`.

## Translating

1. Copy `locales/en.lua` to `locales/<name>.lua`
2. Change `Locales['en']` on the first line to `Locales['<name>']`
3. Translate the strings
4. Set `Config.Locale = '<name>'`

{% hint style="success" %}
**Nothing is compiled.** The interface is handed its wording when a depositor opens
their book, so a translation reaches the screen on the next restart. You do not need
Node, and you do not need to touch `web/`.
{% endhint %}

## Placeholders

Anything in `{curly braces}` is filled in by the script. Keep the names; move them
wherever your language needs them.

```lua
insufficient = 'You are {short} short of {need}.'
insufficient = '{need} için {short} eksiğin var.'
```

Both work. A placeholder you delete simply does not appear; one you misspell is
printed as written, so a mistake is obvious in game rather than silent.

A key you leave out **falls back to English** rather than printing nothing, so a
half-finished translation degrades line by line instead of leaving blank labels.

## How the file is laid out

```lua
Locales['en'] = {
  ledger    = { ... },   -- entries written into a depositor's book
  statement = { ... },   -- how the printed statement sorts a period
  prompt    = 'Bank Business',
  branch    = { ... },   -- branch standings and posted hours
  account   = { ... },   -- words the server writes into a payload
  charter   = { ... },   -- the company book, as the server fills it in
  ui        = { ... },   -- every label in the interface
}
```

`ui` is the branch sent to the screen. Everything above it is written by the server —
into ledgers, onto items, into refusals.

### Ledger entries are stored

Lines under `ledger` are **written into the book at the moment they happen**.
Changing them renames future entries only; entries already written keep the wording
they were written with. That is deliberate — a book should not rewrite its own
history.

## Two things to watch

{% hint style="warning" %}
**The printed statement.** It sorts a period into five headings by matching **words**
against ledger descriptions:

```lua
statement = {
  buckets = {
    { label = 'Deposits received', kind = 'in',
      words = { 'Cash deposit', 'Account opening deposit' } },
    ...
  },
}
```

If you translate the ledger wording, translate `buckets[].words` to match — otherwise
entries fall outside their headings and the totals read zero. Keep the English
fragments in the list so books written before the translation still add up.
{% endhint %}

{% hint style="warning" %}
**The summary's interest figure.** The summary adds up interest by matching the start
of a ledger line against `ui.overview.interestPrefix`. Translate it to match your
`ledger.interest`, or the figure reads zero.
{% endhint %}

## Words that come from config, not the locale

Two tables in `config.lua` supply printed words and **win over the locale**, so a
server can rename them without touching a translation:

| Config | What it names |
| ------ | ------------- |
| `Config.Company.rights` | Deposit only / Draw & deposit / Full authority |
| `Config.Credit.grades[].standing` `.depositor` `.register` | Risk wording and the depositor's own standing |

## If you edit `locales/en.lua` itself

You do not have to — a translation is a new file, and English is the fallback either
way.

But if you change the **English** wording, the interface keeps a generated copy of it
at `web/src/locale.en.json` for the moment before the server answers. Regenerating it
needs the dev checker resource and a rebuild:

```
start _vts_syntaxcheck      # server console
vts_dumplocale              # writes locale.en.json into that resource
```

Copy the result to `web/src/locale.en.json`, then:

```bash
cd web
npm install
npm run build
```

{% hint style="info" %}
Skipping this is harmless in game — the server sends your wording and it wins. The
stale copy would only show in a browser opened straight on `web/dist`.
{% endhint %}
