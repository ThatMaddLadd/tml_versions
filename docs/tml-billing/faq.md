**"The business account could not be found" when paying.**
The banking bridge couldn't find the shared account named in `Config.Businesses[job].society`. Check the console:
it prints the account and business. On QBCore/Qbox you need Renewed-Banking or qb-banking (or a custom adapter); on
ESX, esx_addonaccount society accounts (`society_mechanic`, written as `mechanic` in the config).

**I can't see the "New invoice" tab options for my job.**
Your job name must be a key in `Config.Businesses`, and your grade at least `minGrade`. Personal invoices show for
everyone while `Config.Personal.enabled` is `true`.

**Nobody shows up to bill.**
Stand within `Config.Billing.sendDistance` metres (5 by default) of the person and press Refresh. The list shows
server ids, not names.

**Can the player decline a fine?**
Not when the business has `allowDecline = false` (the default for police).

**The employee was offline when the invoice was paid. Did they lose their commission?**
No. It's held in the database and paid into their bank the next time they log in.

**Can players be charged twice?**
No. Only one payment request can mark an invoice as paid; any other is refunded on the spot.

**Where do late fees go?**
To the business, with the rest of the payment. Commission and tax are worked out on the original amount.

**How do I bill from another script (speed cameras, impound, shops)?**
Use the `CreateInvoice` export with `issuer = nil` (see `docs/API.md`).

**Can I change the colours?**
Yes: edit `web/theme.css` or set `Config.Theme.vars`.

**Can I add another language?**
Copy `config/locales/en.lua`, translate the values, and set `Config.Locale`.

**My framework or banking script isn't in the list.**
Edit or add an adapter in `bridge/`; those files are open, not escrowed. `bridge/framework/custom.lua` and
`bridge/banking/custom.lua` are commented templates.

**I renamed the resource folder.**
Keep the name `tml_billing`. The exports and events are tied to it.
