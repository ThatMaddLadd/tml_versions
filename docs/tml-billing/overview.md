Invoices and fines for FiveM. Businesses bill nearby players with itemised invoices, players pay or decline them in a
clean billing window, and the money is split between the business account, the employee's commission and tax.

Works with Qbox, QBCore, ESX, ox_core, ND_Core, vRP and standalone, and records payments in TML Banking, Renewed-Banking,
okokBanking, qs-banking, fd_banking, tgg-banking, snipe-banking, pefcl, qb-banking or esx_banking. The adapters are in
`bridge/` and aren't escrowed, so you can edit them.

## Features

- **Itemised invoices.** Up to 10 lines (description, quantity, price), one-click presets per business, a note, and
  a live total. The server recalculates every amount from the lines; nothing the client sends about money is trusted.
- **Businesses from config.** Any job can bill: who can send (minimum grade), who manages (sees every invoice of the
  business and can cancel them), commission for the employee, a maximum amount, and whether it can be declined.
- **Fines and invoices.** Set a business to `kind = 'fine'` and players see "Fine" and can't decline it.
- **Money goes where it should.** Payment goes to the business's shared account through your banking script, the
  commission to the employee, and optional tax to a government account. Each payment is recorded in the banking
  script's statement.
- **Nobody loses money.** Commission or personal payments for players who are offline are held and paid out when they
  next log in. Double-clicking Pay can never charge twice.
- **Due dates and late fees.** Invoices go overdue after a set number of days; late fees build up per day, capped.
  Optional auto-pay for overdue invoices.
- **Player to player.** Optional personal invoices with their own maximum.
- **Business stats.** Outstanding, collected in the last 30 days, and overdue count.
- **Nearby people only.** Pick who to bill from the players standing next to you (by server id and distance), checked
  again on the server.
- **Exports and events** so other scripts can send invoices (speed cameras, shops, impound) and react to payments.
- English and Spanish included.

## Requirements

- FiveM server with OneSync
- oxmysql (tables are created automatically)
- One of the supported frameworks, and a banking script with shared accounts for the businesses you bill as (or ESX
  society accounts)

## Install

See install/README.md.

## Use

- `/invoices` (or a key bound in GTA settings) opens the billing window.
- **My bills:** your invoices and fines. Pay from your bank (or cash, if allowed) or decline when allowed.
- **New invoice:** choose who you bill as (your business or Personal), pick a nearby person, add lines or presets,
  and send.
- **Sent:** what you sent, with business stats. Managers see every invoice of their business and can cancel unpaid
  ones.

## Config

Everything is in `config/config.lua`, commented per option. See [the config reference](/docs/tml-billing/config/). Text is in
`config/locales/`. Colours are CSS variables in `web/theme.css` (or `Config.Theme.vars`).

## Known limitations

- With the `framework` banking adapter (no supported banking script), shared business accounts are only available on
  ESX (esx_addonaccount). On QBCore and Qbox use TML Banking, Renewed-Banking or qb-banking, or fill in `bridge/banking/custom.lua`.

