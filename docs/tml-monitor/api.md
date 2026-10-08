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

## exports.tml_monitor:allow (server)

```lua
exports.tml_monitor:allow(source, kind, seconds)
```

Lets one player do something that would otherwise be flagged, for a while: an arena or paintball script giving out
weapons that aren't inventory items, a game mode making players invincible, a minigame moving them. `seconds` defaults
to 10 and is capped at 3600. Returns `false` for an unknown kind or a player who isn't online.

| `kind` | Checks it covers |
| --- | --- |
| `movement` | speed, teleport, super jump |
| `godmode` | god mode, health or armour over the maximum |
| `weapons` | damage, hits with a weapon not held, hit rate, hit distance, weapon not in inventory, banned weapon, infinite ammo |
| `events` | game event spam, changing another player's weapons, projectiles, particle effects |
| `explosions` | explosion spam, hidden, boosted and banned explosions |
| `entities` | spawning too much, banned models |
| `visibility` | invisible, free camera, night or thermal vision |
| `all` | every check |

```lua
-- Paintball: weapons handed out directly, for the length of the round.
GiveWeaponToPed(GetPlayerPed(src), `WEAPON_PISTOL`, 250, false, true)
exports.tml_monitor:allow(src, 'weapons', 600)
```

## exports.tml_monitor:addZone / removeZone (server)

```lua
exports.tml_monitor:addZone(id, coords, radius)
exports.tml_monitor:removeZone(id)
```

An allowed area from a script, like the dashboard's *Allow this area*: the movement checks (speed, teleport, super
jump) don't run inside it. For areas that only exist while something is on, such as an event arena. `radius` is 5 to
1000 metres; adding with an existing `id` replaces it. Areas from scripts are forgotten when tml_monitor restarts.
Returns `false` for bad arguments, or (`removeZone`) an unknown id.

```lua
exports.tml_monitor:addZone('derby', vector3(-1234.5, -2345.6, 13.9), 120)
-- ...when the event ends:
exports.tml_monitor:removeZone('derby')
```

## Console command

`tml_monitor` in the server console (or for anyone with `add_ace group.admin command.tml_monitor allow`) prints the
version and where data goes, whether the dashboard is accepting it and what's waiting, the adapters in use, whether
the anti-cheat is on (and in test mode), and the device ID, fingerprint and screenshot settings.

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
| `anticheat` | `speed`, `teleport`, `super_jump`, `god_mode`, `health`, `damage`, `explosion_spam`, `explosion_hidden`, `entity_spam`, `event_spam`, `weapon_other`, `projectile`, `particle`, `client_silent`, `weapon_mismatch`, `hit_rate`, `hit_distance`, `weapon_spawned`, `weapon_banned`, `explosion_boosted`, `explosion_banned`, `entity_banned`, `event_watch`, `nui_devtools`, `resource_injected`, `infinite_ammo`, `invisible`, `freecam`, `vision`, `shared_ids`, `ocr_match` | `Config.AntiCheat` (needs OneSync); `shared_ids` and `ocr_match` come from the TML Monitor service. Flags with `client = true` were reported by the player's game. Alert flags may carry `shot`, a screenshot's id |
| `resource` | `monitor_start`, `monitor_stop` | always on |
