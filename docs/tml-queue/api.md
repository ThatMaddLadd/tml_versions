Server side only. Identifiers are strings such as `discord:123456789012345678`, `license:abc...`, `steam:110000...`
or `fivem:123456`. Exports are stable within a major version.

## Exports

| Export | Returns | What it does |
|---|---|---|
| `GetQueueSize()` | `number` | How many players are waiting |
| `GetQueue()` | `table[]` | `{ name, points, label, waited }` per waiting player, in queue order |
| `GetPosition(identifier)` | `number|nil` | Queue position of the player with this identifier |
| `AddPriority(identifier, points, days?, label?, note?)` | `boolean` | Adds or replaces database priority. No `days` = permanent |
| `RemovePriority(identifier)` | `boolean` | `true` when something was removed |
| `AddWhitelist(identifier, note?)` | `boolean` | Adds to the database whitelist |
| `RemoveWhitelist(identifier)` | `boolean` | `true` when something was removed |
| `IsWhitelisted(identifier)` | `boolean` | On the database whitelist |

```lua
-- Give a player who boosted your Discord 25 points for 7 days
exports.tml_queue:AddPriority('discord:123456789012345678', 25, 7, 'Booster')
```

## Events

Fired with `TriggerEvent` on the server. They aren't net events.

### `tml_queue:admitted`

`(license, name, secondsWaited)` when a player leaves the queue and starts loading in.

```lua
AddEventHandler('tml_queue:admitted', function(license, name, waited)
  print(('%s got in after %d seconds'):format(name, waited))
end)
```

## Bridge

The roles adapter exposes one function (see `bridge/README.md`). Other resources shouldn't call it.
