Everything you may change is in `config/config.lua` (never encrypted), with a comment on every option. Server-only
settings such as API keys are in `config/server.lua`, which is never sent to players. Text shown to players is in
`config/locales/*.lua`. Restart the resource after editing.

**Config is checked on start.** A missing option or a value of the wrong type or range prints a readable warning in
the server console and uses the default below, so a typo never breaks the phone.

## Contents

- [General](#general) · [Bridge](#bridge) · [Devices](#devices) · [Opening the phone](#opening-the-phone) ·
  [Mouse focus](#mouse-focus) · [Phone size](#phone-size) · [Phones and ownership](#phones-and-ownership) · [Phone numbers](#phone-numbers) ·
  [PIN lock](#pin-lock) · [First-boot setup](#first-boot-setup)
- [Contacts](#contacts) · [Messages](#messages) · [Calls and video calls](#calls-and-video-calls) ·
  [Service numbers](#service-numbers)
- [Smartphone apps](#smartphone-apps) · [Themes](#themes) · [Designs](#designs) · [Flashlight](#flashlight) ·
  [Battery](#battery)
- [Notes](#notes) · [Clock](#clock) · [Mail](#mail) · [Ads](#ads) · [Maps](#maps) · [Bank](#bank) ·
  [Camera and Photos](#camera-and-photos) · [Music](#music) · [Dark Chat](#dark-chat) · [Chirp and Lens](#chirp-and-lens) ·
  [Marketplace](#marketplace) · [News](#news) · [Garage](#garage) · [Calendar](#calendar)
- [Sounds and ringtones](#sounds-and-ringtones) · [Animations](#animations) · [Rate limits](#rate-limits) ·
  [Server-only settings](#server-only-settings-configserverlua) · [Colours of the flip phone](#colours-of-the-flip-phone)

## General

| Option | Default | Meaning |
|---|---|---|
| `Config.Locale` | `'en'` | Language file from `config/locales` (English and Spanish included). Missing keys fall back to English. |
| `Config.Debug` | `false` | Verbose console output and the admin-only `/tml_phone_*` test commands. Leave off on live servers. |
| `Config.CheckForUpdates` | `true` | Prints a console notice when a newer version exists. |

## Bridge

`'auto'` detects what is running; set a name to force one. Detection order and how to write your own adapter:
`bridge/README.md`. Start `tml_phone` after everything it should detect.

| Option | Default | Values |
|---|---|---|
| `Config.Framework` | `'auto'` | `'qbox'` `'qbcore'` `'esx'` `'ox_core'` `'nd_core'` `'vrp'` `'standalone'` `'custom'` |
| `Config.Inventory` | `'auto'` | `'ox_inventory'` `'qb-inventory'` `'ps-inventory'` `'qs-inventory'` `'codem-inventory'` `'core_inventory'` `'standalone'` `'custom'` |
| `Config.Notify` | `'auto'` | `'ox_lib'` `'qbcore'` `'esx'` `'standalone'` `'custom'` |
| `Config.Voice` | `'auto'` | `'pma-voice'` `'saltychat'` `'none'` `'custom'`: carries the audio of calls |
| `Config.Dispatch` | `'auto'` | `'ps-dispatch'` `'cd_dispatch'` `'core_dispatch'` `'standalone'` `'custom'`: alerts from service texts |
| `Config.Banking` | `'auto'` | `'tml_banking'` `'renewed-banking'` `'okokBanking'` `'qs-banking'` `'fd_banking'` `'tgg-banking'` `'snipe-banking'` `'pefcl'` `'qb-banking'` `'esx_banking'` `'framework'` `'custom'`: where Bank payments show up as statements, and where job accounts live |
| `Config.Uploads` | `'auto'` | `'fivemanage'` `'none'` `'custom'`: where Camera photos are stored (keys in `config/server.lua`). `'auto'` = `'fivemanage'`, ready once its key is set |
| `Config.Speaker` | `'auto'` | `'builtin'` `'xsound'` `'none'` `'custom'`: Music played out loud to players nearby. `'auto'` = `'builtin'` (needs no other script); `'none'` = only the holder hears it |
| `Config.Garage` | `'auto'` | `'jg-advancedgarages'` `'cd_garage'` `'framework'` `'none'` `'custom'`: where the Garage app reads a character's vehicles. `'framework'` = the framework's own vehicle table, which qb-garages, qbx_garages and esx_garage use |

The framework adapters refuse a payment the player can't afford, even on frameworks whose bank balance may go
negative (QBCore, Qbox, ND_Core).

## Devices

A flip phone and a smartphone, on the same numbers, contacts and messages. Disable one for a flip-only or
smart-only server.

| Option | Default | Meaning |
|---|---|---|
| `Config.Devices.flip` | `{ enabled = true, item = 'tml_flipphone', prop = 'prop_npc_phone_02' }` | `item`: the inventory item that is this device. `prop`: the model in the player's hand |
| `Config.Devices.smart` | `{ enabled = true, item = 'tml_smartphone', prop = 'prop_npc_phone' }` | same, for the smartphone |

## Opening the phone

The command and keybind open the active phone (the last one used, or the first one carried).

| Option | Default | Meaning |
|---|---|---|
| `Config.Open.command` | `{ enabled = true, name = 'phone' }` | chat command |
| `Config.Open.keybind` | `{ enabled = true, key = 'F2' }` | default key; players can rebind it in GTA's key settings |
| `Config.Open.item` | `{ enabled = true }` | using a phone item opens that device |

## Mouse focus

The toggle key hands the mouse back to the game (look around, aim the camera, drive) while the phone stays on
screen; pressing it again gives the mouse back to the phone. While unfocused, the keys below still work the phone.
Key names are FiveM keyboard names (`UP`, `RETURN`, `BACK`, `LMENU` = left Alt, `F1`, ...); players can rebind all of
them in GTA's key settings (FiveM section).

| Option | Default | Meaning |
|---|---|---|
| `Config.Focus.toggle` | `{ enabled = true, key = 'LMENU' }` | the focus toggle |
| `Config.Focus.keys` | `up = 'UP'`, `down = 'DOWN'`, `left = 'LEFT'`, `right = 'RIGHT'`, `ok = 'RETURN'`, `clear = 'BACK'` | phone key = keyboard key while unfocused. Phone keys: `up` `down` `left` `right` `ok` `back` `soft1` `soft2` `call` `end` `clear` |
| `Config.Focus.disabledControls` | attack, aim, melee, weapon wheel and slots, cover, reload, duck, vehicle aim, radio wheel, Alt wheel, pause menu, vehicle mouse control | GTA control ids switched off while the phone has the mouse ([list](https://docs.fivem.net/docs/game-references/controls/)). The player can still walk and drive |

## Phone size

Players make their phone bigger or smaller themselves: smartphone Settings > Display > Phone size (− / + in 5%
steps, tap the size to reset it), flip phone Settings > Phone size (left / right, OK resets it). The size is kept on
the player's own computer, so it fits their screen whichever phone they pick up, and the phone stays in its corner.

| Option | Default | Meaning |
|---|---|---|
| `Config.Display.scale` | `1.0` | size for players who haven't changed it (1.0 = normal) |
| `Config.Display.minScale` | `0.7` | smallest size players can pick (0.5 - 2) |
| `Config.Display.maxScale` | `1.3` | largest size players can pick (0.5 - 2) |

## Phones and ownership

| Option | Default | Meaning |
|---|---|---|
| `Config.Phones.mode` | `'unique'` | `'unique'`: every phone item has its own number and data, which move with the item (give it away, drop it, steal it). `'character'`: one number per character, whichever phone item they carry. Inventories that can't store item data use `'character'` with a warning |
| `Config.Phones.requireItem` | `true` | `'character'` mode only: a phone item must be carried to use the phone. Standalone (no inventory) never requires one |
| `Config.Phones.defaultDevice` | `'smart'` | `'flip'` or `'smart'`: the device used when no item decides it |
| `Config.Phones.refreshInterval` | `15` | seconds between checks of what players carry, so a phone that was dropped, given away or picked up starts or stops receiving within this time |

## Phone numbers

| Option | Default | Meaning |
|---|---|---|
| `Config.Numbers.format` | `'###-####'` | `#` is a digit, anything else is shown as-is; 5 to 16 `#`. Numbers are stored as digits, so the format can change later as long as the digit count stays the same. Examples: `'(###) ###-####'`, `'07### ######'` |
| `Config.Numbers.prefix` | `''` | digits every generated number starts with (they count toward the total) |
| `Config.Numbers.reserved` | `{ '911', '311', '112', '999' }` | numbers and number prefixes never given to players. Service numbers are always reserved |

## PIN lock

A PIN is optional and exactly 4 digits. Whoever holds a phone can open it if it has no PIN, or with the right PIN.
Wrong guesses lock the phone for a while, doubling each time.

| Option | Default | Meaning |
|---|---|---|
| `Config.Pin.freeAttempts` | `3` | wrong guesses before the first lockout |
| `Config.Pin.lockoutSeconds` | `30` | first lockout; each further wrong guess doubles it |
| `Config.Pin.maxLockoutSeconds` | `3600` | the longest lockout |
| `Config.Pin.relockSeconds` | `300` | a phone unlocked by someone locks again after being closed this long |
| `Config.Pin.adminAce` | `'tml_phone.admin'` | ACE for admin commands (`/phonebattery`). A forgotten PIN or a new number: the `ResetPin` / `SetNumber` exports (docs/API.md) |

## First-boot setup

A new or factory-reset smartphone asks whoever opens it first for a language, the owner's name, a name for the phone
and a passcode. Saved on the phone (and, with unique phones, the item's metadata: `tml_name`, `tml_owner_name`,
`tml_language`). Other scripts read them with `GetPhoneByNumber` (docs/API.md). Players change them later in
Settings > About.

| Option | Default | Meaning |
|---|---|---|
| `Config.Setup.enabled` | `true` | `false` = new phones skip setup |
| `Config.Setup.chooseLanguage` | `true` | offer the languages in `config/locales` (the phone's own text; server messages stay in `Config.Locale`). `false` = every phone uses `Config.Locale` |
| `Config.Setup.passcode` | `'optional'` | `'optional'` (can be skipped), `'required'`, or `'off'` (not asked; set in Settings) |
| `Config.Setup.nameLength` | `24` | longest owner name or phone name |

## Contacts

| Option | Default | Meaning |
|---|---|---|
| `Config.Contacts.max` | `200` | contacts per phone |
| `Config.Contacts.nameLength` | `30` | characters in a contact name |
| `Config.Contacts.share.enabled` | `true` | "My number": offer your number to players nearby |
| `Config.Contacts.share.distance` | `5.0` | metres; only players this close, with their phone open, get the offer |
| `Config.Contacts.share.expireSeconds` | `60` | how long an unanswered offer stays valid |

## Messages

| Option | Default | Meaning |
|---|---|---|
| `Config.Messages.maxLength` | `500` | characters per message |
| `Config.Messages.perMinute` | `20` | messages one phone may send per minute |
| `Config.Messages.groupMax` | `16` | members in a group, creator included |
| `Config.Messages.groupNameLength` | `30` | characters in a group name |
| `Config.Messages.historyPerConversation` | `300` | messages kept per conversation (oldest removed) |
| `Config.Messages.pageSize` | `40` | messages loaded at a time when scrolling back |

## Calls and video calls

| Option | Default | Meaning |
|---|---|---|
| `Config.Calls.ringSeconds` | `30` | how long a call rings before it counts as missed |
| `Config.Calls.allowAnonymous` | `true` | players can hide their number by dialling `#31#` first |
| `Config.Calls.logDays` | `30` | days of call history kept |
| `Config.Calls.logPageSize` | `50` | entries shown in the call log |
| `Config.Calls.nearbyRingDistance` | `10.0` | metres within which nearby players hear a phone ring |
| `Config.Calls.roundRobinSeconds` | `10` | a round-robin service call rings each responder this long before the next |
| `Config.Calls.video` | `true` | smartphones can video call. A player's camera films their own face while they walk around, and the picture is streamed to the other side at any distance |
| `Config.Calls.videoIce` | `{ { urls = 'stun:stun.l.google.com:19302' } }` | WebRTC servers the picture uses to connect. `{ urls = 'stun:...' }` or `{ urls = 'turn:host:3478', username = '...', credential = '...' }`. Sent to players' games, so use TURN credentials made for this |
| `Config.Calls.videoRelayOnly` | `false` | `true` = the picture always goes through a TURN server in `videoIce`, so the two players never learn each other's IP address. Needs a TURN entry |

**Video and IP addresses.** With the default (`videoRelayOnly = false`), the two games connect directly where they
can, which means each can see the other's IP address, as in most voice and video apps. If that matters on your
server, run a TURN server (for example [coturn](https://github.com/coturn/coturn)), add it to `videoIce` and set
`videoRelayOnly = true`. Some players behind strict routers also need TURN to see video at all.

## Call voice effect

How the other person sounds on a call. pma-voice only (SaltyChat adds its own effect), and it needs
`setr voice_useNativeAudio true` and `setr voice_enableSubmix 1` in your voice config. pma-voice's own call sound has
no filter at all, so without this calls sound like the person is standing next to you.

| Option | Default | Meaning |
|---|---|---|
| `Config.VoiceEffect.enabled` | `true` | use the effect; `false` = pma-voice's own (clean) call sound |
| `Config.VoiceEffect.preset` | `'phone'` | a key in `presets`: `phone` (clean phone line), `cellular` (a bit of grit), `landline` (older, narrower) |
| `Config.VoiceEffect.volume` | `1.0` | 0.0-1.0, loudness of the other side through the effect |
| `Config.VoiceEffect.presets` | see config | your own presets: `freq_low` / `freq_hi` (the band of the voice that gets through, Hz), `o_freq_lo` / `o_freq_hi` (the output band), `fudge` (distortion), `rm_mix` (crackle), `rm_mod_freq` (ring modulation, robotic; 0 = off) |

Change a value and restart the resource to hear it. A preset with a missing or non-number value turns the effect off
with a console warning.

## Service numbers

`Config.Services` is a list. Calling a service number rings the listed jobs' phones; texting it opens a request those
players see and answer in the smartphone Services app (flip phones get a notification). Two are included: `911`
(police and ambulance, ring all, with a dispatch alert) and `311` (taxi, round robin).

| Field | Type | Meaning |
|---|---|---|
| `number` | string | the number to call or text |
| `label` | string | shown in the Services app and on calls |
| `jobs` | string[] | jobs whose phones ring and who see requests |
| `onDutyOnly` | boolean | only players on duty |
| `description` | string | shown under the name in the Services app |
| `icon` | string | `'siren'` `'shield'` `'medical'` `'car'` `'wrench'` `'flame'` `'briefcase'` `'phone'` |
| `color` | `#RRGGBB` | the service's colour in the app |
| `route` | string | `'ring_all'`: every responder's phone rings, first to answer takes it. `'round_robin'`: one responder at a time, taking turns |
| `text` | boolean | players can text this number |
| `dispatch` | table or `false` | also raise an alert in your dispatch script when a text shares a location: `{ code, jobs?, sprite?, color? }` (`jobs` defaults to the service's) |

| Option | Default | Meaning |
|---|---|---|
| `Config.ServiceRequests.keepHours` | `24` | a request drops off the responders' list this long after its last message |
| `Config.ServiceRequests.shareLocation` | `true` | "Share my location" starts switched on when texting a service |
| `Config.ServiceRequests.announceTaken` | `true` | tell the player when a responder takes the request |

Other scripts can add their own numbers with `RegisterCustomNumber` (docs/API.md).

## Smartphone apps

Settings is always installed. Players install and remove apps in the App Store and arrange the home screen by holding
an icon. The home screen has up to five pages that players swipe between. To move an icon to another page, drag it to
the side of the screen.

| Option | Default | Meaning |
|---|---|---|
| `Config.AppStore.enabled` | `true` | `false` = no App Store: every phone has exactly the preinstalled apps, and none can be added or removed |
| `Config.CoreApps.phone` | `true` | dialler, recents, favourites |
| `Config.CoreApps.messages` | `true` | texts and group chats |
| `Config.CoreApps.contacts` | `true` | the contact list (names still show in calls and messages) |

`false` in `Config.CoreApps` removes that app from the smartphone, including its buttons elsewhere (such as "Message"
on a contact). Incoming calls still ring without the Phone app, and the Services app still calls services. Flip
phones are not affected.

`Config.Apps` has one entry per optional app: `{ enabled, preinstalled, removable? }`.

- `enabled`: offered at all.
- `preinstalled`: already on the home screen of new phones.
- `removable` (default `true`): players may remove it. Preinstalled and not removable = on every phone, existing
  ones included.

| App | Default | What it is |
|---|---|---|
| `calendar` | enabled | events and reminders; scripts can add events |
| `calculator` | enabled | |
| `notes` | enabled, preinstalled | |
| `clock` | enabled, preinstalled | alarms, timer, stopwatch |
| `mail` | enabled, preinstalled | |
| `services` | enabled | call or text service numbers; responders answer requests here |
| `ads` | enabled | classifieds board |
| `maps` | enabled | the game's map, saved places, sharing locations |
| `wallet` | enabled | the Bank app: balance, paying and requesting money, the job's account |
| `camera` | enabled, preinstalled | needs an image host (`Config.Uploads`) |
| `photos` | enabled, preinstalled | the gallery; share photos in messages |
| `music` | enabled, preinstalled | curated songs and links players add |
| `darkchat` | enabled | anonymous channels with handles |
| `chirp` | enabled | public text feed |
| `lens` | enabled | public photo feed (same accounts as Chirp) |
| `market` | enabled | Marketplace: listings with photos |
| `garage` | enabled | your vehicles and a waypoint to them |
| `news` | enabled | articles from reporters, breaking news |

## Themes

`Config.Themes`: the colour themes players pick in Settings > Appearance (next to Light / Dark mode). The first one
is the default. Each entry: `{ id, label, color, color2, ink }`. `color` and `color2` are the theme's two hues,
`ink` the text colour on top of them (dark for very light colours). Included: Amethyst, Ember, Glacier, Acid, Rose,
Gold, Crimson, Graphite.

## Designs

The smartphone's shape and on-screen controls. Every app, theme and wallpaper works on all of them.

| Design | Look |
|---|---|
| `rail` | TML Rail: an angular slab with a second display (notifications, controls, home) down its right edge |
| `island` | rounded corners, a pill cutout with the status bar around it, a home bar |
| `classic` | a rounded slab with a punch-hole camera and Back / Home / Recent keys |
| `pear` | shaped like a pear, stem and leaf included, with the Island's pill and home bar |

| Option | Default | Meaning |
|---|---|---|
| `Config.Designs.default` | `'rail'` | the design a smartphone starts with |
| `Config.Designs.choose` | `true` | players may change it in Settings > Display |
| `Config.Designs.list` | all four | `{ id, label, item?, prop? }`. Remove an entry to turn that design off |

`item` gives a design its own inventory item (a pear phone sold in a shop): that item is a smartphone that always
shows the design, and the design leaves the Settings choice. Add the item to your inventory too (`install/`).
`prop` sets the model in the player's hand for that design.

## Flashlight

In the smartphone's control centre. Only lit while the phone is out; nearby players see the beam.

| Option | Default | Meaning |
|---|---|---|
| `Config.Flashlight.enabled` | `true` | show the button |
| `Config.Flashlight.color` | `{ 255, 244, 229 }` | `{ r, g, b }` |
| `Config.Flashlight.distance` | `14.0` | metres the beam reaches |
| `Config.Flashlight.brightness` | `7.0` | intensity |
| `Config.Flashlight.radius` | `24.0` | beam width in degrees |
| `Config.Flashlight.viewDistance` | `80.0` | other players' beams are drawn within this distance |

## Battery

Every phone a player carries drains over real time, faster while in use, and charges in a vehicle with its engine
running. At 0% it's dead: it can't be used, calls and payments can't reach it, and it shows no notifications and
rings no alarms. Rates are percent per real minute and add up.

| Option | Default | Meaning |
|---|---|---|
| `Config.Battery.enabled` | `true` | `false` = phones never run out and no level is shown |
| `Config.Battery.drain.idle` | `0.05` | always, while carried (about 33 hours from full) |
| `Config.Battery.drain.open` | `0.35` | extra while the phone is on screen |
| `Config.Battery.drain.call` | `0.25` | extra during a call |
| `Config.Battery.drain.flashlight` | `0.3` | extra while the flashlight is on |
| `Config.Battery.drain.camera` | `0.4` | extra while the camera is up (Camera app, or own camera in a video call) |
| `Config.Battery.vehicleCharge` | `2.0` | gained per minute in a vehicle with its engine on (`0` = vehicles don't charge) |
| `Config.Battery.startLevel` | `100` | level of a brand-new phone |
| `Config.Battery.lowWarning` | `15` | notify the holder below this percent (`0` = never) |
| `Config.Battery.deadScreen` | `true` | opening a dead phone shows a charge screen. `false` = it doesn't open; a notification says why |
| `Config.Battery.adminCommand` | `'phonebattery'` | `/phonebattery <0-100> [player id]` sets a carried phone's level, for players with `Config.Pin.adminAce` (also from the server console). `''` = no command |

Levels are saved to the database once a minute, so a change in the last minute before a server stop is lost.

## Notes

| Option | Default | Meaning |
|---|---|---|
| `Config.Notes.max` | `100` | notes per phone |
| `Config.Notes.length` | `3000` | characters per note |

## Clock

Alarms go off at in-game time (the time the phone shows) and ring even while the phone is put away. The timer and
stopwatch count real time.

| Option | Default | Meaning |
|---|---|---|
| `Config.Clock.maxAlarms` | `20` | alarms per phone |
| `Config.Clock.labelLength` | `30` | characters in an alarm label |
| `Config.Clock.ringSeconds` | `30` | an unanswered alarm or finished timer stops ringing after this long |
| `Config.Clock.snoozeMinutes` | `10` | in-game minutes (one is 2 real seconds by default) |
| `Config.Clock.sound` | `{ type = 'native', name = '5_SEC_WARNING', set = 'HUD_MINI_GAME_SOUNDSET' }` | repeated while ringing; see [Sounds](#sounds-and-ringtones) |
| `Config.Clock.soundInterval` | `900` | ms between repeats |

## Mail

Every phone gets an address the first time it's needed, made from the character's name (`john.smith@tml.mail`).
Scripts can send mail with buttons: `SendMail` in docs/API.md.

| Option | Default | Meaning |
|---|---|---|
| `Config.Mail.domain` | `'tml.mail'` | the part after the `@` |
| `Config.Mail.allowChangeAddress` | `true` | players can pick a different address |
| `Config.Mail.subjectLength` | `80` | characters in a subject |
| `Config.Mail.bodyLength` | `4000` | characters in a body |
| `Config.Mail.maxRecipients` | `5` | addresses one player mail can go to |
| `Config.Mail.perMinute` | `6` | mails one phone may send per minute |
| `Config.Mail.maxPerMailbox` | `300` | mails kept per phone (oldest removed) |
| `Config.Mail.keepDays` | `60` | older mails are deleted |

## Ads

A public classifieds board. Ads show the poster's number so people can call or text.

| Option | Default | Meaning |
|---|---|---|
| `Config.Ads.titleLength` | `50` | characters in a title |
| `Config.Ads.bodyLength` | `500` | characters in the text |
| `Config.Ads.maxPrice` | `10000000` | highest price (`0` = no price field) |
| `Config.Ads.currency` | `'$'` | shown before prices |
| `Config.Ads.maxActive` | `3` | live ads per phone |
| `Config.Ads.expireHours` | `24` | whole hours an ad stays up (at least 1) |
| `Config.Ads.removeOnDisconnect` | `false` | take a player's ads down when they leave or switch character |
| `Config.Ads.cooldownSeconds` | `120` | wait between two ads from one phone |
| `Config.Ads.showName` | `true` | show the poster's character name |
| `Config.Ads.moderatorAce` | `'tml_phone.admin'` | ACE that may take down anyone's ad |
| `Config.Ads.categories` | For sale, Wanted, Services, Jobs, Events, Other | `{ id, label, icon }`; the first is preselected when posting |

## Maps

The Maps app shows the game's own map, loaded from each player's game files, so map mods (postal codes, satellite
style) show through. Public places are shown to everyone.

| Option | Default | Meaning |
|---|---|---|
| `Config.Maps.maxPlaces` | `50` | saved places per phone |
| `Config.Maps.labelLength` | `40` | characters in a place name |
| `Config.Maps.places` | hospital, police stations, City Hall, Legion Square, Fleeca, LS Customs | `{ label, x, y, icon }`. Icons: `'medical'` `'shield'` `'wrench'` `'bank'` `'fuel'` `'food'` `'bag'` `'building'` `'star'` |

## Bank

The Bank app replaced the Wallet app in 1.1.0 and keeps its settings (`Config.Apps.wallet`, `Config.Wallet`), so
existing configs work unchanged. It shows the bank balance, pays another phone number, asks a number for money, and
gives a job's bosses that job's own account. Money goes to whoever carries the other phone right now (they must be
online). Payments also show up in your banking script (`Config.Banking`).

| Option | Default | Meaning |
|---|---|---|
| `Config.Wallet.account` | `'bank'` | framework account payments come from and go to |
| `Config.Wallet.currency` | `'$'` | shown before amounts |
| `Config.Wallet.showCash` | `true` | also show the cash the character carries |
| `Config.Wallet.minAmount` | `1` | smallest payment or request |
| `Config.Wallet.maxAmount` | `100000` | largest single payment or request |
| `Config.Wallet.perMinute` | `5` | payments and requests one phone may send per minute |
| `Config.Wallet.noteLength` | `60` | characters in a payment note |
| `Config.Wallet.keepDays` | `30` | older history (and business activity) is removed |
| `Config.Wallet.requests` | `true` | players can ask a number for money; the other side pays or declines in their Bank app |
| `Config.Wallet.requestHours` | `24` | an unanswered request is removed after this many hours |
| `Config.Wallet.maxRequests` | `10` | unanswered requests one phone can have out at once |
| `Config.Wallet.bank` | `true` | with TML Banking running, also show the player's bank accounts, history, transfers, cards, loans and credit score (TML Banking's `Config.Mobile` decides what's allowed) |
| `Config.Wallet.business.enabled` | `true` | show "Business" to the players listed below |
| `Config.Wallet.business.jobs` | `{ police = 4, ambulance = 4, mechanic = 4 }` | job name = lowest grade that may use that job's account: see its balance and activity, deposit from their own account, withdraw to it |

A job's account is the job name in your banking script (`Config.Banking`). TML Banking, Renewed-Banking, qb-banking, okokBanking,
qs-banking, fd_banking, tgg-banking, snipe-banking and pefcl keep job accounts; with `'framework'`, ESX society
accounts (`esx_addonaccount`) are used. When the banking script has no account for a job, the Business page says it
isn't available.

## Camera and Photos

The phone takes photos itself (no screenshot resource needed); it only needs an image host to store them
(`Config.Uploads`, keys in `config/server.lua`; guide in `bridge/uploads/README.md`). In the camera: Enter or left click takes a photo, Up switches between the rear and
selfie cameras, Backspace or right click closes it.

| Option | Default | Meaning |
|---|---|---|
| `Config.Camera.maxPhotos` | `200` | photos kept per phone (taken and added from links together) |
| `Config.Camera.encoding` | `'webp'` | `'webp'`, `'jpg'` or `'png'` |
| `Config.Camera.maxWidth` | `1280` | pixels; larger screens are scaled down |
| `Config.Camera.maxHeight` | `720` | pixels |
| `Config.Camera.uploadRetries` | `2` | a failed upload (host busy, connection dropped) sends the same photo again this many more times (`0` = no retries) |

Adding images from a link (Photos app > +). Only direct image links from these sites are accepted: everyone who views
a shared image contacts the site it's on.

| Option | Default | Meaning |
|---|---|---|
| `Config.Photos.allowLinks` | `true` | show "Add from link" |
| `Config.Photos.linkHosts` | `i.imgur.com`, `fivemanage.com`, `i.ibb.co`, `files.catbox.moe` | sites links may come from (subdomains included). Discord links expire after about a day, so they aren't listed |
| `Config.Photos.linkExtensions` | `png jpg jpeg webp gif` | the link must end in one of these (`{}` = any) |

## Music

Songs play for the phone's holder, who can turn on the speaker so players nearby hear it too (`Config.Speaker`).
Songs are direct audio links (an `https` URL ending in `.mp3`, `.ogg`, ...), not YouTube or Spotify pages.

| Option | Default | Meaning |
|---|---|---|
| `Config.Music.tracks` | `{}` | songs every phone has under "Curated for you": `{ title, artist?, url }` (direct https link) or `{ title, artist?, file }` (an `.ogg` / `.mp3` you put in `web/music/`) |
| `Config.Music.curateAce` | `'tml_phone.admin'` | ACE that may add and remove curated songs in game (Music app, +). `''` = config and exports only |
| `Config.Music.maxCurated` | `100` | songs staff can add in game (config songs not counted) |
| `Config.Music.allowLinks` | `true` | players can add songs from a link |
| `Config.Music.linkHosts` | `files.catbox.moe`, `fivemanage.com`, `cdn.pixabay.com`, `archive.org` | sites song links may come from (subdomains included). Staff adding curated songs may use any https link |
| `Config.Music.linkExtensions` | `mp3 ogg wav webm` | the link must end in one of these (`{}` = any) |
| `Config.Music.maxSongs` | `50` | songs a phone can add from links |
| `Config.Music.titleLength` | `60` | characters in a title or artist |
| `Config.Music.volume` | `0.5` | 0-1: music volume a new phone starts with (players change it in the player) |
| `Config.Music.background` | `true` | keep playing when the phone is put away |
| `Config.Music.speaker.enabled` | `true` | offer the speaker button |
| `Config.Music.speaker.distance` | `20.0` | metres other players hear it |
| `Config.Music.speaker.volume` | `0.5` | 0-1: the loudest it gets for others (scaled by the phone's music volume) |

## Dark Chat

Anonymous channels. Messages show the handle a player picks, never their number.

| Option | Default | Meaning |
|---|---|---|
| `Config.DarkChat.handleLength` | `16` | longest handle (at least 3; letters, numbers, `_ . -`) |
| `Config.DarkChat.allowHandleChange` | `true` | players can pick a new handle later |
| `Config.DarkChat.channelNameLength` | `24` | characters in a channel name |
| `Config.DarkChat.maxChannels` | `20` | channels one phone can be in |
| `Config.DarkChat.messageLength` | `300` | characters per message |
| `Config.DarkChat.perMinute` | `15` | messages one phone may send per minute |
| `Config.DarkChat.pageSize` | `40` | messages loaded at a time |
| `Config.DarkChat.historyPerChannel` | `200` | messages kept per channel |
| `Config.DarkChat.keepDays` | `14` | older messages are removed |

## Chirp and Lens

A public text feed (Chirp) and a public photo feed (Lens) on one set of accounts. An account is a handle and a
password, not a phone: players log in on any phone, and a phone stays logged in until someone logs out in the app. A
lost or stolen phone that's still logged in can post as its owner; changing the password logs every other phone out.
Follows work in both apps. Photos come from the phone's gallery (Camera, Photos); the gallery picker works without the
Photos app installed.

| Option | Default | Meaning |
|---|---|---|
| `Config.Social.handleLength` | `16` | longest handle (at least 3; letters, numbers, `_` and `.`) |
| `Config.Social.nameLength` | `30` | characters in a display name |
| `Config.Social.bioLength` | `160` | characters in a profile bio |
| `Config.Social.postLength` | `280` | characters in a post, caption, reply or comment |
| `Config.Social.maxMedia` | `4` | photos in one post (1-4) |
| `Config.Social.accountsPerCharacter` | `3` | accounts one character can create (`0` = no limit) |
| `Config.Social.accountsPerPhone` | `5` | accounts one phone can stay logged in to (players switch between them) |
| `Config.Social.perMinute` | `6` | posts, replies and comments one account may send per minute |
| `Config.Social.pageSize` | `20` | posts loaded at a time |
| `Config.Social.keepDays` | `0` | posts older than this are deleted (`0` = kept forever) |
| `Config.Social.notifyLikes` | `true` | tell authors about likes (replies, mentions, follows, reposts and quotes always notify) |
| `Config.Social.blockedWords` | `{}` | posts, names and bios containing one of these are refused (any case) |
| `Config.Social.moderatorAce` | `'tml_phone.admin'` | ACE that may delete any post, suspend accounts and give the verified badge (in the app: a post's "…" menu, a profile's "…" menu) |

Players can report a post (spam, abuse, something else). Reports go to a Discord webhook if you set one in
`config/server.lua` (`ServerConfig.Social`). A suspended account's posts are hidden, and it can't log in or post.

## Marketplace

Listings with photos and a price. Buyers call or text the seller's number; money and items change hands in person.
Listings expire, and sellers can mark them sold: they leave the public list but stay in the seller's own list and in
buyers' saved ones. Ads stays the text board for services, jobs and events.

| Option | Default | Meaning |
|---|---|---|
| `Config.Marketplace.titleLength` | `50` | characters in a title |
| `Config.Marketplace.bodyLength` | `1000` | characters in the description |
| `Config.Marketplace.maxPhotos` | `4` | photos per listing (0-8; `0` = text only) |
| `Config.Marketplace.maxPrice` | `10000000` | highest price |
| `Config.Marketplace.currency` | `'$'` | shown before prices |
| `Config.Marketplace.maxActive` | `5` | listings one phone can have up at once (sold ones don't count) |
| `Config.Marketplace.expireDays` | `7` | whole days a listing stays up |
| `Config.Marketplace.cooldownSeconds` | `60` | wait between two listings from one phone |
| `Config.Marketplace.showName` | `true` | show the seller's character name |
| `Config.Marketplace.moderatorAce` | `'tml_phone.admin'` | ACE that may take down anyone's listing |
| `Config.Marketplace.categories` | Vehicles, Property, Electronics, Fashion, Tools, Other | `{ id, label, icon }`; the first is preselected when listing |

## News

Articles everyone with the app can read, written by the jobs you list (or anyone with `publisherAce`). A writer can
send an article out as breaking news: every smartphone with News installed gets a notification. Writers edit and take
down their own articles. Scripts publish with `PublishNews` (docs/API.md).

| Option | Default | Meaning |
|---|---|---|
| `Config.News.jobs` | `{ reporter = 0 }` | job name = lowest grade that may write articles |
| `Config.News.onDutyOnly` | `false` | writers must be on duty |
| `Config.News.publisherAce` | `'tml_phone.news'` | ACE that may write whatever their job (`''` = jobs only) |
| `Config.News.moderatorAce` | `'tml_phone.admin'` | ACE that may take down any article |
| `Config.News.titleLength` | `80` | characters in a headline |
| `Config.News.bodyLength` | `5000` | characters in an article |
| `Config.News.breaking` | `true` | writers can send an article out as breaking news |
| `Config.News.breakingCooldown` | `300` | seconds between two breaking news alerts from one writer |
| `Config.News.pageSize` | `20` | articles loaded at a time |
| `Config.News.keepDays` | `30` | older articles are deleted |

## Garage

The character's vehicles: whether each is out, in a garage or impounded, fuel and damage when your garage script keeps
them, and a waypoint to the vehicle (when it's out), its garage or the impound lot. Vehicles are read through
`Config.Garage` (bridge/garage/); vehicle names come from the player's own game.

| Option | Default | Meaning |
|---|---|---|
| `Config.Vehicles.locate` | `true` | vehicles that are out show where they are now, and players can set a waypoint to them |
| `Config.Vehicles.garages` | four examples | garage id = `{ label, x, y }`: the name and place of each garage, keyed by the id your garage script stores. Ids missing here show as that id, with no waypoint |
| `Config.Vehicles.impound` | `{ label = 'Impound Lot', x = 409.2, y = -1623.1 }` | where impounded vehicles are collected (`false` = no waypoint) |

## Calendar

Reminders also reach flip phones as a notification. Scripts can add events for one player, a number, a job or
everyone: `AddCalendarEvent` in docs/API.md.

| Option | Default | Meaning |
|---|---|---|
| `Config.Calendar.maxEvents` | `200` | a phone's own upcoming events |
| `Config.Calendar.titleLength` | `60` | characters in a title |
| `Config.Calendar.locationLength` | `80` | characters in a location |
| `Config.Calendar.notesLength` | `500` | characters in notes |
| `Config.Calendar.maxDurationDays` | `14` | longest event |
| `Config.Calendar.reminders` | `{ 0, 5, 15, 30, 60, 1440 }` | minutes before the start players can pick (`0` = at the start) |
| `Config.Calendar.keepDays` | `60` | finished events are deleted after this many days |

## Sounds and ringtones

Each sound is a native GTA sound (`{ type = 'native', name, set }`) or your own file (`{ type = 'file', file =
'name.ogg' }` in `web/sounds/custom/`). File sounds only play for the phone's holder; native ringtones are also heard
by players nearby.

| Option | Meaning |
|---|---|
| `Config.Sounds.keypress` / `select` / `back` / `hangup` | key and navigation sounds |
| `Config.Sounds.dialTone` | what the caller hears while the other phone rings (repeats) |
| `Config.Ringtones` | ringtones players pick in Settings; the first is the default. `{ id, label, type, name }` (`name` = a ped ringtone for `native`) or `{ id, label, type = 'file', file }` |
| `Config.MessageTones` | message tones, same format with `set` for native sounds |

## Animations

Base game animations; swap in your own. Each is `{ dict, name }`.

| Option | Meaning |
|---|---|
| `Config.Animations.enabled` | `true`: play animations and show the phone prop |
| `Config.Animations.onFoot` / `inVehicle` | `takeOut`, `read` (may be a list: one is picked at random), `type` (a text field is focused), `call`, `putAway` |
| `Config.Animations.bone` | `28422`: hand bone the prop attaches to |
| `Config.Animations.offset` / `rotation` | prop offset `{ x, y, z }` |

The Camera app and video calls use the game's own phone camera, which moves the arm itself; these animations pause
while it's up.

## Rate limits

`Config.RateLimit`: milliseconds between requests of one type from one player. Requests that come faster are refused.

| Option | Default | Covers |
|---|---|---|
| `default` | `150` | anything not listed (mostly reads: lists, pages) |
| `open` | `750` | opening the phone |
| `pin` | `1000` | PIN attempts |
| `write` | `400` | creating or editing contacts, settings and groups |
| `call` | `1000` | dialling, answering, hanging up |
| `signal` | `100` | video call connection messages |
| `share` | `5000` | sharing your number with players nearby |
| `photo` | `2500` | taking a photo |
| `pay` | `1500` | Bank payments, requests and business deposits / withdrawals |

## Server-only settings (`config/server.lua`)

A server script, never sent to players. Put keys here, not in `config/config.lua`.

| Option | Default | Meaning |
|---|---|---|
| `ServerConfig.Uploads.fivemanage.key` | convar `tml_phone_fivemanage_key` | your Fivemanage API key. Easiest in server.cfg: `set tml_phone_fivemanage_key "your-key"` |
| `ServerConfig.Uploads.fivemanage.url` | `'https://api.fivemanage.com/api/v3/file'` | upload endpoint |
| `ServerConfig.Uploads.custom` | `{ url = '', field = 'file', headers = {}, responseUrl = 'url' }` | any host that takes a multipart upload and replies with JSON; see `bridge/uploads/README.md` |
| `ServerConfig.Uploads.allowedHosts` | `{ 'fivemanage.com' }` | camera photos are only accepted from these hosts (and subdomains). Add your custom host here |
| `ServerConfig.Social.webhook` | convar `tml_phone_social_webhook` | a Discord webhook URL for Chirp and Lens reports (the post, its author, who reported it and why). Empty = off. Easiest in server.cfg: `set tml_phone_social_webhook "https://discord.com/api/webhooks/..."` |
| `ServerConfig.Social.logPosts` | `false` | also send every new post and comment to the webhook |

## Colours of the flip phone

`web/theme.css` holds the flip phone's shell, LCD and key colours as CSS variables, with ready-made alternatives in
the comments. It's loaded at runtime: edit and restart the resource, no rebuild needed. Smartphone colours are
`Config.Themes`.
