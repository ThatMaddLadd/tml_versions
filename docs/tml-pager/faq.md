**The pager does not open.**
Check `ensure tml_pager` runs after oxmysql, your framework and your inventory. If `Config.Pager.requireItem` is `true`,
confirm the item is in your inventory config (see `install/`).

**Which framework/inventory is it using?**
The console prints the detected framework and inventory at start. Set `Config.Framework` / `Config.Inventory` to force
one. `standalone` means none was found.

**The console says the framework or inventory started after this resource.**
Harmless in most cases, but safest to fix: put `ensure tml_pager` after your framework and inventory in `server.cfg`.

**I don't have a pager number.**
Your profile loads the first time you open the pager or receive a message. If you use `Config.Pager.requireItem`, make
sure the item exists in your inventory's item list (see `install/`).

**Can I test messaging without a second player?**
Set `Config.TestMode = true` to message and save your own number. Turn it off before going live.

**Messages aren't arriving for offline players.**
They are: messages are stored in the database and delivered the next time that pager number is opened. Nothing to
configure.

**Dispatch alerts aren't reaching anyone.**
Check `Config.Dispatchers` (who may send) and `Config.Departments` (who receives, by job). If
`Config.Dispatch.requireOnDuty` is `true`, responders must be on duty.

**Can I change the colours?**
Yes: edit `web/theme.css` (or set `Config.Theme.vars` at runtime). Blue, graphite and amber-backlight presets are
included as comments in the file — uncomment one instead of writing your own.

**Can I add another language?**
Copy `config/locales/en.lua`, translate the values, and set `Config.Locale`.

**My inventory or framework isn't in the list.**
Edit or add an adapter in `bridge/`; those files are open, not escrowed. `bridge/framework/custom.lua` and
`bridge/inventory/custom.lua` are commented templates.

**I renamed the resource folder.**
Use the name `tml_pager`. The exports (`SendPage`, `GetNumber`) and item name are tied to it.
