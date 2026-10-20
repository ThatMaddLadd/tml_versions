Everything here is server side unless noted. Exports are stable within a major version.

## Exports

### `BuildBanCard(banId, displayName, reason, bannedAt, unbanAt) -> string`

Returns the Adaptive Card JSON (schema 1.0) used on the ban screen.

| Param | Type | Notes |
|---|---|---|
| `banId` | number | Row id of the ban |
| `displayName` | string? | Name stored with the ban |
| `reason` | string? | Shown on the card |
| `bannedAt` | string? | `YYYY-MM-DD HH:MM:SS` |
| `unbanAt` | string? | Same format. `nil` = permanent |

```lua
local card = exports.tml_adminpanel:BuildBanCard(12, 'Name', 'Cheating', '2026-09-25 12:00:00', nil)
deferrals.presentCard(card)
```

### `BuildBanText(banId, displayName, reason, bannedAt, unbanAt) -> string`

Same arguments. Returns the plain-text version of the ban screen.

### `GetBanScreen(banId, displayName, reason, bannedAt, unbanAt) -> table`

Returns `{ mode = 'card', card = <json>, text = <string> }` or `{ mode = 'text', text = <string> }`, following
`Config.BanScreenMode`.

### `RemoteBan(target, reason, durationSeconds, byName) -> boolean, string`

Bans an online player (`target` = server id) or an identifier (`'license2:…'`, `'discord:…'`; every identifier the
framework has stored for that account is banned too). `durationSeconds` 0 is permanent. Returns `true, 'BAN-…'` or
`false, why`. Logged in the panel as `byName`. Used by TML Monitor's dashboard actions.

```lua
local ok, ref = exports.tml_adminpanel:RemoteBan(12, 'Cheating', 7 * 86400, 'Richy (TML Monitor)')
```

### `RemoteUnban(identifier, byName) -> boolean, string?`

Lifts the active ban matching an identifier. Returns `true`, or `false, why`.

```lua
exports.tml_adminpanel:RemoteUnban('license2:abc123…', 'Richy (TML Monitor)')
```

### `RemoteFreeze(targetId, frozen, byName) -> boolean, string?`

Freezes (`frozen = true`) or unfreezes an online player, the same freeze as the panel's button.

```lua
exports.tml_adminpanel:RemoteFreeze(12, true, 'Richy (TML Monitor)')
```

### `RemoteUnbanId(banId, byName) -> boolean, string?`

Lifts an active ban by its number (`12`) or ref (`'BAN-00012'`). Returns `true`, or `false, why`.

```lua
exports.tml_adminpanel:RemoteUnbanId('BAN-00012', 'Richy (TML Monitor)')
```

### `RemoteBans(limit) -> table[]`

The active bans, newest first (`limit` defaults to 500, at most 1000). Each is `{ id, ref, name, reason, by,
banned_at, unban_at, identifier }`; times are unix seconds, `unban_at` is `nil` for a permanent ban and `identifier`
is the first one stored (a licence when there is one).

```lua
for _, ban in ipairs(exports.tml_adminpanel:RemoteBans(100)) do print(ban.ref, ban.name, ban.reason) end
```

### `RemoteKick(targetId, reason, byName) -> boolean, string?`

Kicks an online player. Returns `true`, or `false, why`.

```lua
exports.tml_adminpanel:RemoteKick(12, 'AFK in a busy spot', 'Richy (TML Monitor)')
```

### `RemoteWarn(targetId, reason, byName) -> boolean, number|string`

Warns an online player's character. The warning is kept in the panel like one given in game and shown to the player.
Returns `true` and their warning count, or `false, why`.

```lua
local ok, count = exports.tml_adminpanel:RemoteWarn(12, 'Fail RP', 'Richy (TML Monitor)')
```

### `RemoteMedical(targetId, kind, byName) -> boolean, string?`

Revives (`kind = 'revive'`) or heals (`'heal'`) an online player, the same as the panel's buttons.

```lua
exports.tml_adminpanel:RemoteMedical(12, 'revive', 'Richy (TML Monitor)')
```

### `RemoteAnnounce(message, byName) -> boolean, string?`

Shows an announcement to everyone, the same as the panel's Announce.

```lua
exports.tml_adminpanel:RemoteAnnounce('Restart in 10 minutes.', 'Richy (TML Monitor)')
```

### `captureScreenshot(targetId, opts) -> table?, string?`

Captures a player's screen and stores it in the evidence gallery. Needs screenshot-basic and the upload key.

| Param | Type | Notes |
|---|---|---|
| `targetId` | number | Server id of a connected player |
| `opts.adminId` | string? | Recorded as the actor. Default `0` |
| `opts.adminName` | string? | Default `System` |
| `opts.reason` | string? | Stored with the capture |
| `opts.pinned` | boolean? | Pinned captures never expire |

Returns the saved row (`id`, `url`, `targetCharId`, `targetName`, `metadata`), or `nil` and an error message.

## Server events

Triggered by the panel for your own scripts to listen to. They are not net events, so clients cannot fire them.

### `tml_adminpanel:actionLogged`

`(adminSource, action, targetName, details, targetId)`. Fired for every staff action the panel writes to its own
action log (kick, ban, warn, money, groups, teleports, vehicles, screenshots...). `adminSource` is the staff member's
server id, `action` the log's action name (for example `ban`, `money_cash`, `toggle_noclip`), `targetId` the target's
account or character id when there is one. TML Monitor listens to this to show panel actions on its dashboard.

```lua
AddEventHandler('tml_adminpanel:actionLogged', function(adminSource, action, targetName, details)
  print(('%s used %s on %s: %s'):format(GetPlayerName(adminSource), action, targetName or '-', details or ''))
end)
```

### `tml_adminpanel:characterRenamed`

`(charId, firstName, lastName, source)`. Fired after a staff member renames a character. `source` is the player's server
id when they are online, otherwise `nil`. Use it to refresh names in other resources.

### `tml_adminpanel:selfVehicleSpawned`

`(source, vehicleEntity, plate)`. Fired after staff spawn a vehicle for themselves with the Admin Tools tab. Use it to
hand out vehicle keys.

```lua
AddEventHandler('tml_adminpanel:selfVehicleSpawned', function(source, vehicle, plate)
  exports['my_keys']:GiveKeys(source, plate)
end)
```

## Bridge

Adapters expose a fixed set of functions (see `bridge/README.md`). Other resources should not call them; use your
framework's own API instead.
