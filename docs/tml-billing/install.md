1. Put `tml_billing` in your resources folder. Keep the folder name `tml_billing`.
2. Add `ensure tml_billing` to `server.cfg` **after** oxmysql, your framework, your banking script and ox_lib (if you
   use it).
3. Start the server. The database tables are created automatically (`install.sql` has the same statements if you
   would rather run them by hand).
4. Edit `Config.Businesses` in `config/config.lua`: the keys are job names (`police`, `ambulance`, `mechanic` by
   default) and `society` is the shared account each business is paid into. Use the job and account names from your
   server.

Players open the billing window with `/invoices`, or bind a key in GTA settings > Key Bindings > FiveM
("Billing: open invoices").

No items are needed.

## Checking the business accounts

When a business invoice is paid, the money goes into that business's shared account through the banking bridge. If
the account can't be found, the payment is refused (the player keeps their money) and the console prints which
account is missing. The console also prints `banking=...` at start, showing which banking script was detected.
