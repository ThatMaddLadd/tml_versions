**Where did my players' money go? / Do balances carry over from my old bank?**
Personal accounts are the framework's `bank` money, so they're exactly what they were. Job, gang and shared accounts
from Renewed-Banking can be imported with `tmlbank_import renewed` in the server console (see the install guide).

**A script says "Renewed-Banking is needed" (or checks `GetResourceState('Renewed-Banking')`).**
tml_banking answers Renewed-Banking's exports, but a script that first checks whether the resource *named*
Renewed-Banking is running will still refuse. Change that check to also accept `tml_banking`. For qbx_vehicleshop,
in `config/server.lua` `addSocietyFunds`:

```lua
if GetResourceState('tml_banking'):find('started') then
    return exports.tml_banking:AddMoney(society, amount, 'Vehicle sale', 'Vehicle sale') == true
elseif GetResourceState('Renewed-Banking'):find('started') then
```

Scripts that list Renewed-Banking as a `dependency` in their fxmanifest need that line removed.

**The ATM says I need a card.**
`Config.Cards.requireAtATM` is on. Order a card at a bank (Cards tab), or set it to `false`. Without an inventory that
stores item data, cards are off and ATMs open without one.

**I can't see the Loans page.**
It's in the bank window's sidebar, under "Borrowing", and only at banks (not ATMs) that offer loans. With
`Config.Loans.playerApply = false` it only shows once you have a loan.

**Players were taking loans and paying them straight back to raise their score.**
That's what `Config.Loans.protection` stops: a cooldown between loans, a payment required before paying early, no
`repaid` credit for loans paid off before half their term, no credit from small loans and a daily cap on rises.
Tighten the numbers if you need to.

**I only want loans from my car dealer / property script, not at the bank.**
Set `Config.Loans.playerApply = false` (or `playerApply = false` on single products) and give loans with
`exports.tml_banking:IssueLoan(...)` (see the API). Admins can give them from the panel too.

**How do I open the admin panel?**
`/bankadmin`, or the Admin button in the bank window. You need the ACE `tml_banking.admin`
(`add_ace group.admin tml_banking.admin allow` in server.cfg) or one of `Config.Admin.groups`.

**Discord webhooks don't post.**
Set `enabled = true` in `config/webhooks.lua` and paste a webhook URL (`https://discord.com/api/webhooks/...`) into
`default` or the event's own `url`. A switched-on event with no usable URL prints a warning on start.

**Interest isn't paid on personal accounts.**
The default rate for personal accounts is `0`. Personal accounts also only earn while the owner is online unless
`Config.Interest.personalOffline = true`.

**Can players see each other's money?**
No. Every request is checked on the server against that player's rights on the account, and the window is only sent
what they may see.

**The phone's Bank app doesn't show my accounts.**
TML Phone shows them when tml_banking is running and `Config.Wallet.bank = true` in the phone's config.
`Config.Mobile` in tml_banking decides what the phone may do.

**How do shops take card payments?**
Call `exports.tml_banking:ChargeCard(source, amount, { merchant = 'Shop name', to = 'shopjob' })` from a thread
(see the API). The player picks a card and enters the PIN; the export returns whether it was paid.

**How do I charge rent or insurance every week?**
`exports.tml_banking:RegisterDirectDebit(source, { amount = 1200, intervalHours = 168, label = 'Rent', to = 'realestate' })`.
Listen to `tml_banking:server:debitFailed` (with `final = true`) to act when the player stops paying.

**Where do taxes go?**
Into `Config.Taxes.account` (default `government`). Add that account to `Config.SharedAccounts.extra`
(`{ government = 'Government' }`), or set `account = false` to remove taxed money from the economy.

**The ATM screen shows the normal game screen, or the window opens instead.**
The prop needs an entry in `config/atm_models.lua` and `Config.ATMs.style = 'screen'`. Screens only load within
`Config.ATMs.screen.loadDistance` metres. If another resource also replaces ATM textures (an old ATM script), stop it.

**A label on the ATM screen sits next to the wrong button.**
Adjust `rows` for that layout in `Config.AtmLayouts` (`config/atm_models.lua`), pixels from the top of the screen
texture. `/tmlbank_atmbuttons` (with `Config.Debug = true`) shows where the buttons are on the prop.

**Will the deleted-character check wipe my server if the database is misconfigured?**
No. A character has to be missing on every check for `graceDays`, characters online are never touched, a failed query
stops the check, and a check that finds more than `maxPerSweep` missing at once changes nothing and warns instead.

**A cheque bounced.**
The writer's account couldn't cover it when it was cashed. Set `Config.Cheques.reserveFunds = true` to take the money
when cheques are written, so they never bounce.

**Nobody reviews loan applications.**
With `Config.Banker.autoWhenNoBanker = true` (the default) loans are decided automatically while no banker is on duty.
Bankers need a job from `Config.Banker.jobs` and must be on duty if `requireDuty` is on.

**Can I change the colours?**
Yes: `Config.Theme.vars` in the config, or the CSS variables in `web/theme.css`.
