Everything customers may change is in `config/config.lua` (never encrypted). Text shown to players and staff is in
`config/locales/*.lua`. Restart the resource after editing.

## General

| Option | Default | Meaning |
|---|---|---|
| `Config.Locale` | `'en'` | Language file from `config/locales`. English and Spanish are included. Missing keys fall back to English. |
| `Config.Debug` | `false` | Extra console diagnostics. Also registers the `tml_adminpanel_pedscan` console command. Leave off in production. |
| `Config.CheckForUpdates` | `true` | Prints a console notice when a newer version exists. |
| `Config.RateLimitMs` | `500` | Minimum time between two state-changing requests from the same staff member. |
| `Config.Framework` | `'auto'` | `'auto'`, `'qbox'`, `'qbcore'`, `'ox_core'`, `'nd_core'`, `'esx'`, `'standalone'` or `'custom'`. |
| `Config.Notify` | `'auto'` | `'auto'`, `'ox_lib'`, `'qbcore'`, `'esx'`, `'standalone'` or `'custom'`. |
| `Config.Commands.stopSpectate` | `'tml_stopspectate'` | Command that leaves spectate mode (Esc also works). |
| `Config.Commands.laser` | `'tml_laser'` | Toggles the entity delete laser (admin ACE only). |
| `Config.OpenKey` | `'F7'` | Default key to open the panel. Players can rebind it in FiveM's keybind settings. |

## Access (ACE)

Access to the panel is plain FiveM ACE, so it works the same on every framework:

```
add_ace group.admin tml_adminpanel.admin allow
add_ace group.moderator tml_adminpanel.mod allow
add_principal identifier.license:xxxxxxxx group.admin
```

| Option | Default | Meaning |
|---|---|---|
| `Config.AcePrefix` | `'tml_adminpanel'` | Prefix for the ACE strings above. |
| `Config.StaffGroups` | `{ 'admin', 'mod', 'support' }` | Groups that may open the panel (`<prefix>.<group>`). |

`<prefix>.developer` gets every permission. `<prefix>.admin` also gets every permission. Other staff groups get the
defaults in the table below, and an admin can grant or revoke individual permissions per person from the Permissions tab.

## Bans

| Option | Default | Meaning |
|---|---|---|
| `Config.ServerName` | `'My Server'` | Name shown on the ban screen. |
| `Config.BanAppealUrl` | `''` | Appeal link on the ban screen. Empty = none. |
| `Config.BanAppealMinDays` | `30` | Bans shorter than this cannot be appealed. `0` = all appealable. Permanent bans always can. |
| `Config.BanIdPrefix` | `'BAN'` | Prefix on ban ids, for example `BAN-00042`. |
| `Config.BanScreenMode` | `'card'` | `'card'` (Adaptive Card) or `'text'` (plain text, never depends on the renderer). |
| `Config.BanCardNativeButtons` | `false` | `true` uses real card buttons (left aligned) if Close ever stops working. |
| `Config.BanDurations` | 1h to 1 month + permanent | Length presets in seconds (`0` = permanent). Their labels are `bandur_<seconds>` in the locale files. |

Bans are matched on raw FiveM identifiers when a player connects. They do not depend on the framework.

## Optional hooks

| Option | Default | Meaning |
|---|---|---|
| `Config.MedicalEvents.revive` / `.heal` | `nil` | Client event of your EMS resource to fire after the native revive/heal. |
| `Config.OptionalIntegrations.appearance` | `nil` | Appearance resource used when changing a ped model (`setPlayerModel`, `setPlayerAppearance`). |
| `Config.OptionalIntegrations.controlsHint` | `nil` | Resource showing a controls hint while spectating (`showControls`, `hideControls`). |
| `Config.OptionalIntegrations.densityControl` | `nil` | Resource controlling ped density, raised during NPC Chaos Mode. |

## Screenshots

Player screenshots need the **screenshot-basic** resource and an upload key. Set these in `server.cfg`. They stay on the
server: the image is uploaded from the server, and the key is never sent to a player's client.

```
set tml_adminpanel:screenshotApiKey "your-upload-key"
set tml_adminpanel:screenshotUploadUrl "https://api.fivemanage.com/api/image"   # optional
set tml_adminpanel:screenshotUploadField "image"                                # optional
set tml_adminpanel:screenshotTTL "24"                                           # hours listed; pinned never expire
```

## Ped models, nameplates, chaos

`Config.PedModels` (quick presets), `Config.Nameplates`, `Config.Chaos` and `Config.TrollActions` are documented inline
in the config file. To add a language for the troll action names, add `troll_<id>` and `troll_<id>_desc` to your locale.

## Panel colours

Edit `web/dist/theme.css` (colour tokens as `R G B` triplets and corner radii). It loads after the panel styles and needs
no rebuild. The UI source is in `web/` if you want to change layout (`npm install`, `npm run build`).

## Permissions

| Key | Name | On by default | Description |
|---|---|---|---|
| `tml_adminpanel.players.view` | View Players | yes | View online player list and basic info |
| `tml_adminpanel.players.lookup` | Player Lookup | yes | Search and view detailed player information |
| `tml_adminpanel.players.freeze` | Freeze Players | no | Freeze/unfreeze player movement |
| `tml_adminpanel.players.kick` | Kick Players | no | Kick players from the server |
| `tml_adminpanel.players.ban` | Ban Players | no | Ban players from the server |
| `tml_adminpanel.players.teleport` | Teleport | yes | Teleport to players or bring players |
| `tml_adminpanel.players.spectate` | Spectate Players | yes | Spectate/watch players in real-time |
| `tml_adminpanel.players.rename` | Rename Character | yes | Change a character's first/last name |
| `tml_adminpanel.players.medical` | Medical / State | no | Revive, heal, kill, and set armor |
| `tml_adminpanel.players.warn` | Warn Players | no | Issue and view player warnings |
| `tml_adminpanel.players.screenshot` | Screenshot Player | no | Capture a player's screen for evidence |
| `tml_adminpanel.screenshots.view` | View Screenshots | yes | Browse the shared screenshot evidence gallery |
| `tml_adminpanel.screenshots.surveil` | Timed Surveillance | no | Auto-capture a player's screen on an interval for a set duration |
| `tml_adminpanel.screenshots.delete` | Delete Screenshots | no | Permanently remove screenshots from the evidence gallery |
| `tml_adminpanel.players.notes` | Player Notes | yes | View and add staff notes on a character |
| `tml_adminpanel.players.resetposition` | Reset Position | no | Clear a stuck character's stored spawn position (offline only) |
| `tml_adminpanel.players.resetbucket` | Reset Routing Bucket | no | Return a player stranded in a side dimension to the main world |
| `tml_adminpanel.economy.manage` | Manage Economy | no | View and adjust a character's cash and bank balance |
| `tml_adminpanel.bans.view` | View Ban List | no | Browse and search all active bans |
| `tml_adminpanel.announce` | Announce | no | Broadcast messages to the server or a single player |
| `tml_adminpanel.groups.view` | View Groups | yes | View player group assignments |
| `tml_adminpanel.groups.manage` | Manage Groups | no | Add/remove players from groups |
| `tml_adminpanel.ped.model` | Change Ped Model | no | Change player ped models |
| `tml_adminpanel.ped.clothing` | Modify Clothing | no | Modify player clothing/appearance |
| `tml_adminpanel.troll.basic` | Basic Troll | no | Ragdoll, screen and movement effects that wear off on their own |
| `tml_adminpanel.troll.advanced` | Advanced Troll | no | Explode, fire, launch, and spawn things on players |
| `tml_adminpanel.troll.extreme` | Extreme Troll | no | Eject from moving vehicles, hijack their car, drop them from orbit |
| `tml_adminpanel.stats.view` | View Statistics | yes | View server statistics |
| `tml_adminpanel.logs.view` | View Admin Logs | yes | View admin action logs |
| `tml_adminpanel.permissions.manage` | Manage Permissions | no | Grant/revoke panel permissions to other staff |
| `tml_adminpanel.tools.view` | Admin Tools | yes | Access admin tools tab |
| `tml_adminpanel.tools.noclip` | Noclip | yes | Toggle noclip/freecam mode |
| `tml_adminpanel.tools.invisible` | Invisible Mode | yes | Become invisible to other players |
| `tml_adminpanel.tools.nameplates` | Player Nameplates | yes | Show player info above heads |
| `tml_adminpanel.tools.blips` | Player Blips | yes | Show every online player on your own map. Only you see them. |
| `tml_adminpanel.tools.chaos` | NPC Chaos Mode | no | Arm nearby NPCs and make them brawl (/chaos, /chaosoff) |
| `tml_adminpanel.tools.vehicle` | Self Vehicle Tools | yes | Spawn a vehicle for yourself, repair, clean, flip, and delete it |
| `tml_adminpanel.vehicles.manage` | Manage Vehicles | yes | Locate, store, impound, and delete player vehicles |

## Troll actions

| Id | Name | Permission tier |
|---|---|---|
| `ragdoll` | Ragdoll | `tml_adminpanel.troll.basic` |
| `drunk` | Drunk Effect | `tml_adminpanel.troll.basic` |
| `screen_shake` | Screen Shake | `tml_adminpanel.troll.basic` |
| `explode` | Explode | `tml_adminpanel.troll.advanced` |
| `fire` | Set on Fire | `tml_adminpanel.troll.advanced` |
| `launch` | Launch | `tml_adminpanel.troll.advanced` |
| `vehicle_spawn` | Spawn Vehicle on Head | `tml_adminpanel.troll.advanced` |
| `ghost_mode` | Ghost Mode | `tml_adminpanel.troll.advanced` |
| `blackout` | Blackout | `tml_adminpanel.troll.basic` |
| `bad_trip` | Bad Trip | `tml_adminpanel.troll.basic` |
| `slow_mo` | Slow Motion | `tml_adminpanel.troll.basic` |
| `sonic` | Sonic Legs | `tml_adminpanel.troll.basic` |
| `moon_jump` | Moon Jump | `tml_adminpanel.troll.basic` |
| `clumsy` | Clumsy | `tml_adminpanel.troll.basic` |
| `thunderstruck` | Thunderstruck | `tml_adminpanel.troll.basic` |
| `forced_dance` | Forced Dance | `tml_adminpanel.troll.basic` |
| `blowout` | Blowout | `tml_adminpanel.troll.advanced` |
| `animal_ambush` | Animal Ambush | `tml_adminpanel.troll.advanced` |
| `boxed_in` | Boxed In | `tml_adminpanel.troll.advanced` |
| `eject` | Eject | `tml_adminpanel.troll.extreme` |
| `possessed_car` | Possessed Car | `tml_adminpanel.troll.extreme` |
| `orbit` | Orbit | `tml_adminpanel.troll.extreme` |
