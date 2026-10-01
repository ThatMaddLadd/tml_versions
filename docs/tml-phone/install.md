1. Put `tml_phone` in your resources folder.
2. `ensure tml_phone` in server.cfg **after** oxmysql, your framework, your inventory, your voice script and ox_lib
   (if you use them).
3. Start the server. The database tables are created and upgraded automatically (`install.sql` has the same tables
   if you would rather run them by hand).
4. Add the phone items for your inventory (below).
5. Optional: set up photo uploads (below) and read through `config/config.lua`.

Players open their phone with `/phone` or F2 (both configurable). The console tells you which framework, inventory,
voice and banking scripts were picked up, and prints a readable warning for any config value it had to reset.

## Phone items

Add the two items for **your** inventory (files in this folder):

| Inventory | File |
|---|---|
| ox_inventory (also Qbox) | `ox_inventory.txt` |
| qb-inventory / ps-inventory | `qb-inventory.txt` |
| qs-inventory | `qs-inventory.txt` |
| codem-inventory | `codem-inventory.txt` |
| core_inventory | `core_inventory.txt` |
| an inventory that reads ESX's items table | `esx.sql` |

Copy the images from `images/` into your inventory's image folder. `tml_pearphone.png` and `tml_classicphone.png`
are only for designs you give their own item in `Config.Designs`.

- Phones must not stack (`stack = false` / `unique = true`): every phone item has its own number.
- ox_inventory: the item **must** contain `server = { export = 'tml_phone.use_item' }` and `consume = 0`, exactly as
  in `ox_inventory.txt`, or using it does nothing.
- No inventory, or ESX's own default inventory: skip this section. The phone runs without items (everyone has one).

## Photo uploads (Camera app)

The phone takes photos itself; it only needs an image host to store them. With [Fivemanage](https://fivemanage.com),
put your API key in server.cfg:

```
set tml_phone_fivemanage_key "your-key"
```

Other hosts: `bridge/uploads/README.md`. Without an image host the Camera app says it isn't set up on this server; everything else works (photos can still
be added from links, see `Config.Photos`).

## Permissions

```
add_ace group.admin tml_phone.admin allow   # /phonebattery, remove anyone's ad or listing, curate Music songs,
                                            # moderate Chirp and Lens, take down news articles
add_ace identifier.license:xxxx tml_phone.news allow   # optional: may write News whatever their job
```

News writers are the jobs in `Config.News.jobs` (default: `reporter`). Chirp and Lens reports can go to a Discord
webhook: `set tml_phone_social_webhook "https://discord.com/api/webhooks/..."` in server.cfg (or
`ServerConfig.Social` in `config/server.lua`).
