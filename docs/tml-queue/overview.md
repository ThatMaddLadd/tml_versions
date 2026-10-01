A connection queue for FiveM. When the server is full, players wait in line on a live queue card that shows their
position, priority, time waited and an estimated wait. Priority comes from Discord roles, fixed identifiers, or timed
priority you add by command (ideal for selling priority on Tebex).

Runs entirely on the server and works with any framework, or none.

## Features

- **Priority from anywhere.** Discord roles (built-in bot, or Badger_Discord_API), fixed identifiers in the config,
  and timed priority in the database (`queue_addprio discord:123 50 30 VIP` = 50 points for 30 days). The highest
  that applies wins.
- **Live queue card.** Server name, logo, position "4 of 12", priority label, time waited, estimated wait, a rotating
  tip and up to three link buttons (Discord, website, store).
- **Reconnect grace.** Players who crash or time out get top priority for a few minutes, so they don't lose their
  place. Quitting on purpose doesn't count (configurable).
- **Keeps your place.** Reconnecting while still in line keeps your original position.
- **Reserved slots.** Keep slots free for staff or VIPs above a priority level.
- **Whitelist.** Off, by Discord role, by a database list managed with a command, or either.
- **Requirements.** Optionally require Discord, Steam, or membership of your Discord server.
- **Fair slot counting.** Players who were let in but are still loading hold their slot; players who never finish
  loading, or who another resource refuses, give it back.
- **Admin commands** (console, txAdmin or ACE): list the queue, add and remove priority, manage the whitelist, move
  someone to the front.
- **Exports and events** for priority, whitelist and queue info.
- English and Spanish included.

## Requirements

- FiveM server
- oxmysql (tables are created automatically)
- Optional: a Discord bot (set up in 2 minutes, see `config/server.lua`) or Badger_Discord_API, for role priority and
  Discord whitelists

## Install

See install/README.md.

## Selling priority on Tebex

Add a command to your Tebex package (Game server commands, run "when the player is online" or not, as you prefer):

```
queue_addprio fivem:{id} 50 30 VIP
```

This gives 50 points for 30 days. `{id}` is the buyer's Cfx.re account id in Tebex's FiveM commands, which matches
the player's `fivem:` identifier. Check Tebex's placeholder list for your store if you collect a different
identifier, such as Discord. Buying again replaces the old priority with the new one (same identifier).

## Config

Everything is in `config/config.lua` (and the Discord bot token in `config/server.lua`), commented per option. See
[the config reference](/docs/tml-queue/config/). Text is in `config/locales/`.

## Known limitations

- The queue card is an Adaptive Card. FiveM draws it in its own connecting screen, so its colours follow FiveM, not
  the TML theme.
- Ban checks are not part of the queue. TML Admin Panel and other ban systems check bans themselves on connect, and
  the queue gives their slot back if they refuse the player.

Update announcements are posted in the Discord.
