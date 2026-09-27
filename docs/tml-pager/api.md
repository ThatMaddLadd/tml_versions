## Server exports

### `SendPage(dept, message, priority?, from?)`

Sends a page to every on-duty player in the department (with a pager if `requireItem`).

| Param | Type | Notes |
|---|---|---|
| `dept` | string | key in `Config.Departments` (`police`, `ems`, `fire`, ...) |
| `message` | string | trimmed, control characters removed, capped at `Config.Pager.maxMessageLength` |
| `priority` | `'normal'` \| `'urgent'` | default `'normal'` |
| `from` | string | sender label, default `Dispatch` |

Returns `ok: boolean, result`. On success `result` is the recipient count; on failure it is a locale key (`page_invalid`, `page_no_targets`).

```lua
local ok, count = exports.tml_pager:SendPage('police', '10-71 shots fired, Legion Sq', 'urgent', 'CAD')
```

### `GetNumber(src)`

Returns the pager number (string of digits) of an online player whose profile is loaded, or `nil`. The profile loads the first time the player opens the pager or receives a message.

```lua
local number = exports.tml_pager:GetNumber(source)
```

## Events

Internal events (`tml_pager:server:*`, `tml_pager:client:*`) are not public API and may change. The client never decides who may send: the server checks job, grade or ACE on every request and rate-limits it.

## ACE

```
add_ace group.admin tml_pager.dispatch allow      # may send pages
add_ace group.police tml.job.police allow          # standalone framework only: gives the "police" job
```
