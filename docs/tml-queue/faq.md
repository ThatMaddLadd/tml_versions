**Players never see the queue.**
The queue only appears when the server is full. To test it, set `Config.Queue.maxSlots = 1` and connect with two
clients. Make sure `ensure tml_queue` comes early in `server.cfg`.

**Discord role priority doesn't work.**
The console prints `roles=...` at start. With `none`, no bot token is set (`config/server.lua`) and Badger_Discord_API
isn't running. With `discord`: turn on "Server Members Intent" for the bot, check the guild id, and make sure the bot is
in your server. A "Discord refused the bot token" error means the token is wrong. Role ids in the config must be
strings: `['123456789012345678']`.

**A player says they have priority but the card shows "Standard".**
Their identifier must match exactly. Run `queue_addprio` with the identifier type they actually connect with (Discord
must be running for `discord:` ids to exist).

**Does buying priority again stack?**
No. It replaces the player's database priority (points, label and expiry) with the new one.

**What happens when someone crashes?**
With `Config.Grace.enabled`, they get `Config.Grace.points` priority for `Config.Grace.minutes`, which puts them at
the front when they reconnect.

**A player is stuck "loading" and holding a slot.**
Admitted players have `Config.Queue.connectTimeout` seconds to finish loading. After that, the slot is freed.

**Does it check bans?**
No. Your ban system (TML Admin Panel or another) checks bans on connect. If it refuses a player the queue let in,
the queue gives the slot back.

**Can I change the card's colours?**
No. FiveM draws Adaptive Cards in its own style. You can change the text (locales), server name, logo, tips and
buttons.

**Can I add another language?**
Copy `config/locales/en.lua`, translate the values, and set `Config.Locale`.

**I renamed the resource folder.**
Keep the name `tml_queue`. The exports and events are tied to it.
