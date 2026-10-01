1. Put `tml_adminpanel` in your resources folder.
2. In `server.cfg`, **after** ox_lib, oxmysql and your framework:
   ```
   ensure tml_adminpanel
   ```
3. Give staff access with ACE (the prefix comes from `Config.AcePrefix`):
   ```
   add_ace group.admin tml_adminpanel.admin allow
   add_ace group.moderator tml_adminpanel.mod allow
   add_principal identifier.license:xxxxxxxx group.admin
   ```
4. Start the server. The tables are created automatically. `install.sql` has the same statements if you prefer to run
   them by hand. Tables from earlier builds (`adminpanel_*`) are renamed to `tml_adminpanel_*` and keep their data.
5. Edit `config/config.lua` (server name, ban appeal link, language).

There are no items to add: the panel does not use inventory items.

## Optional: player screenshots

Install [screenshot-basic](https://github.com/citizenfx/screenshot-basic), then add to `server.cfg`:

```
set tml_adminpanel:screenshotApiKey "your-upload-key"
```

See `docs/CONFIG.md` for the other convars.
