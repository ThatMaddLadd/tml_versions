1. Put `tml_queue` in your resources folder. Keep the folder name `tml_queue`.
2. Add `ensure tml_queue` to `server.cfg` **after** oxmysql (and Badger_Discord_API, if you use it). Start it early,
   before your framework, so it sees every connection.
3. Start the server. The database tables are created automatically (`install.sql` has the same statements if you
   would rather run them by hand).
4. In `config/config.lua`, set `Config.Card.serverName`, the card buttons, and the Discord role priorities if you want
   them.
5. For Discord role priority with the built-in bot, follow the steps in `config/server.lua` (bot token, "Server
   Members Intent", guild id). The console prints `roles=discord` at start when it's active.

## Commands

From the server console, txAdmin's live console, or in game with the ACE permission:

```
add_ace group.admin tml_queue.admin allow
```

| Command | What it does |
|---|---|
| `queue` | List everyone waiting |
| `queue_addprio <identifier> <points> [days] [label]` | Give priority (no days = permanent) |
| `queue_removeprio <identifier>` | Remove database priority |
| `queue_whitelist add|remove <identifier> [note]` | Manage the database whitelist |
| `queue_skip <position or identifier>` | Move someone in the queue to the front |

Identifiers look like `discord:123456789012345678`, `license:abc123...`, `steam:110000...` or `fivem:123456`.

## Testing the queue

Set `Config.Queue.maxSlots = 1` and connect with two clients: the second waits in the queue until the first leaves.
Set it back to `0` (use `sv_maxclients`) afterwards.
