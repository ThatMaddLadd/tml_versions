1. Put `tml_radio` in your resources folder. Keep the folder name `tml_radio`.
2. Add `ensure tml_radio` to `server.cfg` **after** your framework, your inventory, your voice script (pma-voice or
   saltychat) and ox_lib (if you use it).
3. Add the radio item to your inventory (below), and copy `images/tml_radio.png` into your inventory's image folder.
4. Restart the server. No database is needed.
5. Check `config/config.lua`: the restricted frequency ranges in `Config.Restricted` use the job names `police`,
   `sheriff`, `ambulance`, `ems` and `fire`. Change them to the job names on your server.

With pma-voice, set `setr voice_enableRadioAnim 0` in your voice config: the radio plays its own talk animations
(shoulder mic for emergency jobs, radio to mouth for everyone else), and pma-voice's would play on top of them.

Players open the radio with `/radio`, by using the item, or with a key they bind in
GTA settings > Key Bindings > FiveM ("Radio: open", "Radio: on / off", "Radio: panic").

## The radio item

Use the snippet for **your** inventory (files in this folder):

| Inventory | File |
|---|---|
| ox_inventory | `ox_inventory.txt` |
| qb-inventory / ps-inventory | `qb-inventory.txt` |
| qs-inventory | `qs-inventory.txt` |
| codem-inventory / core_inventory | use the qb-style entry from `qb-inventory.txt` in your inventory's own item format |
| ESX (items table) | `esx.sql` |

ox_inventory: the item **must** contain `server = { export = 'tml_radio.use_item' }` and `consume = 0`, exactly as in
`ox_inventory.txt`, or using it does nothing.

Don't want an item? Set `Config.Radio.requireItem = false` and everyone can use the radio with the command or key.

## Permissions (optional)

Restricted frequencies also accept an ACE permission, set per range in `Config.Restricted`:

```
add_ace group.admin tml_radio.police allow       # may join the police range
add_ace group.admin tml_radio.emergency allow    # may join the shared emergency range
add_ace group.police tml.job.police allow        # standalone framework only: gives the "police" job
```
