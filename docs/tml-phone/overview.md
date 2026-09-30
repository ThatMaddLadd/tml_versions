A flip phone and a smartphone on one shared core. Both run on the same numbers, contacts and messages, so a player can
text from a smartphone to a friend's flip phone. Every phone item can have its own number and data, which move with
the item when it's given away, dropped or stolen.

Works with Qbox, QBCore, ESX, ox_core, ND_Core, vRP and standalone, and with ox_inventory, qb-inventory, ps-inventory,
qs-inventory, codem-inventory, core_inventory or no inventory. The adapters are in `bridge/` and aren't escrowed, so
you can edit them.

## Features

- **Two devices.** A retro flip phone with a keypad, and a smartphone with four designs (TML Rail, Island, Classic,
  Pear), eight colour themes, light and dark mode and wallpapers.
- **Calls** through pma-voice or saltychat: ringtones nearby players hear, anonymous calls (`#31#`), call log,
  favourites, and **video calls** where each player's camera films their own face while they walk around.
- **Messages**: direct and group chats, photos and locations in messages, blocking, sharing your number with players
  nearby.
- **Service numbers**: 911 / 311 style numbers that ring a job's phones at once or in turn, text requests
  responders answer in the Services app, and alerts in your dispatch script.
- **Apps**: App Store, Phone, Messages, Contacts, Settings, Calendar, Calculator, Notes, Clock (alarms, timer,
  stopwatch), Mail (with buttons scripts can add), Ads, Maps (the game's own map), Camera and Photos, Music (with a
  speaker nearby players hear), Dark Chat, and Snake on the flip phone.
- **Chirp and Lens**: a public text feed and a public photo feed on one set of accounts. Handles and passwords (log
  in on any phone, stay logged in until you log out), follows, likes, replies and comments, reposts and quotes,
  @mentions, #hashtags and trending tags, notifications, verified badges, and moderation: reports to a Discord
  webhook, deleting posts, suspending accounts, a blocked-words list.
- **Marketplace**: listings with up to four photos, categories, search, saved listings, mark as sold; buyers message
  or call the seller.
- **Bank**: balance and history, paying any number, asking a number for money (they pay or decline in their app), and
  the job's own account for its bosses (deposit, withdraw, activity) through your banking script.
- **Garage**: the character's vehicles, out, stored or impounded, with fuel and damage, and a waypoint to the
  vehicle, its garage or the impound. Reads jg-advancedgarages, cd_garage or your framework's vehicle table
  (qb-garages, qbx_garages, esx_garage), through an open bridge you can edit.
- **News**: articles from the jobs you choose, with a cover photo; breaking news notifies every phone with the app.
  Scripts publish with an export.
- **Battery** that drains with use and charges in vehicles.
- **First-boot setup**: language, owner name, phone name and passcode, readable by other scripts.
- **Your own apps**: add a smartphone app from any resource with `RegisterApp` and the small App SDK.
- **Server-authoritative**: every request is checked and rate-limited on the server; PINs lock out after wrong
  guesses; API keys never reach players.

## Requirements

- A FiveM server with OneSync
- [oxmysql](https://github.com/overextended/oxmysql) (tables are created automatically)
- One of the supported frameworks, or standalone

Optional: an inventory for phone items, pma-voice or saltychat for call audio, an image host such as Fivemanage for
the Camera and for photos in Chirp, Lens, Marketplace and News (the phone takes the photos itself; no screenshot
resource needed), ox_lib for notifications, a dispatch, banking or garage script from the supported lists.

## Install

1. Put `tml_phone` in your resources folder.
2. `ensure tml_phone` in server.cfg after oxmysql, your framework, inventory and voice script.
3. Add the phone items for your inventory and copy their images (`install/`).
4. Start the server.

Details, item snippets for every inventory and photo upload setup: install/README.md.

## Use

- `/phone` or **F2** opens the phone (rebindable in GTA's key settings), as does using a phone item.
- **Left Alt** hands the mouse back to the game while the phone stays on screen (walk, drive, aim the camera); press
  it again to use the phone with the mouse.
- Smartphone: apps are added and removed in the App Store, and arranged by holding an icon. The home screen has up to
  five pages: swipe, scroll the mouse wheel, use the arrow keys or click the dots. While moving an icon, hold it at the
  side of the screen to carry it to the next page.
- Too big or too small for your screen? Settings > Phone size (smartphone: under Display). It's saved on your own
  computer and used for every phone you carry.
- Admins (`add_ace group.admin tml_phone.admin allow`): `/phonebattery <0-100> [id]`, remove anyone's ad or
  listing, add songs everyone gets in Music, moderate Chirp and Lens (delete posts, suspend accounts, verified
  badges), take down news articles.
- News writers: the jobs in `Config.News.jobs` (default: `reporter`), or anyone with `tml_phone.news`.

## Documentation

- [the config reference](/docs/tml-phone/config/): every option
- [the API docs](/docs/tml-phone/api/): exports, events and the App SDK
- [the FAQ](/docs/tml-phone/faq/): common questions
- bridge/README.md: adapters and how to write your own
- bridge/uploads/README.md: photo uploads and image hosts

## Config

Everything is in `config/config.lua`, with a comment on every option; keys go in `config/server.lua`. Text is in
`config/locales/` (English and Spanish). The config is checked on start: a wrong value prints a readable warning and
the default is used. The flip phone's colours are CSS variables in `web/theme.css`.

## Notes

- There is no target-script bridge: nothing in the phone is placed in the world.
- ESX's own default inventory has no adapter, so on ESX without ox_inventory, core_inventory or codem-inventory the
  phone runs without items (everyone has one).
- codem-inventory and core_inventory run in one-number-per-character mode out of the box; their adapters explain how
  to enable per-item phones.
- Battery levels are saved once a minute, so a change in the last minute before a server stop is lost.

Update announcements are posted in the Discord.
