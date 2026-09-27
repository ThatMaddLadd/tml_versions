Everything customers may change is in `config/config.lua` (never encrypted). Text shown to players is in
`config/locales/*.lua`. Restart the resource after editing.

## General

| Option | Default | Meaning |
|---|---|---|
| `Config.Locale` | `'en'` | Language file from `config/locales`. English and Spanish are included. Missing keys fall back to English. |
| `Config.Debug` | `false` | Verbose console output. Leave off in production. |
| `Config.CheckForUpdates` | `true` | Prints a console notice when a newer version exists. |
| `Config.TestMode` | `false` | Lets you message and save your own pager number, for testing alone. Turn off on live servers. |

## Bridge

`'auto'` detects what is running (Qbox → QBCore → ox_core → ND_Core → ESX → vRP → standalone for the framework; a
similar fixed order for inventory). Force a value to override, and make sure `tml_pager` starts after your framework and
inventory.

| Option | Default | Values |
|---|---|---|
| `Config.Framework` | `'auto'` | `'qbox'`, `'qbcore'`, `'esx'`, `'ox_core'`, `'nd_core'`, `'vrp'`, `'standalone'`, `'custom'` |
| `Config.Inventory` | `'auto'` | `'ox_inventory'`, `'qb-inventory'`, `'ps-inventory'`, `'qs-inventory'`, `'codem-inventory'`, `'core_inventory'`, `'standalone'`, `'custom'` |
| `Config.Target` | `'auto'` | `'ox_target'`, `'qb-target'`, `'none'`, `'custom'` |
| `Config.Notify` | `'auto'` | `'ox_lib'`, `'qbcore'`, `'esx'`, `'standalone'`, `'custom'` |

### Standalone jobs

Standalone framework only: maps a job name to the ACE permission that grants it.

```
add_ace group.police tml.job.police allow
```

`Config.Standalone.jobs` ships with `police`, `ambulance`, `fire` and `dispatch` mapped to `tml.job.<name>`. Add or
rename entries to match the jobs you use.

## Pager

| Option | Default | Meaning |
|---|---|---|
| `Config.Pager.item` | `'tml_pager'` | Inventory item name. |
| `Config.Pager.requireItem` | `false` | `true` = players must carry the item to use the pager. `false` = everyone can use `/pager` or the keybind. |
| `Config.Pager.command` | `'pager'` | Chat command that opens the pager, or `false` to disable it. |
| `Config.Pager.key` | `'F4'` | Default keybind (players can rebind it in GTA settings), or `false` to disable it. |
| `Config.Pager.messageTone` | `'tone_message'` | Sound file in `web/sounds` (no extension) played for incoming player messages. |

## Messages and contacts

Every character gets a pager number. Messages are stored so they arrive even if the receiver was offline. Requires
oxmysql; tables are created automatically.

| Option | Default | Meaning |
|---|---|---|
| `Config.Messages.numberDigits` | `6` | Length of pager numbers (4–10). |
| `Config.Messages.maxLength` | `80` | Characters per message. |
| `Config.Messages.maxStored` | `30` | Messages kept per pager; oldest are removed. |
| `Config.Messages.maxContacts` | `50` | Saved contacts per player. |
| `Config.Messages.contactNameLength` | `20` | Characters in a contact name. |
| `Config.Messages.sendCooldown` | `3000` | Milliseconds between messages from one player. |

## Dispatch

| Option | Default | Meaning |
|---|---|---|
| `Config.Dispatch.maxHistory` | `25` | Alerts kept per responder in the UI. |
| `Config.Dispatch.maxMessageLength` | `180` | Characters per alert. |
| `Config.Dispatch.sendCooldown` | `2000` | Milliseconds between alerts sent by one dispatcher. |
| `Config.Dispatch.requireOnDuty` | `true` | Only on-duty responders receive alerts. |

`Config.Dispatchers` controls who may **send** department alerts: a table of `jobs` (job names), `minGrade` (minimum
grade in those jobs) and an `ace` fallback permission (`tml_pager.dispatch` by default, or `false` to disable the ACE
check).

`Config.Departments` controls who may be **alerted**: each key is a department id (`police`, `ems`, `fire` out of the
box) with a `label`, the `jobs` that receive its pages, an `accent` colour for the UI and a `tone` sound file in
`web/sounds` (no extension). Add your own department by adding another entry.

## Theme

| Option | Default | Meaning |
|---|---|---|
| `Config.Theme.position` | `'bottom-right'` | `'top-left'`, `'top-right'`, `'bottom-left'`, `'bottom-right'`, `'center'` |
| `Config.Theme.sounds` | `true` | UI and tone sounds on/off. |
| `Config.Theme.volume` | `0.5` | `0.0`–`1.0`. |
| `Config.Theme.vars` | `{}` | Any CSS variable from `web/theme.css`, e.g. `{ ['--accent'] = '#A855F7' }`. |

Shell, LCD and button colours live in `web/theme.css`; the file has ready-made alternatives in comments (blue or
graphite shell, amber backlight) you can uncomment instead of writing your own.
