A pager for every player on the server, with retro looks and modern features: an LCD screen, four hardware buttons and a translucent purple shell. Each character gets a pager number; send short messages to other numbers, save contacts so you do not have to remember them, block spammers, and read messages that arrived while you were offline. Emergency departments also get a dispatch alerts tab with tones, urgent priority and "responding" counts.

Works with Qbox, QBCore, ESX, ox_core, ND_Core, vRP and standalone; ox_inventory, qb-inventory, ps-inventory, qs-inventory, codem-inventory, core_inventory or no inventory. Adapters are in `bridge/` and are not escrowed, so they can be edited.

## Requirements

- FiveM server with OneSync
- oxmysql (tables are created automatically)
- One of the supported frameworks (or standalone with ACE permissions)

## Install

See install/README.md.

## Use

- Everyone: `/pager` or **F10** opens the pager (rebindable in GTA settings), or use the `tml_pager` item from your inventory. The LCD shows your number at the top.
  - **Keys:** `^` `v` move, **OK** selects, `<` goes back (from the main menu it switches the pager off). On a keyboard: arrow keys, Enter, Esc. Mouse wheel scrolls.
  - **Messages**: inbox, `*` marks unread, OK on a message to reply, save the number, or block it.
  - **Compose**: type a number or a contact name; in the To field, up/down steps through your saved contacts.
  - **Contacts**: add, message, block or remove. Blocked numbers can still "send", but you never receive it and they cannot tell.
- Item mode: the item always opens the pager when used. Set `Config.Pager.requireItem = true` to also require carrying it to open, send or receive.
- Dispatch: jobs in `Config.Dispatchers` (or ACE `tml_pager.dispatch`) get a **Dispatch** menu item to alert a department. Responders get a tone and an LCD strip on screen and press OK to respond.
- Other scripts: exports `SendPage` and `GetNumber`, see [the API docs](/docs/tml-pager/api/).

## Config

Everything is in `config/config.lua`, commented per option. Text is in `config/locales/`. Shell, LCD and button colours are CSS variables in `web/theme.css` (or `Config.Theme.vars`); the file has ready-made alternatives in comments (blue or graphite shell, amber backlight).

## Known limitations

- No number-change or "share my number with a nearby player" feature yet.
- The nd_core, vrp, codem-inventory and core_inventory adapters have not yet been checked on a live server. The adapter files are open, so they can be adjusted if your setup differs.

Update announcements are posted in the Discord.
