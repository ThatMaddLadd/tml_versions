Frequencies are numbers with up to two decimals (`145.5`). Exports are stable within a major version.

## Server exports

### `GetPlayerFrequency(source) -> number|nil`

The frequency a player is on, or `nil` when their radio is off.

```lua
local freq = exports.tml_radio:GetPlayerFrequency(source)
```

### `GetFrequencyMembers(frequency) -> number[]`

Server ids of everyone on a frequency.

```lua
for _, id in ipairs(exports.tml_radio:GetFrequencyMembers(1.0)) do
  TriggerClientEvent('my_script:alert', id)
end
```

### `SetPlayerFrequency(source, frequency, force?) -> boolean, string?`

Puts a player on a frequency and turns their radio on. Restricted ranges are still checked unless `force` is `true`.
Returns `false` and an error code (`invalid`, `restricted`, `voice_failed`) on failure.

```lua
exports.tml_radio:SetPlayerFrequency(source, 1.0)
```

### `RemovePlayerFromRadio(source)`

Takes a player off their frequency. The player is told they were removed.

## Client exports

| Export | Returns |
|---|---|
| `IsRadioOn()` | `boolean` |
| `GetFrequency()` | `number|nil`: the frequency this player is tuned to |
| `OpenRadio()` | opens the radio UI (the server still checks for the item) |

## Server events

Fired with `TriggerEvent` for your own server scripts. They aren't net events, so clients can't fire them.

### `tml_radio:joined`

`(source, frequency)` after a player joins a frequency.

### `tml_radio:left`

`(source, frequency)` after a player leaves one (radio off, changed frequency, item lost, access lost, disconnect).

### `tml_radio:panic`

`(source, frequency, coords)` when a player presses panic. `coords` is a `vector3`. Use it to send a call to your
dispatch script:

```lua
AddEventHandler('tml_radio:panic', function(source, frequency, coords)
  exports['ps-dispatch']:CustomAlert({ message = 'Officer panic', coords = coords, code = '10-99' })
end)
```

## Bridge

Adapters expose a fixed set of functions (see `bridge/README.md`). Other resources shouldn't call them.
