Everything is in `config/config.lua`, except the Discord bot token, which is in `config/server.lua`. Every value is
checked when the resource starts: a missing or invalid value prints a warning in the console and its default is used
instead.

## General

| Option | Default | What it does |
|---|---|---|
| `Config.Locale` | `'en'` | Language file in `config/locales/` (`en`, `es`) |
| `Config.Debug` | `false` | Print every join, admission and drop |
| `Config.CheckForUpdates` | `true` | Console notice when a newer version exists |
| `Config.Roles` | `'auto'` | Where Discord roles come from: `discord` `badger_discord_api` `none` `custom` (`auto`: Badger_Discord_API if running, else the built-in bot if a token is set, else none) |

## `Config.Queue`

| Option | Type | Default | What it does |
|---|---|---|---|
| `maxSlots` | number | `0` | Player slots the queue fills. `0` = `sv_maxclients` |
| `reservedSlots` | number | `0` | Slots only priority players can take |
| `reservedMinPoints` | number | `50` | Priority points needed to use a reserved slot |
| `connectTimeout` | number | `180` | Seconds an admitted player has to finish loading before their slot is freed |
| `refreshSeconds` | number | `2` | How often the queue card updates (1-10) |
| `showPosition` | boolean | `true` | Show the position on the card |
| `showWait` | boolean | `true` | Show an estimated wait on the card |

## `Config.Grace`

| Option | Default | What it does |
|---|---|---|
| `enabled` | `true` | Players who drop get temporary priority |
| `minutes` | `5` | How long the grace lasts |
| `points` | `1000` | Priority during the grace |
| `onQuit` | `false` | Also give grace when the player quit on purpose |

## `Config.Requirements`

| Option | Default | What it does |
|---|---|---|
| `discord` | `false` | Discord must be running and linked to FiveM |
| `steam` | `false` | Steam must be running |
| `inGuild` | `false` | Must be a member of your Discord server (needs a roles bridge) |

## `Config.Whitelist`

| Option | Default | What it does |
|---|---|---|
| `mode` | `'off'` | `off`, `discord` (needs a role in `roles`), `database` (added by command or export), `either` |
| `roles` | `{}` | Discord role ids, as strings, that count as whitelisted |

## `Config.Priority`

A player's priority is the highest that applies to them. More points go first; equal points keep the order they
joined in.

```lua
Config.Priority = {
  roles = {
    ['123456789012345678'] = { points = 100, label = 'Staff' },
    ['234567890123456789'] = { points = 50, label = 'VIP' },
  },
  identifiers = {
    ['discord:123456789012345678'] = { points = 100, label = 'Owner' },
  },
  defaultLabel = 'Priority',
}
```

| Key | What it does |
|---|---|
| `roles` | Discord role id → `{ points, label }` |
| `identifiers` | Fixed priority for an identifier (`license:`, `discord:`, `steam:`, `fivem:`) |
| `defaultLabel` | Label for database priority added without one |

Timed priority from the database (`queue_addprio` or the `AddPriority` export) works the same way.

## `Config.Card`

| Option | What it does |
|---|---|
| `serverName` | Shown at the top of the card |
| `logo` | `https` URL of a square logo, `''` for none |
| `buttons` | Up to 3 `{ label, url }` link buttons |
| `tips` | Strings; one is shown at random each refresh |

## `Config.Commands`

| Option | Default | What it does |
|---|---|---|
| `ace` | `'tml_queue.admin'` | ACE permission for using the commands in game |
| `queue` | `'queue'` | List the queue |
| `addPriority` | `'queue_addprio'` | Give priority |
| `removePriority` | `'queue_removeprio'` | Remove priority |
| `whitelist` | `'queue_whitelist'` | Add or remove from the database whitelist |
| `skip` | `'queue_skip'` | Move someone to the front |

## `Config.Discord` (config/server.lua)

| Option | Default | What it does |
|---|---|---|
| `token` | `''` | Bot token for the built-in roles bridge |
| `guild` | `''` | Your Discord server id |
| `cacheMinutes` | `5` | How long a player's roles are remembered |
