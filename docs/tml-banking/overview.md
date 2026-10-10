A full bank for FiveM: personal, savings, business, job and gang accounts, bank cards with PINs, loans with a credit
score, interest, transfers, statements, an admin panel and Discord logs. Every feature can be switched off or tuned
in the config.

Works with Qbox, QBCore, ESX, ox_core, ND_Core, vRP and standalone. The adapters are in `bridge/` and aren't escrowed,
so you can edit them.

## Features

- **Every kind of account.** Each character's personal account is the framework's `bank` money, so every other
  script that pays into `bank` keeps working. Players can open savings accounts (optionally joint) and business
  accounts with members and roles. Job and gang accounts are made for every job and gang, with rights by grade.
- **Banks and ATMs.** Tellers at every bank, and every ATM prop on the map. Each bank can offer fewer services (a
  small branch without loans, say). ATMs do what you allow. Optional opening hours.
- **ATMs with a real screen.** The bank appears on the ATM prop's own screen: step up, the camera moves in, and press
  the machine's side buttons and keypad. Card, PIN, withdraw, pay in, send money and recent activity, all on the
  machine. Or use the bank window at ATMs instead (`Config.ATMs.style`).
- **ATM cash.** ATMs hold limited cash; a cash-transport job refills them and sees the low ones on the map.
- **Bank cards.** A card is an inventory item tied to one account, with a PIN. ATMs can ask for a card and PIN first.
  Wrong PINs lock the card, and the ATM can keep it. Freeze a card or report it lost at a bank or from the phone.
  Optional daily card limits and expiry.
- **Loans.** Products you define (amount range, interest, number of payments, how often), priced by credit score.
  Payments are taken automatically. Missed payments add late fees. After a set number, the loan defaults, and you
  decide what happens: take the balance, go negative, garnish money coming in, block new loans, freeze cards.
- **No credit farming.** A cooldown between loans, payments required before paying early, no credit for loans paid
  off too soon, small loans that never raise the score, and a daily cap on score rises.
- **Loans from scripts only.** Turn off applying at the bank and hand loans out from your own scripts (car dealers,
  property sales, jobs) with one export, or from the admin panel.
- **Credit cards.** A credit line priced by credit score, with statements, a minimum payment, interest and late fees.
  Spend with its card at ATMs and shops; the limit follows the score.
- **Card payments at shops.** One export (`ChargeCard`) puts a payment pad in front of the player: pick a card, enter
  the PIN (or tap for small amounts), done.
- **Standing orders and direct debits.** Players set up repeating transfers; other scripts take rent, insurance or
  subscriptions with one export, and hear about it when a player stops paying.
- **Payroll.** Business owners set a wage per role and a pay day; members are paid automatically.
- **Term deposits.** Lock money away for a set time for a better return.
- **Cheques.** Written at a bank as an item, cashed by whoever holds it with the Cash cheque option at any bank. They can bounce (or have the money held).
- **Payslips.** Jobs pay with a payslip item cashed at the counter (or straight into the bank). Business payroll and
  your framework's own paychecks can use them too.
- **Secured loans.** Loans from scripts can be secured on a car or house; on default your script repossesses it and
  the debt comes down.
- **Overdrafts.** A personal account can go below zero, with a limit by credit score and a daily fee.
- **Two-person approval.** Large withdrawals and transfers from business, job and gang accounts need a second person.
- **Safe deposit boxes.** A stash rented at a bank, paid by the week, locked when the rent isn't paid.
- **Business insights.** Week-by-week money in and out, top payers and wage costs for business, job and gang accounts.
- **Crime hooks.** Robbery scripts close banks and empty vaults and ATMs; criminals fit card skimmers to ATMs and use
  the cloned cards; marked money is refused, charged or flagged at the bank and laundered through businesses.
- **Deleted characters.** Their savings and business money goes to a beneficiary they named; loans, cards and boxes
  are closed.
- **Banker job.** Loan applications reviewed by players with a bank job, with the applicant's full history.
- **Taxes.** Income tax on paychecks, transfer tax and wealth tax, paid into a government account.
- **Fraud flags.** Large movements are flagged; brand-new characters and transfer spam are refused. Flags show in the
  admin panel, TML Monitor and Discord.
- **Notifications** on TML Phone for money arriving, card use and scheduled payments.
- **Credit score.** Changed by loans, and by any other script through exports (capped per call).
- **Interest.** Per account type, paid every set number of hours, with caps and catch-up after downtime.
- **Transfers, fees and limits.** To an account number or a player's server id. Fees per kind of movement, and daily
  limits per character.
- **Admin panel.** `/bankadmin`, or the Admin button in the bank window for staff. Find any player or account, then
  add or remove money, freeze accounts, set credit scores, forgive loans, clear missed payments, delay payments and
  give loans. Every change needs a reason and is logged with the admin's name.
- **Economy dashboard.** How much money exists and where, what enters and leaves the banks each day, who moved the
  most and how loans are doing.
- **Discord webhooks.** Admin actions, large transfers, loans taken, missed, defaulted, repaid and forgiven. Each
  event can post to its own channel. Webhook URLs sit in a server-only file that players never receive.
- **Phone banking.** With TML Phone, the Bank app shows every account, its history, transfers, cards, loans and the
  credit score. `Config.Mobile` decides what a phone may do.
- **Drop-in for other banks.** Answers the exports of Renewed-Banking, qb-banking, qb-management, tgg-banking and
  esx_addonaccount, so job scripts written for them keep working. Imports Renewed-Banking accounts and history.
- **Exports and events** for everything (see [the API docs](/docs/tml-banking/api/)).
- English and Spanish included.

## Requirements

- FiveM server with OneSync
- oxmysql (tables are created automatically)
- One of the supported frameworks. For bank cards, an inventory that stores item data (ox_inventory, qb-inventory,
  ps-inventory or qs-inventory); without one, cards are off and ATMs open without a card.

## Install

See install/README.md.

## Use

- **Banks:** walk up to a teller (target, or the key prompt with no target script). Deposit, withdraw, transfer, see
  statements, open accounts, manage members, order cards and take loans.
- **ATMs:** any ATM prop. Aim at the machine's buttons and click: pick a card, type the PIN on the keypad, then
  withdraw, pay in, send money or see recent activity. Press Esc to leave.
- **Loans:** the Loans page in the bank window's sidebar shows your loans and what you can borrow.
- **Admins:** `/bankadmin`, or the Admin button in the bank window.

## Config

Everything is in `config/config.lua`, commented per option. See [the config reference](/docs/tml-banking/config/). Discord webhooks are
in `config/webhooks.lua` (server only). Text is in `config/locales/`. Colours are CSS variables in `web/theme.css` (or
`Config.Theme.vars`).

## Known limitations

- tgg-banking can't be imported automatically yet (its database layout isn't published).
- The nd_core and vrp framework adapters, and some inventory adapters, are written from those projects' docs and
  haven't all been checked on a live server. The adapter files are open, so they can be adjusted if your setup differs.
- Personal-account interest for offline characters (`Config.Interest.personalOffline`) reads each offline character
  from the database once per period.

Update announcements are posted in the Discord.
