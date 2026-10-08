FiveM resource for TML Monitor. It measures your server and sends the data to the TML Monitor dashboard. Stage 1
(0.1.x) collects players online, server frame time and hitches, entity counts (OneSync), and connects, joins and
drops with reasons and play time.

## Install

1. Drop `tml_monitor` into your resources and `ensure tml_monitor` (after your framework).
2. Add your server key from the dashboard to `server.cfg`, **with `set`** (never `setr` or `sets`, which would send
   it to clients):

   ```
   set tml_monitor_key "tmk_..."
   ```

3. Restart. The console prints `Connected to TML Monitor as "<your server>"`.

No SQL and no items: nothing is stored in your database.

## Config

`config/config.lua` (open): how often data is sent, the offline buffer size and age, how often stats are measured,
and which collectors are on. Every option has its type and default beside it; bad values print a warning and fall
back to the default.

`Config.Debug = true` also prints a self-check every minute: average server time per frame, the per-tick loop's
cost, the stats pass's cost, and batches sent and waiting. It times the resource's own code (CfxLua `os.nanotime`).

Optional convar for testing against a local dev service: `set tml_monitor_url "http://127.0.0.1:8090"`.

## What is sent

- Every 15 s: players online and slots, server frame time (p50 / p95 / max), hitches per minute (frames over
  100 ms), entity counts by type.
- Every 15 s: the machine's CPU and memory use and FXServer's own (`Config.Collect.host`). Only percentages and
  megabytes; nothing about other programs on the machine.
- Every 15 s: who is online (server id, name, ping, FPS, time online) for the dashboard's live list, plus median and
  95th-percentile ping and median and low-end FPS.
- Every 30 s from each player: their average FPS and slowest 5 s window (`Config.Collect.clientFps`). The client
  counts frames on a 5 s timer, never per frame.
- Connects, joins and drops: the player's name, their identifiers (license, Discord, FiveM, Steam...) and the drop
  reason. **IP addresses are never sent.** For the player's country (`Config.Collect.location`), only the network
  part goes with the connect (`81.2.69` of `81.2.69.142`, or the first three groups of an IPv6 address); the service
  looks up the country and stores only that. Private and local addresses send nothing.
- Logs (each switchable in `Config.Logs`): money changes with reason and balance, items moved/given/bought/crafted
  or added by scripts, job changes, kills with killer, weapon and distance, txAdmin actions and in-game menu use, and
  chat (off by default). Other scripts add their own lines with `exports.tml_monitor:log` (`docs/API.md`).
- Anti-cheat signals (`Config.AntiCheat`, OneSync): moving too fast, teleporting, super jump, god mode, health or
  armour over the maximum, weapon damage over the limit, hits with a weapon the shooter isn't holding, too many hits a
  second, hits from too far away, weapons that aren't in the inventory or are banned, explosion spam or hidden or
  boosted explosions, entity spam, bursts of
  the game events cheat menus abuse (giving or taking weapons, stopping other peds, fires, particle effects,
  projectiles), weapons given to or taken from another player, projectiles credited to someone else or starting far
  away, oversized particle effects, and a player's client scripts going silent, each
  with the measured value, the limit and where it happened. They only report; nothing is blocked. The service also
  flags an account that shares a PC, Discord, Steam, FiveM or Xbox identifier with another account (a likely alt),
  with how strong the link is.
- A device ID (`Config.AntiCheat.deviceId`): a random ID the resource gives each player's game, kept in the game's
  own storage on their PC. Only a hash of it is sent, salted with a value unique to your server, so it can't be
  matched across servers or copied off the dashboard. Clearing the game's cache resets it.
- A browser fingerprint (`Config.AntiCheat.fingerprint`): a hidden page in the game's built-in browser reads its user
  agent, language, time zone, screen size, CPU threads, memory, graphics card name and how it draws a test image.
  The values are joined and hashed with your server's salt on your server; only the hash is sent. Same purpose as
  the device ID, but it survives clearing the game's cache.
- Screenshots (`Config.AntiCheat.screenshots`, needs screenshot-basic): when a player gets an alert-level flag, a
  JPEG of their screen, at most one per player every 5 minutes. Kept with the flag for 30 days, then deleted. The
  service reads the text on it to spot cheat menus.
- The names of the resources running in each player's game, compared on your server with the ones it runs; only
  unknown names are sent, with a flag.
- Monitor start and stop.

Data goes out in batches every 5 s. Each batch is saved in resource KVP first and deleted once the API confirms it, so
an outage or restart loses nothing younger than `Config.Transport.bufferMaxHours`.

## Anti-cheat

Every check runs on the server from what OneSync already knows (positions, health, damage, explosion, entity and
other game events), so a modified client can't simply switch it off. The one exception is `clientSilent`: the
resource's small client script reports every 30 seconds, and a connected player whose reports stop has had their
client scripts stopped, which is what executors do. Staff are never flagged for an admin mode they turned on
in txAdmin, qbx_adminmenu or tml_adminpanel. Never flagged at all:

- Players on the dashboard's exemption list (Anti-cheat page). The list reaches the server within about 15 seconds
  and is kept in KVP, so it holds through restarts.
- Players with any ACE in `Config.AntiCheat.exemptAces`. The default includes `command`, which standard server.cfgs
  give `group.admin`, so admins are covered out of the box. To cover another group:

  ```
  add_ace group.moderator tml_monitor.exempt allow
  ```

**Test mode:** with `Config.Debug = true` and `Config.AntiCheat.testMode = true` (the default), every check uses very
low limits and nobody is exempt, so you can trip each one by just playing. Turn Debug off before going live. Test mode
also drops the grace periods below (except the first minute after joining), so loading into a character and picking a
spawn get flagged too; that's expected.

Short grace periods follow joining, character changes, picking a spawn point (once per character load), revives,
jail and admin teleports. God mode is only flagged when an invincible player also moves `godMode.meters`, because
character select, spawn pickers and clothing menus make players invincible while they stand still.

Most door, elevator and interior scripts fade the screen out before moving the player. With `moveHints` on (the
default), the client says when the screen fades out, a player switch starts or a cutscene plays, and the teleport
check pauses for 8 seconds. Any client could send that, so it works at most once every 15 seconds and never pauses
the speed check. Scripts that move players without a fade have four more ways out of the movement checks:

- **Allow the route on the dashboard.** Open a teleport flag and press *Allow this route*: teleports that start near
  one end and land near the other (20 m by default) are no longer flagged. This works for any resource, including
  ones that move the player with no event at all (doors into interiors, elevators), and a cheater gains nothing from
  it: the only teleport it allows is the one the script makes.
- **Allow the area on the dashboard.** Open a speed or super jump flag and press *Allow this area*: inside it (50 m
  by default) the speed, teleport and super jump checks don't run. For race tracks, stunt parks and event arenas;
  god mode, damage, explosions and spawning are still checked there.
- **`Config.AntiCheat.graceEvents`:** events a script already sends when it moves someone, with how many seconds to
  pause speed, teleport and super jump for. Clients can send events too, so each one only works once every 30 seconds
  per player and never pauses god mode, health, damage, explosion or spawn checks.
- **`exports.tml_monitor:exempt(source, seconds)`** from your own server scripts (`docs/API.md`).

Routes, areas and graceEvents still apply in test mode, so you can try them. Each player is flagged at
most once per check per `cooldownSeconds`; the next log says how many more times it happened.

Positions and the weapon in each player's hands are checked once a second per player, spread across ticks in small
groups. A weapon that isn't in the player's inventory is only flagged if it's still in their hands 4 seconds later,
and only with an inventory adapter that can tell (`HasWeapon` in `bridge/README.md`; ox_inventory can).

**Reported by the player's game.** A few checks can only run in the game itself. A cheat can hide them, so their
flags say *reported by the player's game*, count for less in the risk score, and are checked again on the server
(types, sizes, how often) before anything is flagged:

- **Developer tools** (`devtools`): someone opening the developer tools on the game's web pages, usually to tamper
  with another script's UI. Not checked for exempt players or with `Config.Debug` on, since staff and developers open
  them legitimately (the check pauses them every couple of seconds).
- **Resources the server never sent** (`injectedResources`): every resource a game runs comes from your server, so
  one your server doesn't have was put there by an executor or a menu.
- **`clientChecks`:** infinite ammo and invisibility (on), free camera and night or thermal vision (off by default:
  CCTV, decorating and spectate scripts move the camera, and police helicopters use thermal).

**Your own events.** `Config.AntiCheat.watchedEvents` lists events from your other scripts with a limit and, if you
like, the argument types they take. TML Monitor listens alongside them and flags anyone sending one too often or with
the wrong arguments. It never changes what your script does.

**Banned models and explosion types.** `entities.bannedModels` (props menus troll with) and
`explosions.bannedTypes` are flagged the moment one appears.

**Screenshots.** With screenshot-basic running, every alert-level flag gets a screenshot of the player's screen (at
most one per player every `screenshots.everySeconds`). It shows in the flag on the dashboard, and the service reads
the text on it: a known cheat menu's name becomes its own flag.

**Scripts that do these things on purpose** can say so for one player at a time with
`exports.tml_monitor:allow(source, kind, seconds)`, and add allowed areas with `exports.tml_monitor:addZone`
(`docs/API.md`).

The dashboard gives each flagged player a **risk score**: each different kind of flag adds once, weighted by how
strongly it points to cheating on its own, up to 100. It shows on the Anti-cheat page (*Players to look at*) and next
to players in *Who's online*.

## Deviations from STANDARDS.md

- **No database:** everything is stored by the TML Monitor service, so there is no `install.sql` and no oxmysql
  dependency.
- **One extra global, `Monitor`:** the server files share state through it. `Config` and `L` are the usual globals.
- **A read-only, server-only bridge:** TML Monitor never gives or takes anything, so its framework contract is
  `GetCharacter`, `GetJob`, `GetMoney` plus money and job events instead of the full STANDARDS contract, and there
  are no target/notify/banking categories. Framework adapters: Qbox, QBCore, ESX, standalone, custom. Inventory:
  ox_inventory, standalone, custom (other inventories log through the export until they get an adapter). See
  `bridge/README.md`.
- **No NUI:** the only client script is the FPS sampler; everything players could see lives on the dashboard.
- **One per-tick loop (`Wait(0)`) on the server:** frame timing has to be measured every tick. A timer that
  wakes every N ms can only see lag in whole ticks, which made every healthy server read ~50 ms late. The loop only
  stores the time since the last tick in a reused array (no allocations), and must stay within the 0.01 ms server
  budget in `resmon`.
- **One JavaScript file (`server/host.js`):** CPU and memory use can only be read from server JS (Node's `os` and
  `process`); Lua has no access. It does no work of its own and only answers when the stats pass asks.
- **Plain JSON, not gzip:** FiveM's Lua has no compression, so batches are sent uncompressed (well under the 1 MB
  limit).
