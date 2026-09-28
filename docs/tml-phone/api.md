Exports and events for other resources. Phone numbers are accepted in any format (`'555-0123'`, `5550123`) and
returned as digits; use `FormatNumber` to show one the way your server formats them.

Where a function takes a `target`, it's either a player's server id (their **active phone**: the last one they used,
or the first one they carry) or a phone number.

Functions that can fail return `nil` or `false` plus an **error code** string (`'not_found'`, `'bad_request'`, ...).

## Contents

- [Server exports](#server-exports): [phones](#phones) (incl. blocking, notifications, contacts) ·
  [messages](#messages) · [calls](#calls) · [evidence](#evidence-mdt-and-police-scripts) ·
  [custom numbers](#custom-numbers) · [mail](#mail) · [calendar](#calendar) · [wallet](#wallet) · [ads](#ads) ·
  [music](#music) · [battery](#battery) · [script apps](#script-apps)
- [Server events](#server-events)
- [Client exports](#client-exports)
- [Client events](#client-events)
- [App SDK](#app-sdk) (your own smartphone app)
- [Permissions](#permissions)

## Server exports

### Phones

#### `GetNumber(src)` → `string|nil`

The number of the player's active phone, or `nil` when they carry none.

```lua
local number = exports.tml_phone:GetNumber(source)
```

#### `GetNumbers(src)` → `string[]`

Numbers of every phone the player carries.

#### `HasPhone(src)` → `boolean`

Does the player carry at least one phone?

#### `GetSourceFromNumber(number)` → `number|nil`

Server id of the player carrying that number's phone.

#### `GetPhoneByNumber(number)` → `table|nil`

```lua
{
  number = '5550123', display = '555-0123',
  device = 'smart',               -- 'flip' | 'smart'
  owner = 'ABC12345',             -- character identifier of the owner, or nil
  serial = 'K3J9...',             -- the item's serial (unique phones), or nil
  hasPin = true,
  name = "Sam's phone",           -- from first-boot setup; nil until it's done
  ownerName = 'Sam Carter',
  language = 'en',
  battery = 87,                   -- whole percent (100 with Config.Battery off)
}
```

#### `FormatNumber(number)` → `string`

Digits in the server's display format (`Config.Numbers.format`).

#### `SetNumber(number, newNumber)` → `boolean, err?`

Gives a phone a new number. Errors: `'not_found'`, `'number_invalid'`, `'number_reserved'`, `'number_taken'`.

#### `FactoryReset(number)` → `boolean, err?`

Wipes everything on the phone: contacts, blocks, messages, call log, notes, alarms, calendar, mail, ads, places,
Dark Chat, Wallet history, photos, songs, settings, home screen, first-boot setup and PIN. The number stays.

#### `ResetPin(number)` → `boolean, err?`

Removes a phone's PIN.

#### `GetSettings(src)` → `table|nil`

A copy of the active phone's settings (theme, ringtone, `silent`, `dnd`, `airplane`, ...).

#### `IsAirplaneMode(src)` → `boolean`

#### `SetPhoneBlocked(src, reason, blocked)` → `boolean, err?`

Stops a player using their phone (`true`) or lets them again (`false`): for handcuffs, being knocked out, a
cutscene. While blocked the phone won't open (the player is told they can't use it right now), every request is
refused, they can't answer calls, and service numbers don't ring them. Blocking also puts the phone away and ends a
call they're on.

Each script blocks under its own `reason` (up to 64 characters, 16 per player), so one script lifting its block never
lifts another's: the phone is free once every reason is lifted. Blocks are cleared when the player leaves.

```lua
exports.tml_phone:SetPhoneBlocked(target, 'police:cuffs', true)   -- cuffed
exports.tml_phone:SetPhoneBlocked(target, 'police:cuffs', false)  -- uncuffed
```

Errors: `'not_found'` (no such player), `'bad_request'`. Reasons starting with `client:` belong to client scripts
(the client export of the same name).

#### `IsPhoneBlocked(src)` → `boolean, reasons`

`reasons` lists every reason it's blocked under.

#### `SendNotification(target, data)` → `boolean, err?`

A notification on a player's phone, from any script, without registering an app: `data = { title, body?, icon? }`.
Title up to 60 characters, body up to 200. `icon` is one of the [phone's icons](#script-apps) (`bell` when left out
or unknown). It respects the player's silent and do-not-disturb settings.

```lua
exports.tml_phone:SendNotification(source, { title = 'Benny\'s', body = 'Your car is ready to collect.', icon = 'car' })
```

Errors: `'no_phone'` (not carried by anyone online), `'battery_dead'`, `'bad_request'`.

#### `AddContact(target, number, name)` → `contact|nil, err?`

Saves a contact on a phone (a job's number, "Your lawyer"). Returns `{ id, number, name, favourite }`. An open phone
shows it straight away.

```lua
exports.tml_phone:AddContact(source, '555-0100', 'Mechanic Shop')
```

Errors: `'no_phone'`, `'not_allowed'` (the Contacts app is switched off, `Config.CoreApps`), `'contact_name'`,
`'number_invalid'`, `'contact_limit'`, `'contact_exists'`.

#### `GetContacts(target)` → `table[]|nil, err?`

A phone's contacts: `{ { id, number, name, favourite } }`, by name.

### Messages

#### `SendMessage(from, to, body, attachments?)` → `message|nil, err?`

Sends a text as if typed on a phone. `from` is a phone number, a service number (`Config.Services`) or one of your
[custom numbers](#custom-numbers). `attachments` is an optional list of
`{ type = 'location', x, y, z, label? }` or `{ type = 'photo', url }` (the photo must be on an allowed host).

```lua
exports.tml_phone:SendMessage('311', playerNumber, 'Your taxi is on its way.', {
  { type = 'location', x = 215.1, y = -810.2, z = 30.7, label = 'Pickup' },
})
```

### Calls

#### `IsInCall(src)` → `boolean`

On a call, or the phone is ringing.

#### `GetCall(src)` → `table|nil`

`{ id, direction = 'in'|'out', state = 'ringing'|'active', number, display, label, anonymous, started }`

#### `EndCall(src)` → `boolean`

Hangs up the player's current call.

#### `StartCall(src, number, video?)` → `call|nil, err?`

Calls a number from the player's active phone, as if they dialled it: a payphone, a "call this business" button. The
phone opens on the call screen. `video = true` makes it a video call (smartphone). Works with service and custom
numbers too.

```lua
exports.tml_phone:StartCall(source, '311')
```

Errors are the same as dialling on the phone: `'no_phone'`, `'locked'` (the phone opens on its lock screen),
`'phone_blocked'`, `'battery_dead'`, `'airplane'`, `'in_call'`, `'number_invalid'`, `'self'`, `'unavailable'`,
`'busy'`, `'service_unavailable'`.

### Evidence (MDT and police scripts)

These read a phone's private data. They are exports, so only your server scripts can call them; players never can.
Use them from scripts that already check who is asking, such as an MDT searching a seized phone. They return what
the phone itself shows: messages or calls its owner cleared are left out.

#### `GetMessages(number, other?, limit?)` → `table[]|nil, err?`

The phone's messages, newest first, across all conversations, or only the one-to-one conversation with `other`.
`limit` 1-500 (default 50).

```lua
{ id, conversationId, sender, senderDisplay, body, attachments, created, outgoing, isGroup, groupName }
```

#### `GetCallLog(number, limit?)` → `table[]|nil, err?`

`{ id, direction = 'in'|'out', number?, display?, label?, status = 'answered'|'missed'|'declined', started, duration }`,
newest first. `number` is missing for a caller who hid their number. `limit` 1-500 (default 50).

#### `GetPhotos(number, limit?)` → `table[]|nil, err?`

The gallery, newest first: `{ id, url, created }`. `limit` 1-500 (default 50).

#### `AddPhoto(target, url)` → `photo|nil, err?`

Puts an image in a phone's gallery (an evidence photo, a mugshot). The link must be `https` on an allowed host
(`ServerConfig.Uploads.allowedHosts` or `Config.Photos.linkHosts`) and counts toward `Config.Camera.maxPhotos`.
Errors: `'no_phone'`, `'photo_link'`, `'photo_limit'`.

All four return `nil, 'no_phone'` for an unknown number and `nil, 'bad_request'` for a bad `other` or `limit`.

### Custom numbers

A number your script answers: a bank hotline, a job's text line, an NPC.

#### `RegisterCustomNumber(number, def)` → `boolean, err?`

```lua
exports.tml_phone:RegisterCustomNumber('5550100', {
  label = 'Maze Bank',
  -- A player calls it. Return true to pick up: the call goes live without voice; end it with EndCall.
  onCall = function(src, callerNumber, callId)
    return false -- let it ring out
  end,
  -- A player texts it. Reply with SendMessage.
  onMessage = function(src, fromNumber, body, attachments, conversationId)
    exports.tml_phone:SendMessage('5550100', fromNumber, 'Thanks, an advisor will text you back.')
  end,
})
```

At least 3 digits. Errors: `'number_invalid'`, `'bad_request'`, `'number_reserved'` (a service number),
`'number_taken'` (a player's number or another resource's). Numbers are removed when your resource stops.

#### `UnregisterCustomNumber(number)` → `boolean`

Only the resource that registered it can remove it.

### Mail

#### `SendMail(target, mail)` → `id|nil, err?`

`target`: a server id, a phone number or a mail address.

```lua
exports.tml_phone:SendMail(source, {
  sender = 'hr@lsmc.gov',      -- default: noreply@<Config.Mail.domain>
  senderName = 'Pillbox HR',
  subject = 'Interview',
  body = 'Come by at 10:00.\nBring ID.',
  actions = {                  -- up to 4 buttons
    { label = 'Accept', event = 'myjob:server:accept', data = { slot = 10 }, once = true },
    { label = 'Directions', waypoint = { x = 298.6, y = -584.5 } },
  },
})

-- A server-local event: players can't trigger it from their client.
AddEventHandler('myjob:server:accept', function(src, data, mailId) end)
```

`once = true` greys the button out after the first click. A `waypoint` button sets the player's GPS.
Errors: `'no_phone'`, `'bad_request'`.

#### `GetMailAddress(target)` → `string|nil`

#### `DeleteMail(id)` → `boolean`

### Calendar

#### `AddCalendarEvent(target, event)` → `id|nil, err?`

`target`: a server id, a phone number, `{ job = 'police' }` (everyone with that job) or `'all'`.

```lua
exports.tml_phone:AddCalendarEvent({ job = 'police' }, {
  title = 'Briefing',
  starts = os.time() + 3600, -- unix time (real time)
  ends = os.time() + 5400,   -- optional: default 1 hour (or the whole day with allDay)
  allDay = false,
  location = 'Mission Row',
  notes = 'Bring your radio.',
  remind = 15,               -- minutes before; one of Config.Calendar.reminders
})
```

Errors: `'no_phone'`, `'bad_request'`, `'event_title'`, `'event_time'` (more than a year ago, more than five years
ahead, or longer than `Config.Calendar.maxDurationDays`). Players can hide shared events but not delete them.

#### `RemoveCalendarEvent(id)` → `boolean`

#### `GetCalendarEvents(target, from, to)` → `table[]|nil, err?`

Events between two unix times: what a phone sees (server id or number), or those stored for a job or `'all'`.

### Wallet

#### `AddWalletTransaction(target, entry)` → `id|nil`

Adds a line to a phone's Wallet history for a payment your script made (shop, fine, salary). It does **not** move
money: change the balance through your framework as usual.

```lua
exports.tml_phone:AddWalletTransaction(source, { amount = 250, direction = 'out', title = '24/7 Supermarket', note = 'Groceries' })
```

`amount` a positive whole number, `direction` `'in'` or `'out'`.

### Ads

#### `GetAds(category?)` → `table[]`

Live ads, newest first (at most 30): `{ id, category, title, body, price, name, number, display, created, expires }`.

#### `DeleteAd(id)` → `boolean`

### Music

#### `GetCuratedSongs()` → `table[]`

Every "Curated for you" song, from config and staff: `{ id, title, artist?, url }`.

#### `AddCuratedSong(data)` → `song|nil, err?`

`data = { url = 'https://...mp3', title, artist? }`. Errors: `'bad_request'`, `'music_link'`, `'music_title'`,
`'music_limit'` (`Config.Music.maxCurated`).

#### `RemoveCuratedSong(id)` → `boolean, err?`

A staff-added song, by its id (`'cur:<n>'`). Config songs can only be removed from the config.

### Battery

#### `GetBattery(number)` → `number|nil`

Whole percent.

#### `SetBattery(number, level)` → `boolean, err?`

`level` 0-100; under 1 is dead. Use it for chargers, power banks or items. Errors: `'not_found'`, `'bad_request'`.

```lua
exports.tml_phone:SetBattery(exports.tml_phone:GetNumber(source), 100)
```

### Script apps

Add your own smartphone app. It shows up in the App Store (and on new phones when preinstalled); its page is an HTML
file from **your** resource, loaded in a frame inside the phone. See [App SDK](#app-sdk).

#### `RegisterApp(def)` → `boolean, err?`

```lua
exports.tml_phone:RegisterApp({
  id = 'garage',                  -- a-z, 0-9, _ (2-24 characters)
  label = 'Garage',
  description = 'Your vehicles',  -- shown in the App Store
  icon = 'car',                   -- one of the phone's icons (below), or image = 'web/icon.png' (a file in your resource)
  color = '#22C55E',              -- or { '#22C55E', '#15803D' } for a gradient
  ui = 'web/app.html',            -- the page, a file in your resource (list it in your fxmanifest `files`)
  fullscreen = false,             -- true = the page also covers the status bar
  preinstalled = false,
})
```

Errors: `'bad_id'`, `'id_taken'` (another resource's), `'bad_label'`, `'bad_ui'`. Register again (for example on
every start) to update it. An installed app stays on players' phones while your resource is stopped.

Icons: `phone` `message` `user` `users` `settings` `calculator` `search` `star` `clock` `calendar` `bag` `notes`
`mail` `inbox` `at` `flashlight` `alarm` `timer` `stopwatch` `sliders` `palette` `siren` `shield` `medical` `car`
`wrench` `flame` `briefcase` `megaphone` `map` `navigation` `tag` `bank` `fuel` `food` `building` `flag` `bookmark`
`wallet` `camera` `video` `terminal` `key` `globe` `link` `music` `image` `lock` `bell` `speaker` `zap`. For anything
else, use `image`.

#### `UnregisterApp(id)` → `boolean`

#### `HasApp(src, id)` → `boolean`

Is your app installed on the player's active phone?

#### `NotifyApp(src, id, data)` → `boolean`

A notification from your app: `data = { title, body? }`. Shown only when the app is installed and the phone isn't
dead.

## Server events

Server-local events you can listen to with `AddEventHandler`. Players can't trigger them.

```lua
AddEventHandler('tml_phone:messageReceived', function(number, from, body, conversationId) end)
```

| Event | Arguments |
|---|---|
| `tml_phone:phoneCreated` | `number, device, owner` |
| `tml_phone:opened` | `src, number` |
| `tml_phone:closed` | `src, number` |
| `tml_phone:setupComplete` | `src, number, { language, name, ownerName }` (first-boot setup finished) |
| `tml_phone:aboutChanged` | `src, number, { language, name, ownerName }` (Settings > About) |
| `tml_phone:numberChanged` | `oldNumber, newNumber` |
| `tml_phone:factoryReset` | `number` |
| `tml_phone:pinFailed` | `src, number, fails` (a wrong PIN) |
| `tml_phone:numberShared` | `src, number, sentTo` (count of players offered the number) |
| `tml_phone:messageSent` | `from, conversationId, body` |
| `tml_phone:messageReceived` | `number, from, body, conversationId` (once per receiving phone) |
| `tml_phone:groupCreated` | `conversationId, creatorNumber, members` |
| `tml_phone:callStarted` | `callId, callerNumber, calledNumber` |
| `tml_phone:callAnswered` | `callId, callerNumber, answeringNumber` |
| `tml_phone:callEnded` | `callId, callerNumber, calledNumber, status ('answered'\|'missed'\|'declined'), seconds` |
| `tml_phone:serviceMessage` | `serviceNumber, from, body, conversationId, isNewRequest` (`true` when this text opened a new or reopened request) |
| `tml_phone:serviceRequestTaken` | `serviceNumber, requesterNumber, responderSrc, conversationId` |
| `tml_phone:serviceRequestClosed` | `serviceNumber, requesterNumber, responderSrc, conversationId` |
| `tml_phone:mail:sent` | `src, number, { from, to, subject }` |
| `tml_phone:mail:received` | `src, number, { id, from, subject }` |
| `tml_phone:calendar:added` | `id, targetType ('phone'\|'job'\|'all'), target` |
| `tml_phone:calendar:reminder` | `src, number, event` |
| `tml_phone:alarm` | `src, number, alarm` |
| `tml_phone:adPosted` | `src, number, { id, category, title, price }` |
| `tml_phone:adDeleted` | `id` |
| `tml_phone:walletPayment` | `src, targetSrc, fromNumber, toNumber, amount, note` |
| `tml_phone:photoTaken` | `src, number, url` |
| `tml_phone:photoAdded` | `src, number, url` (from a link) |
| `tml_phone:musicCurated` | `'add'\|'remove', song` |
| `tml_phone:darkchatMessage` | `src, number, channelName, handle, body` |
| `tml_phone:appInstalled` | `src, number, appId` |
| `tml_phone:appRemoved` | `src, number, appId` |
| `tml_phone:batteryDead` | `src, number` |

Other `tml_phone:*` events (`tml_phone:server:*`, `tml_phone:client:*` net events) are internal and may change.

## Client exports

| Export | Returns |
|---|---|
| `GetNumber()` | the open phone's number, or `nil` when the phone isn't open |
| `IsOpen()` | `boolean` |
| `IsInCall()` | `boolean` |
| `Open()` | opens the active phone (same as the command) |
| `Close()` | closes it |
| `SendAppMessage(id, data)` | sends `data` to your script app's page while the phone is on screen; `false` when it isn't |
| `SetPhoneBlocked(reason, blocked)` | the client side of [`SetPhoneBlocked`](#setphoneblockedsrc-reason-blocked--boolean-err), for client scripts (a death or ragdoll script). Stored as `client:<reason>`, so it never lifts a server script's block |
| `IsPhoneBlocked()` | `boolean`: blocked by any script, server or client |

## Client events

Local events on the player's own client:

| Event | Arguments |
|---|---|
| `tml_phone:client:opened` | `payload` (`payload.phone.number`, `payload.phone.device`) |
| `tml_phone:client:closed` | `reason` |
| `tml_phone:client:callChanged` | the call, as `GetCall` returns it; `state = 'ended'` when it's over |
| `tml_phone:client:messageReceived` | `{ number, isGroup, groupName, muted, silent, message }` |
| `tml_phone:client:conversationChanged` | `data` |

## App SDK

Your app's page (the `ui` of `RegisterApp`) includes the SDK:

```html
<script src="https://cfx-nui-tml_phone/web/sdk/tml-phone.js"></script>
```

| Function | What it does |
|---|---|
| `TmlPhone.onInfo(fn)` | `fn({ app, number, display, theme = 'dark'\|'light', textSize, colors })`, now and when it changes |
| `TmlPhone.onMessage(fn)` | receives what your Lua sends with the client export `SendAppMessage(id, data)` |
| `TmlPhone.post(name, data)` | calls your own resource's `RegisterNUICallback(name, ...)`; returns a Promise of its JSON reply |
| `TmlPhone.notify(title, body)` | a banner on the phone |
| `TmlPhone.back()` | the phone's Back |
| `TmlPhone.close()` | back to the home screen |

The page gets the phone's colours as CSS variables on `<html>` (`--tml-accent`, `--tml-accent-2`, `--tml-ink`) and
`data-tml-theme="dark|light"`, so it can match the player's theme. While a text field on the page has focus, the
player stops moving; Esc leaves the field, then goes back.

**Your NUI callbacks are requests from a player's client**: check everything on your server as you would for any
event.

## Permissions

```
add_ace group.admin tml_phone.admin allow
```

`tml_phone.admin` is the default for `Config.Pin.adminAce` (`/phonebattery`),
`Config.Ads.moderatorAce` (take down any ad) and `Config.Music.curateAce` (curated songs in game). Each can be set to
a different ACE.
