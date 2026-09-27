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
