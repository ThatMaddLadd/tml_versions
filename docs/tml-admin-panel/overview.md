An in-game administration panel for FiveM by ThatMaddLadd. Player management, framework-independent bans, warnings and
notes, vehicles, screenshots as evidence, admin tools and a full audit log, in one searchable UI (F7 by default).

Every action is checked on the server, permission gated and logged.

## Requirements

- FiveM server with OneSync
- [ox_lib](https://github.com/overextended/ox_lib) and [oxmysql](https://github.com/overextended/oxmysql)
- One framework (auto-detected), or none:
  Qbox, QBCore, ox_core, ND_Core, ESX, or standalone
- Optional: [screenshot-basic](https://github.com/citizenfx/screenshot-basic) for player screenshots

## Framework support

The panel talks to your framework through small adapter files in `bridge/framework/`. They are not encrypted, so you can
edit them or add your own (`custom.lua` is a commented template). See `bridge/README.md`.

| Framework | Adapter |
|---|---|
| Qbox (`qbx_core`) | `qbox.lua` |
| QBCore (`qb-core`) | `qbcore.lua` |
| ox_core | `ox_core.lua` |
| ND_Core | `nd_core.lua` (no vehicle management: schema not public) |
| ESX (`es_extended`) | `esx.lua` |
| Standalone (no framework) | `standalone.lua` (bans, kick, teleport, spectate, screenshots, warnings, notes and tools; no characters, money or vehicles) |

vRP is not supported: its data model (no character table, groups instead of jobs, two incompatible major versions) needs
a different offline-lookup design than the other adapters. Add it as a `custom` adapter if you need it.

Limits that come from the frameworks themselves:

- Qbox, QBCore and ESX only store the `license` identifier. Banning an offline character by Discord or Steam ID bans that
  ID directly; it will not pull in the other identifiers stored for the account.
- ox_core cash goes through the `money` item in ox_inventory. If you run another inventory with ox_core, change the cash
  functions in `bridge/framework/ox_core.lua`.

## Install

1. Put `tml_adminpanel` in your resources folder.
2. In `server.cfg`, after ox_lib, oxmysql and your framework:
   ```
   ensure tml_adminpanel
   ```
3. Give staff access with ACE:
   ```
   add_ace group.admin tml_adminpanel.admin allow
   add_ace group.moderator tml_adminpanel.mod allow
   add_principal identifier.license:xxxxxxxx group.admin
   ```
4. Start the server. The database tables are created automatically (`install/install.sql` has the same statements).
5. Edit `config/config.lua` (server name, appeal link, language). Every option is commented.

Optional player screenshots: install screenshot-basic and set the upload key (see `docs/CONFIG.md`).

## Using it

Press **F7** (rebindable in FiveM keybind settings) to open the panel. Tabs appear according to each person's permissions.

| Tab | What it does |
|---|---|
| Players | Live list and search across online and offline characters. Freeze, kick, ban, teleport, spectate, rename, revive, heal, kill, armor, money, warnings, notes, vehicles, ped model, troll actions, screenshots |
| Admin Tools | Noclip, invisibility, nameplates, map blips, self vehicle tools, announcements, ped model |
| Bans | Search and lift bans, ban by Discord / licence / FiveM / Steam identifier |
| Screenshots | Shared evidence gallery with pinning, burst capture and timed surveillance |
| Statistics | Players online, uptime, resources, load |
| Admin Logs | Every admin action, searchable |
| Permissions | Grant or revoke individual permissions per person |

## Documentation

- `docs/CONFIG.md`: every option and permission
- `docs/API.md`: exports and events for your own scripts
- `docs/FAQ.md`: common problems
- `bridge/README.md`: writing or editing a framework adapter

## Language and theme

English and Spanish are included (`config/locales`). Copy `en.lua`, translate the values and set `Config.Locale`. Panel
colours are in `web/dist/theme.css` and need no rebuild.

## Source

The UI source is in `web/` (React, TypeScript, Tailwind). To change it: `cd web`, `npm install`, `npm run build`.
Rebuild output goes to `web/dist`.

## Support

Install help, bugs in the shipped code and the documented config. Not covered: custom frameworks (the open bridge exists
for that), heavily modified forks, conflicts with unrelated scripts and general server setup.
Discord: https://discord.gg/RPzkXrDUEQ
