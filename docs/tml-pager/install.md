1. Put `tml_pager` in your resources folder.
2. `ensure tml_pager` in server.cfg **after** oxmysql, your framework, your inventory and ox_lib (if you use it).
3. Start the server. The database tables are created automatically (`install/install.sql` has the same statements if you would rather run them by hand).
4. Edit `config/config.lua` if needed (dispatch jobs, departments, limits, keybind).

Players can now use `/pager` or F10. Nothing else is required.

## Optional: the pager item

Add the item (and set `Config.Pager.requireItem = true` if you want it to be mandatory) for **your** inventory (files in this folder):

- ox_inventory: `ox_inventory.txt`
- qb-inventory / ps-inventory: `qb-inventory.txt`
- qs-inventory: `qs-inventory.txt`
- codem-inventory / core_inventory: use the qb-style format or your inventory's own, with the name `tml_pager`
- ESX (items table): `esx.sql`
- Item image: copy your own 128x128 `tml_pager.png` into your inventory's image folder.

Using the item opens the pager even when `requireItem` is false. With `requireItem = true` you also need it to send and receive messages and alerts.

ox_inventory: the item definition **must** contain `server = { export = 'tml_pager.use_item' }` and `consume = 0`, exactly as in `ox_inventory.txt`, or using it does nothing.

## Optional: dispatch permissions

```
add_ace group.admin tml_pager.dispatch allow          # may send department alerts
add_ace group.police tml.job.police allow             # standalone framework only: gives the "police" job
```
