**The radio doesn't open.**
With `Config.Radio.requireItem = true` you need the `tml_radio` item; check it's in your inventory's item list (see
`install/`). On ox_inventory the item needs `server = { export = 'tml_radio.use_item' }`.

**Nobody can hear me.**
The console prints `voice=...` at start. If it says `none`, start pma-voice or SaltyChat **before** `tml_radio`. Talk
with your voice script's radio key (pma-voice: Left Alt), with the radio UI closed.

**I can't join the police frequencies.**
Restricted ranges check your job name, grade and duty. The default config uses `police`, `sheriff`, `ambulance`,
`ems` and `fire`; change `Config.Restricted` to your server's job names, or set `onDutyOnly = false`.

**How do I show who's on a channel?**
Set `Config.Radio.showMembers = true`. The list then shows on restricted frequencies; listeners on public frequencies stay
anonymous unless you also set `Config.Radio.showMembersOnOpen = true`.

**The console shows a lot of pma-voice "radio check removed" lines when I restart the radio.**
That's pma-voice clearing the channel guards when tml_radio stops; they're added again when it starts. Set
`Config.Radio.guardChannels = false` to turn the guards off (the radio still checks every join itself).

**Two animations play when I talk on the radio.**
pma-voice has its own radio animation. Set `setr voice_enableRadioAnim 0` in your voice config.

**Who gets the shoulder mic?**
Jobs in `Config.Animation.shoulderJobs`. Everyone else raises the radio to their mouth.

**I keep the radio but the HUD disappeared.**
The HUD only shows while the radio is on and tuned to a frequency. Being dead or underwater shows "NO SIGNAL".

**Can I connect the panic button to my dispatch script?**
Yes: listen for `tml_radio:panic` on the server (see `docs/API.md`).

**Can I change the colours?**
Yes: edit `web/theme.css` or set `Config.Theme.vars`. Each restricted range also has its own `accent` colour.

**Can I add another language?**
Copy `config/locales/en.lua`, translate the values, and set `Config.Locale`.

**My inventory, framework or voice script isn't in the list.**
Edit or add an adapter in `bridge/`; those files are open, not escrowed. Each category has a commented `custom.lua`.

**I renamed the resource folder.**
Keep the name `tml_radio`. The ox_inventory item export and the events are tied to it.
