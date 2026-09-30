## Install

**The phone doesn't open.**
Check that `ensure tml_phone` comes after oxmysql, your framework and your inventory in server.cfg. With an inventory,
the player needs a phone item (`tml_flipphone` or `tml_smartphone`; add them from `install/`). A phone at 0% battery
shows a charge screen instead: charge it in a vehicle with the engine running, or `/phonebattery 100` as an admin.

**Which framework, inventory and voice script is it using?**
The server console prints them on start: `framework=qbox inventory=ox_inventory notify=ox_lib voice=pma-voice ...`.
Force one in `config/config.lua` (`Config.Framework = 'esx'`, ...). `standalone` / `none` means nothing was found:
usually a start-order problem.

**The console prints config warnings on start.**
The config is checked on start. Each warning names the option, what was wrong with it, and the default it used
instead, so the phone still works. Fix the value in `config/config.lua` and restart.

**Using the item does nothing (ox_inventory).**
The item definition must contain `server = { export = 'tml_phone.use_item' }` and `consume = 0`, exactly as in
`install/ox_inventory.txt`.

**Every phone item should have its own number, but they share one.**
That's `'character'` mode. `Config.Phones.mode = 'unique'` (the default) needs an inventory that stores data on items:
ox_inventory, qb-inventory, ps-inventory and qs-inventory do. On codem-inventory and core_inventory the phone falls
back to one number per character and says so in the console; their adapters explain how to switch it on if your
version supports item data. ESX's own default inventory has no adapter, so there the phone runs without items.

**Can I rename the resource?**
Keep the name `tml_phone`: the item export (`tml_phone.use_item`), the app SDK address and other scripts' exports use
it.

**Can I test alone?**
With `Config.Debug = true` you can text and call your own number, and admins get `/tml_phone_*` test commands. Turn
it off on a live server.

## Calls

**Calls connect but nobody hears anything.**
The voice script carries call audio. Check the console line shows `voice=pma-voice` (or `saltychat`). `voice=none`
means none was detected: start your voice script before `tml_phone`.

**Service numbers (911) don't ring anyone.**
Only players with a job in the service's `jobs` list ring, and only while on duty if `onDutyOnly = true`. They also
need a phone that isn't dead.

**Video calls show no picture.**
The two players' games connect directly for video. Some home routers block that; add a TURN server to
`Config.Calls.videoIce` (see `docs/CONFIG.md`, "Video and IP addresses"). A TURN server also stops players from seeing
each other's IP address when `Config.Calls.videoRelayOnly = true`.

## Camera and photos

**The Camera app says the camera isn't set up.**
It needs an image host to store the photos (the phone takes them itself; no screenshot resource is needed). For
Fivemanage, set the key in server.cfg: `set tml_phone_fivemanage_key "your-key"`, then restart. Full guide:
`bridge/uploads/README.md`.

**Do I need screencapture or screenshot-basic?**
No. The phone takes its own photos. You can keep those resources for other scripts; the phone doesn't use them.

**The console says the upload failed with HTTP 401.**
The image host refused the key. Copy it again from your Fivemanage dashboard and set it with double quotes, no spaces
inside. Restart the server after changing server.cfg.

**A photo sometimes fails to save.**
A dropped connection or a busy host. The phone sends the same photo again (`Config.Camera.uploadRetries`, 2 by
default) before giving up; if it still fails, the console shows the host's answer.

**"Add from link" refuses my image.**
Only direct image links from `Config.Photos.linkHosts` are accepted: the address of the image file itself
(`https://i.imgur.com/abc.png`, not `https://imgur.com/abc`). Discord links expire, so they aren't allowed by default.

## Music

**A song won't play.**
Songs must be direct audio links (`https://...mp3` / `.ogg`), not YouTube or Spotify pages, from a site in
`Config.Music.linkHosts`. Staff with `Config.Music.curateAce` can add curated songs from any https link.

**Nobody else hears the speaker.**
The speaker button must be on, and other players within `Config.Music.speaker.distance` (20 m) hear it, quieter the
further away they are. It works without any other script; `Config.Speaker = 'xsound'` uses xsound instead.

## Maps

**The map is blank or says it couldn't be loaded.**
The Maps app draws the game's own map from each player's game files, so it needs nothing from you, and map mods
(postal codes, satellite style) show through. When the textures can't be loaded, the app says so and shows a plain
grid with the markers instead.

## Money

**Where did the Wallet app go?**
It's the Bank app now (1.1.0): same id and settings (`Config.Apps.wallet`, `Config.Wallet`), so phones that had Wallet
installed have Bank, and your config works unchanged. It adds payment requests and the job account.

**Business doesn't show in the Bank app.**
The player's job must be in `Config.Wallet.business.jobs` with at least that grade. If it shows "not available", your
banking script (`Config.Banking`) has no account under the job's name: create one there, or use
`bridge/banking/custom.lua` to map job names to your account names.

**A Bank payment is refused.**
The receiver must be online and carry that phone, the amount must be between `Config.Wallet.minAmount` and
`maxAmount`, and the sender must have the money: the phone never lets a bank balance go negative, even on frameworks
that allow it.

**Payments don't show in my banking app.**
Check the console line shows your banking script (`banking=renewed-banking`, ...). `banking=framework` means none was
detected; the payment still happens, it just isn't listed as a statement.

## Chirp, Lens, Marketplace, News, Garage

**Is a Chirp / Lens account tied to the phone or the character?**
Neither: it's a handle and a password. Players log in on any phone, and the phone stays logged in until someone logs
out in the app, so a stolen phone can post as its owner until they change the password (which logs every other phone
out). `Config.Social.accountsPerCharacter` limits how many accounts one character can create.

**How do I moderate Chirp and Lens?**
Give moderators `Config.Social.moderatorAce` (default `tml_phone.admin`). In the app they can delete any post (its "…"
menu) and suspend accounts or give the verified badge (a profile's "…" menu). Set `ServerConfig.Social.webhook` in
`config/server.lua` to get player reports in Discord. Scripts can do the same with `SetSocialBanned`,
`SetSocialVerified` and `DeleteSocialPost`.

**Photos don't show up to pick in Chirp, Lens, Marketplace or News.**
The pickers use the phone's gallery, so players need photos taken with the Camera (an image host must be set up,
`Config.Uploads`) or added from a link in Photos.

**Who can write News?**
The jobs in `Config.News.jobs` (job name = lowest grade), or anyone with the `tml_phone.news` ACE. Scripts can publish
with `PublishNews`.

**The Garage app shows no vehicles.**
The console prints which garage adapter was picked (`garage=...`). If your garage script isn't jg-advancedgarages or
cd_garage, the framework adapter reads your framework's own vehicle table; turn on `Config.Debug` to see a missing
table or column, then edit the query in `bridge/garage/` (or use `garage/custom.lua`). Garage names and waypoints come
from `Config.Vehicles.garages`, keyed by the id your garage script stores.

## Other

**A player forgot their PIN.**
Wrong guesses lock the phone for longer each time. An admin can remove the PIN from a script, such as
your admin menu: `exports.tml_phone:ResetPin('555-0123')`. A factory reset (`FactoryReset`) wipes the phone completely.

**How do I stop cuffed or dead players using their phone?**
Call `exports.tml_phone:SetPhoneBlocked(source, 'police:cuffs', true)` from your cuff script (server), or the client
export of the same name from a death script, and `false` to lift it. Each script uses its own reason, so they never
cancel each other out. See `docs/API.md`.

**Can I add another language?**
Copy `config/locales/en.lua` to a new file (`fr.lua`), translate the values, and set `Config.Locale = 'fr'`. Players
can also pick it for their own phone during setup. Missing keys fall back to English.

**The phone is too big or too small on my screen.**
Each player sets their own size: smartphone Settings > Display > Phone size, flip phone Settings > Phone size. It's
kept on their computer, so it follows them to every phone. You set the default and the limits in `Config.Display`.

**Can I change the look?**
Players pick a design (TML Rail, Island, Classic, Pear), a colour theme, light or dark mode, and a wallpaper in
Settings. You choose which designs and themes exist (`Config.Designs`, `Config.Themes`). The flip phone's colours are
in `web/theme.css`.

**My framework or inventory isn't in the list.**
Everything in `bridge/` is open and editable. Copy the closest adapter or the `custom.lua` template;
`bridge/README.md` explains each contract.
