A handheld two-way radio for FiveM. Tune by keypad, channel buttons or presets. Emergency services get locked
frequency ranges with a member list, callsigns and a panic button. Built on pma-voice or SaltyChat.

Works with Qbox, QBCore, ESX, ox_core, ND_Core, vRP and standalone, and with ox_inventory, qb-inventory, ps-inventory,
qs-inventory, codem-inventory, core_inventory or no inventory. The adapters are in `bridge/` and aren't escrowed, so
you can edit them.

## Features

- **A real handheld.** Backlit LCD, keypad entry (`145.50`), CH+/CH- and VOL+/VOL- buttons, power button, status
  LED (green on, purple receiving, red transmitting) and a speaker grille. Fully keyboard driven too.
- **Restricted frequency ranges.** Lock any range to jobs with a minimum grade, on-duty only, or an ACE permission.
  Each range has its own label and colour on the display. The server checks every join, and with pma-voice the
  channels are also guarded inside pma-voice itself, so they can't be joined from outside the radio.
- **Who's on the channel (optional).** Turn on `Config.Radio.showMembers` for a member list in the radio's menu with
  names, callsigns and live talking dots on restricted frequencies. Off by default.
- **Callsigns.** Players set their own (`1-ADAM-12`); it shows in panic alerts, and in the member list and HUD when
  the member list is on.
- **Panic button.** On emergency ranges: an alarm, a flashing blip and a notification for everyone on the channel,
  with a cooldown. Optional GPS waypoint. Also a server event for your dispatch script.
- **Presets.** Press to tune, hold to save. Volume, mic click, callsign and presets are remembered per player.
- **HUD strip** (top right by default) with the frequency, range label and a receiving / transmitting indicator,
  shown while the radio is on, even with the UI closed. Server owners can turn it off; players can hide their own.
- **Everything on the radio.** Settings live in the radio's own MENU on its LCD: callsign, mic click, HUD.
- **Radio voice effect.** Voices over the radio get a real radio sound (handheld, digital or old presets, or your
  own values), built on pma-voice's audio submixes.
- **Realism.** No signal while dead or underwater. The radio is raised when you open it; cops and medics talk on a
  shoulder mic, everyone else raises the radio to their mouth.
- **Item or no item.** Require the radio item (it's checked continuously, so dropping it takes you off the air), or
  let everyone use `/radio`.
- **Exports and events** to put players on a frequency, read who's on one, and react to joins, leaves and panics.
- No database needed. English and Spanish included.

## Requirements

- FiveM server with OneSync
- pma-voice or SaltyChat (the radio runs without one, but nobody can be heard)
- One of the supported frameworks, or standalone with ACE permissions

## Install

See install/README.md.

## Use

- `/radio`, the `tml_radio` item, or a key bound in GTA settings opens the radio. Separate bindable keys turn it on
  and off and press panic without opening it.
- Turn it on with the power button, type a frequency and press **ENT** (or Enter). Arrow keys: up/down change
  channel, left/right change volume. Esc closes the radio; it stays on.
- Talk with your voice script's radio key (pma-voice: Left Alt by default). Close the radio first: while it's open,
  the game doesn't receive keys.
- **MENU** on the radio switches its screen to settings: callsign, mic click and HUD on/off (and the member list, when
  it's turned on). CH+/CH- or the arrow keys move, ENT selects, CLR goes back. Type a callsign on your keyboard.

## Config

Everything is in `config/config.lua`, commented per option. See [the config reference](/docs/tml-radio/config/). Text is in
`config/locales/`. Shell, LCD and key colours are CSS variables in `web/theme.css` (or `Config.Theme.vars`).

## Known limitations

- SaltyChat doesn't report who is talking, so the receiving indicator and talking dots need pma-voice.
- The nd_core, vrp, codem-inventory and core_inventory adapters are written from those projects' docs and haven't been
  checked on a live server. The adapter files are open, so they can be adjusted if your setup differs.

Update announcements are posted in the Discord.
