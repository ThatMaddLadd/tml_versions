1. Put `tml_banking` in your resources folder.
2. `ensure tml_banking` in server.cfg **after** oxmysql, your framework, your inventory and your target script.
3. Remove or stop any other banking resource (Renewed-Banking, qb-banking, okokBanking, ...). tml_banking answers
   the exports of Renewed-Banking, qb-banking, qb-management, tgg-banking and esx_addonaccount itself
   (`Config.Compat`), so job scripts written for those keep working.
4. Start the server. The database tables are created automatically (`install/install.sql` has the same statements
   if you would rather run them by hand).
5. Add the bank card item (below), then look through `config/config.lua`.

## The bank card item

Cards need an inventory that stores item data: ox_inventory, qb-inventory, ps-inventory or qs-inventory. With any
other inventory (or none), cards are switched off and ATMs open without one.

Add the item for **your** inventory (files in `install/items/`):

- ox_inventory (also Qbox): `ox_inventory.txt` into `ox_inventory/data/items.lua`
- qb-inventory / ps-inventory: `qb-inventory.txt` into `qb-core/shared/items.lua`
- qs-inventory: `qs-inventory.txt` into `qs-inventory/shared/items.lua`
- ESX (items table): run `esx.sql`
- Image: copy `tml_bank_card.png` into your inventory's image folder (ox_inventory: `web/images/`,
  qb-inventory: `html/images/`).

If you rename the item, change `Config.Cards.item` to match.

## The cheque item

Cheques (`Config.Cheques`) are an item too, `tml_cheque`. The same files in `install/items/` include it; copy
`tml_cheque.png` next to the card image. Like cards, cheques need an inventory that stores item data. Players
cash them with the **Cash cheque** option at a bank (`Config.Cheques.cashAt`).

## Payslips, skimmers and cloned cards

The same files in `install/items/` define `tml_payslip` (`Config.Payslips`), and `tml_skimmer` and `tml_cloned_card`
(`Config.Skimming`, off by default). Copy their images too. Inventories read their item lists when they start, so
restart the server after adding items; tml_banking warns in the console about any item the inventory doesn't know.

## ATM screens

With `Config.ATMs.style = 'screen'` (the default) the bank is drawn onto the ATM props' own screens. The four standard
ATM props are set up in `config/atm_models.lua`; an ATM prop you add to `Config.ATMs.models` without an entry there
opens the bank window instead.

## Moving over from another banking script

Personal balances are your framework's `bank` money, so they carry over by themselves.

**From Renewed-Banking**, the importer copies the rest. Leave its tables in the database (`bank_accounts_new`,
`player_transactions`), stop Renewed-Banking, start tml_banking, then in the **server console** (not in game):

```
tmlbank_import renewed dry    -- shows what it would do, changes nothing
tmlbank_import renewed        -- does it
```

It brings over job and gang account balances, the shared accounts players made (as joint savings accounts owned by
their creator, with the same members), frozen flags, and the last 200 history lines per account. Accounts that
already hold money here are skipped unless you add `overwrite`. It runs once; add `force` to run it again (the
imported history is replaced, not doubled).

**From tgg-banking**, its database layout isn't published, so it can't be imported automatically yet.
`tmlbank_import tgg` prints the tables it finds; send that output to support.
