**The panel does not open.**
Check that you have ACE access (`add_ace group.admin tml_adminpanel.admin allow` and your license in a group), then use
`ensure tml_adminpanel` after ox_lib, oxmysql and your framework. If you set `Config.AcePrefix`, use that prefix instead.

**"Access Denied" when I press F7.**
You are not in a group listed in `Config.StaffGroups`. Reconnect after adding the ACE line, or run `refresh` and restart.

**Which framework is it using?**
The console prints `framework=...` at start. Set `Config.Framework` to force one. `standalone` means none was found.

**The console says the framework started after this resource.**
Harmless. Resource start order is not guaranteed and the panel switches to the framework once it appears. Put
`ensure tml_adminpanel` after your framework to avoid the message.

**Offline players or vehicles do not show up.**
That data comes from your framework's database tables. Standalone has none. On ND_Core there is no vehicle management.
If your framework is a fork with different tables, edit the queries in `bridge/framework/<framework>.lua`.

**Bans do not work on someone who never joined.**
Ban by identifier from the Bans tab. Qbox, QBCore and ESX only store the `license`, so a ban by Discord or Steam ID only
covers that exact ID.

**Screenshots say "Screenshot upload is not configured".**
Install screenshot-basic and set `tml_adminpanel:screenshotApiKey` in `server.cfg` (see `docs/CONFIG.md`). The key must be
on the server; it is never sent to players.

**Screenshots fail with "Upload rejected by the image host".**
The host answered with an error. Check the key and, if you use your own host, `tml_adminpanel:screenshotUploadUrl` and
`tml_adminpanel:screenshotUploadField`. The host must answer with JSON containing a `url` field.

**Can I change the colours?**
Yes: edit `web/dist/theme.css`. No rebuild needed.

**Can I add another language?**
Copy `config/locales/en.lua`, translate the values, set `Config.Locale`. Keys starting with `ui_` are shown in the panel.

**I renamed the resource folder.**
Use the name `tml_adminpanel`. Other scripts that call its exports use the folder name.

**Upgrading from an earlier build.**
Upgrade notes for each version, including the 3.0.0 migration (renamed events, tables, permissions and config path), are posted in the Discord.
