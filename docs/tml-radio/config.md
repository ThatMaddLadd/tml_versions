Everything is in `config/config.lua`. Every value is checked when the resource starts: a missing or invalid value
prints a warning in the server console and its default is used instead.

## General

| Option | Type | Default | What it does |
|---|---|---|---|
| `Config.Locale` | string | `'en'` | Language file in `config/locales/` (`en`, `es`) |
| `Config.Debug` | boolean | `false` | Extra console output |
| `Config.CheckForUpdates` | boolean | `true` | Console notice when a newer version exists |

## Bridge

| Option | Default | Values |
|---|---|---|
| `Config.Framework` | `'auto'` | `qbox` `qbcore` `esx` `ox_core` `nd_core` `vrp` `standalone` `custom` |
| `Config.Inventory` | `'auto'` | `ox_inventory` `qb-inventory` `ps-inventory` `qs-inventory` `codem-inventory` `core_inventory` `standalone` `custom` |
| `Config.Notify` | `'auto'` | `ox_lib` `qbcore` `esx` `standalone` `custom` |
| `Config.Voice` | `'auto'` | `pma-voice` `saltychat` `none` `custom` |

`Config.Standalone.jobs` (standalone framework only): job name → ACE permission that grants it.

## `Config.Radio`

| Option | Type | Default | What it does |
|---|---|---|---|
| `item` | string | `'tml_radio'` | Item name |
| `requireItem` | boolean | `true` | Players need the item to open and use the radio |
| `itemCheckSeconds` | number | `5` | How often players on a channel are checked for the item |
| `command` | string | `'radio'` | Chat command that opens the radio (`''` for none) |
| `key` | string | `''` | Default key to open the radio (`''`: players bind it themselves) |
| `powerKey` | string | `''` | Default key to turn the radio on/off without opening it |
| `minFrequency` / `maxFrequency` | number | `1.00` / `999.99` | The band players can tune to |
| `channelStep` | number | `1.00` | How far CH+ / CH- move |
| `defaultVolume` | number | `60` | 0-100, for players who never changed it |
| `volumeStep` | number | `10` | How much VOL+ / VOL- change the volume |
| `micClicks` | boolean | `true` | Default mic click (players can toggle it) |
| `presets` | number | `3` | Preset buttons, 0-6 |
| `showMembers` | boolean | `false` | Optional "who's on the channel" list next to the radio (restricted frequencies). Off: no list, no names sent |
| `showMembersOnOpen` | boolean | `false` | With `showMembers` on, also list members on unrestricted frequencies |
| `callsignLength` | number | `12` | Max callsign length |
| `disableWhenDead` | boolean | `true` | Dead players can't talk or hear |
| `disableUnderwater` | boolean | `true` | No signal while swimming underwater |
| `guardChannels` | boolean | `true` | Make pma-voice itself refuse restricted channels to anyone without access |

## `Config.Restricted`

A list of frequency ranges only some players can join. Ranges are inclusive and mustn't overlap.

```lua
{ from = 1.00, to = 9.99, label = 'Police', jobs = { police = 0, sheriff = 0 }, ace = 'tml_radio.police',
  onDutyOnly = true, panic = true, accent = '#3B82F6' },
```

| Key | Type | What it does |
|---|---|---|
| `from`, `to` | number | First and last frequency of the range |
| `label` | string | Shown on the radio and HUD while tuned in |
| `jobs` | table | Job name → minimum grade |
| `ace` | string or `false` | ACE permission that also grants access |
| `onDutyOnly` | boolean | Job members must be on duty |
| `panic` | boolean | The panic button works on this range |
| `accent` | string | Colour of the display strip and HUD label (`#RRGGBB`) |

A player who changes job, goes off duty or loses the permission is taken off the frequency straight away.

## `Config.VoiceEffect`

How voices sound over the radio. pma-voice only (SaltyChat adds its own effect), and it needs
`setr voice_useNativeAudio true` and `setr voice_enableSubmix 1` in your voice config.

| Option | Default | What it does |
|---|---|---|
| `enabled` | `true` | Use the effect; `false` = pma-voice's own radio sound |
| `preset` | `'handheld'` | A key in `presets`: `handheld` (classic analogue), `digital` (clearer), `old` (worn and crackly) |
| `volume` | `1.0` | 0.0-1.0, loudness of voices through the effect |
| `presets` | see config | Your own presets: `freq_low` / `freq_hi` (the band of the voice that gets through, Hz), `o_freq_lo` / `o_freq_hi` (the output band), `fudge` (distortion), `rm_mix` (crackle), `rm_mod_freq` (ring modulation, robotic; 0 = off) |

Change a value and restart the resource to hear it. A preset with a missing or non-number value turns the effect off
with a console warning.

## `Config.Panic`

| Option | Type | Default | What it does |
|---|---|---|---|
| `enabled` | boolean | `true` | Show the panic button |
| `key` | string | `''` | Default key for panic without opening the radio |
| `cooldown` | number | `30` | Seconds between panics from one player |
| `blipSeconds` | number | `60` | How long the blip stays |
| `blipSprite` / `blipColour` | number | `161` / `1` | Blip look |
| `waypoint` | boolean | `false` | Set a GPS waypoint to the panic |

## `Config.Animation`

While the radio UI is open, the player holds the radio. While talking, jobs in `shoulderJobs` use a **shoulder mic**
(no prop) and everyone else raises the **radio to their mouth** (with the prop).

pma-voice plays its own shoulder animation while talking. Set `setr voice_enableRadioAnim 0` in your voice config,
or the two play at once. The client console warns about this at start.

| Option | Default | What it does |
|---|---|---|
| `enabled` | `true` | Prop and animations |
| `prop` | `'prop_cs_hand_radio'` | Radio prop model |
| `talkWhenClosed` | `true` | Play the talk animation with the UI closed too |
| `hold.dict` / `hold.vehicleDict` | `'cellphone@'` / `'cellphone@in_car@ds'` | Holding the radio on foot / in a vehicle |
| `hold.enter` / `hold.exit` | `'cellphone_text_in'` / `'cellphone_text_out'` | Raise it when the UI opens (holds the last frame), put it away when it closes |
| `hold.bone` | `28422` | Bone the prop is attached to while holding (right hand) |
| `shoulderJobs` | `{ 'police', 'sheriff', 'bcso', 'sasp', 'ambulance', 'ems', 'fire' }` | Jobs that use the shoulder mic |
| `shoulder.dict` / `shoulder.clip` | `'random@arrests'` / `'generic_radio_chatter'` | Shoulder mic animation |
| `mouth.dict` / `mouth.clip` | `'ultra@walkie_talkie'` / `'walkie_talkie'` | Radio to mouth (streamed with the resource, in `stream/`) |
| `mouth.bone` / `mouth.offset` / `mouth.rotation` | `18905` / `{ 0.14, 0.03, 0.03 }` / `{ -105.877, -10.9432, -33.7212 }` | Prop position for radio to mouth (left hand) |
| `mouth.fallbackDict` / `mouth.fallbackClip` / `mouth.fallbackBone` | `'cellphone@'` / `'cellphone_call_listen_base'` / `28422` | Base game animation used if the streamed one isn't loaded |

## `Config.Hud`

| Option | Default | What it does |
|---|---|---|
| `enabled` | `true` | Frequency strip while the radio is on. `false` = never shown |
| `playerToggle` | `true` | Players can turn their own HUD on or off in the radio's MENU |
| `position` | `'top-right'` | `top-left` `top-right` `bottom-left` `bottom-right` |
| `showTalker` | `true` | Name or callsign of whoever is talking (needs `showMembers`; otherwise it shows "Receiving") |

## `Config.Theme`

| Option | Default | What it does |
|---|---|---|
| `position` | `'bottom-right'` | Where the radio opens: `top-left` `top-right` `bottom-left` `bottom-right` `center` |
| `sounds` | `true` | Key beeps, connect chirp and panic alarm |
| `volume` | `0.5` | 0.0-1.0 |
| `vars` | `{}` | Any CSS variable from `web/theme.css`, e.g. `{ ['--shell-mid'] = '#1c2638' }` |

## `Config.RateLimit`

Milliseconds between requests of one kind from one player: `default` (150), `join` (500), `open` (750).
