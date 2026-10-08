## exports.tml_monitor:log (server)

Sends a log line to your TML Monitor dashboard from any server script.

```lua
local ok, reason = exports.tml_monitor:log(category, action, options)
```

| Param | Type | Notes |
| --- | --- | --- |
| `category` | string | 1–32 characters of `a-z`, `0-9`, `_`. Your own (`robbery`, `garage`) or a built-in one (`money`, `inventory`) |
| `action` | string | Same rules, e.g. `store_robbed` |
| `options.source` | number? | Player server id the line is about |
| `options.target` | number? | A second player (victim, receiver, ...) |
| `options.message` | string? | Up to 512 characters |
| `options.data` | table? | Extra facts, kept small: up to 32 keys, 3 levels deep, strings up to 256 characters, 4 KB in total |
| `options.level` | string? | `debug`, `info` (default), `warn` or `alert` |

Returns `true`, or `false` and the reason (bad name, bad level, or rate limited). The calling resource's name is
saved with the line.

```lua
exports.tml_monitor:log('robbery', 'store_robbed', {
  source = src,
  message = 'Robbed 24/7 on Grove St',
  data = { store = 'grove_247', payout = 4200 },
})
```

**Rate limit:** each resource can send a burst of 200 lines, then 50 a second. Lines past that are dropped and the
resource is named in the server console once a minute.

## exports.tml_monitor:exempt (server)

```lua
exports.tml_monitor:exempt(source, seconds)
```

Pauses the anti-cheat movement and health checks for one player, for scripts that move players on purpose (elevators,
apartments, jail, races). `seconds` defaults to 5 and is capped at 60. Returns `false` if the player isn't online.

```lua
SetEntityCoords(GetPlayerPed(src), 412.0, -1003.0, 29.0)
exports.tml_monitor:exempt(src, 5)
```

Staff never need this for admin-menu teleports, noclip or god mode from txAdmin, qbx_adminmenu or tml_adminpanel:
those are recognised already. To exempt someone from every check permanently, add them on the dashboard's Anti-cheat
page, or give them an ACE from `Config.AntiCheat.exemptAces` (`add_ace group.moderator tml_monitor.exempt allow`).

For scripts you can't or don't want to edit: allow the teleport route on the dashboard (open the teleport flag, then
*Allow this route*), or list an event the script already sends in `Config.AntiCheat.graceEvents`.

## Built-in categories

| Category | Actions | Config |
| --- | --- | --- |
| `connection` | `connect`, `join`, `drop` | `Config.Collect.connections` |
| `character` | `loaded`, `unloaded` | `Config.Logs.characters` |
| `money` | `add`, `remove`, `set` | `Config.Logs.money`, `Config.Logs.minMoney` |
| `banking` | `deposit`, `withdraw` (job/society accounts) | `Config.Logs.banking` |
| `inventory` | `move`, `stack`, `swap`, `give`, `add`, `buy`, `craft`, `use` | `Config.Logs.inventory`, `Config.Logs.itemUse` |
| `job` | `change`, `off_duty`, `gang` | `Config.Logs.jobs` |
| `status` | `downed`, `up`, `dead`, `revived`, `cuffed`, `uncuffed`, `jailed`, `released` | `Config.Logs.status` |
| `death` | `killed`, `died` | `Config.Logs.deaths` |
| `vehicle` | `enter`, `leave` (`_passenger` when enabled) | `Config.Logs.vehicles`, `Config.Logs.vehiclePassengers` |
| `admin` | `kick`, `ban`, `warn`, `heal`, `spawn_vehicle`, `teleport_to`, `bring`, `spectate`, `set_*`, ... (tagged with the menu) | `Config.Logs.admin` |
| `chat` | `message` | `Config.Logs.chat` (off by default) |
| `anticheat` | `speed`, `teleport`, `super_jump`, `god_mode`, `health`, `damage`, `explosion_spam`, `explosion_hidden`, `entity_spam`, `shared_ids` | `Config.AntiCheat` (needs OneSync); `shared_ids` comes from the TML Monitor service |
| `resource` | `monitor_start`, `monitor_stop` | always on |
