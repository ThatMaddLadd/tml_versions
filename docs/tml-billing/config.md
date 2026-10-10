Everything is in `config/config.lua`. Every value is checked when the resource starts: a missing or invalid value
prints a warning in the server console and its default is used instead. A business with a broken entry is skipped
with a warning.

## General and bridge

| Option | Default | What it does |
|---|---|---|
| `Config.Locale` | `'en'` | Language file in `config/locales/` (`en`, `es`) |
| `Config.Debug` | `false` | Extra console output |
| `Config.CheckForUpdates` | `true` | Console notice when a newer version exists |
| `Config.Framework` | `'auto'` | `qbox` `qbcore` `esx` `ox_core` `nd_core` `vrp` `standalone` `custom` |
| `Config.Notify` | `'auto'` | `ox_lib` `qbcore` `esx` `standalone` `custom` |
| `Config.Banking` | `'auto'` | `tml_banking` `renewed-banking` `okokBanking` `qs-banking` `fd_banking` `tgg-banking` `snipe-banking` `pefcl` `qb-banking` `esx_banking` `framework` `custom` |

## `Config.Billing`

| Option | Type | Default | What it does |
|---|---|---|---|
| `command` | string | `'invoices'` | Chat command that opens the window (`''` for none) |
| `key` | string | `''` | Default key that opens it (`''`: players bind it themselves) |
| `currency` | string | `'$'` | Shown before amounts |
| `account` | string | `'bank'` | Account invoices are paid from: `bank` or `cash` |
| `allowCash` | boolean | `false` | Players may also pay with cash |
| `sendDistance` | number | `5.0` | Max metres between the sender and the person billed |
| `maxItems` | number | `10` | Line items per invoice |
| `maxQuantity` | number | `99` | Max quantity per line |
| `maxAmount` | number | `1000000` | Highest total for one invoice (businesses can set lower) |
| `itemLength` | number | `40` | Max characters in a line description |
| `noteLength` | number | `200` | Max characters in the note |
| `dueDays` | number | `7` | Days until an invoice is overdue |
| `keepDays` | number | `60` | Paid, declined and cancelled invoices older than this are deleted |
| `listSize` | number | `50` | Invoices shown per list |

## `Config.LateFees`

| Option | Default | What it does |
|---|---|---|
| `enabled` | `true` | Late fees on overdue invoices |
| `percentPerDay` | `2` | % of the amount added per full day overdue |
| `maxPercent` | `20` | The fee never goes above this % |

## `Config.AutoPay`

| Option | Default | What it does |
|---|---|---|
| `enabled` | `false` | Charge overdue invoices automatically while the player is online and can afford them |
| `afterDays` | `3` | Days overdue before auto-pay |
| `checkMinutes` | `10` | How often online players are checked |

## `Config.Tax`

| Option | Default | What it does |
|---|---|---|
| `percent` | `0` | % of each business invoice taken as tax (out of what the business receives) |
| `account` | `false` | Shared account that receives the tax, e.g. `'government'`. `false` = removed from the economy |

## `Config.Personal`

| Option | Default | What it does |
|---|---|---|
| `enabled` | `true` | Anyone can bill a nearby player; the money goes to the sender |
| `maxAmount` | `50000` | Highest total for a personal invoice |
| `allowDecline` | `true` | The person billed may decline |

## `Config.Businesses`

Key = job name. Members of that job at `minGrade` or above can send invoices for it.

```lua
mechanic = {
  label = 'LS Customs', society = 'mechanic', minGrade = 0, manageGrade = 3, commission = 15,
  maxAmount = 100000, allowDecline = true, kind = 'invoice',
  presets = { { label = 'Repair kit', price = 350 }, { label = 'Tow', price = 500 } },
},
```

| Key | Type | What it does |
|---|---|---|
| `label` | string | Shown on invoices |
| `society` | string | Shared account that receives payments (usually the job name) |
| `minGrade` | number | Lowest grade that can send invoices |
| `manageGrade` | number | Lowest grade that sees every invoice of the business and can cancel any unpaid one |
| `commission` | number | % of each paid invoice that goes to the employee who sent it |
| `maxAmount` | number | Highest total for one invoice from this business |
| `allowDecline` | boolean | The person billed may decline (use `false` for fines) |
| `kind` | `'invoice'` or `'fine'` | Changes the wording players see |
| `presets` | list | `{ label, price }` one-click line items |

How a paid business invoice is split: the employee gets `commission`% of the amount, tax takes `Config.Tax.percent`%
of the amount, and the business gets the rest, including any late fee.

## `Config.Theme`

| Option | Default | What it does |
|---|---|---|
| `sounds` | `true` | UI sounds |
| `volume` | `0.5` | 0.0-1.0 |
| `vars` | `{}` | Any CSS variable from `web/theme.css`, e.g. `{ ['--accent'] = '#3B82F6' }` |

## `Config.RateLimit`

Milliseconds between requests of one kind from one player: `default` (150), `open` (750), `write` (1000).
