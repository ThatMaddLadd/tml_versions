Everything is in `config/config.lua`, with a comment on every option. Discord webhooks are in `config/webhooks.lua`,
which only the server loads. The config is checked on start: a wrong value prints a warning in the server console
and its default is used instead.

Fees everywhere use the same shape: `{ flat = number, percent = number, min = number, max = number }` (`max = 0` means
no cap). A fee is charged on top of the amount, from the account the money leaves.

## General

| Option | Default | |
|---|---|---|
| `Config.Locale` | `'en'` | File in `config/locales/` (`en`, `es`) |
| `Config.Debug` | `false` | Extra console output |
| `Config.CheckForUpdates` | `true` | Console notice when a newer version exists |
| `Config.BankName` | `'Maze Bank'` | Shown in the window, statements and notifications |
| `Config.Currency` | `'$'` | Shown before amounts |
| `Config.Framework` / `Inventory` / `Notify` / `Target` | `'auto'` | Force an adapter instead of detecting it (names listed in the config) |
| `Config.Standalone` | | Standalone mode only: starting cash and bank, and job ACEs |

## Banks and ATMs

`Config.Interaction`: `key` (walk-up prompt key with no target script, `'E'`), `chequeKey` (cash cheques at a bank
with the walk-up prompt, `'G'`) and `distance` (metres, `2.0`).

`Config.Banks` is a list of `{ label, coords }`. Optional per bank:

- `services = { deposit, withdraw, transfer, statements, manage, cards, loans, payments, savings, cheques, creditCards, boxes }`:
  switch off what this bank doesn't offer. Anything left out stays on.
- `blip = false`: no map blip for this bank.

`Config.BankBlips`: `enabled`, `sprite`, `colour`, `scale`.

`Config.BankHours`: `enabled` (`false`), `open` (`8`), `close` (`20`; lower than `open` for overnight), `clock`
(`'game'` = in-game hour, `'server'` = the server machine's clock). ATMs are always open.

`Config.ATMs`:

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `models` | four ATM props | Every prop that works as an ATM |
| `services` | withdraw and statements only | `deposit`, `withdraw`, `transfer`, `statements` |
| `fee` | no fee | Withdrawal fee at ATMs, used instead of `Config.Fees.withdraw` |
| `maxWithdraw` | `5000` | Most cash per ATM withdrawal (`0` = no cap) |
| `animation` | `true` | ATM animation while it's open |
| `style` | `'screen'` | `'screen'` = the bank appears on the ATM's own screen: the player steps up, the camera moves in, and they press the machine's side buttons and keypad by aiming and clicking. `'window'` = the bank window opens |
| `screen.camera` | `true` | Move the camera in to the screen |
| `screen.hideHud` | `true` | Hide the radar and HUD while at the screen |
| `screen.loadDistance` | `25.0` | ATMs nearer than this show the bank's idle screen |
| `screen.quickAmounts` | `50, 100, 200, 500, 1000` | Cash amounts on the side buttons when withdrawing |

**ATM screens** (`config/atm_models.lua`): for each ATM prop, which texture is its screen, where the player stands and
where each physical button is. `Config.AtmLayouts` says where the labels beside the side buttons go on the screen
texture; nudge `rows` if a label sits off its button. A prop in `Config.ATMs.models` without an entry there opens the
bank window instead. With `Config.Debug = true`, `/tmlbank_atmbuttons` next to an ATM draws its buttons for 10 seconds.
`shortLabels = true` on a layout uses the short button texts (`atm_*_short` in the locales) where the labels are
narrow; the portable and Fleeca layouts have it on. The screens draw only this resource's own page onto the prop's
texture; nothing from the game files ships with it. The Fleeca ATM's texture also holds its front, trim, side label
and sign, so that layout draws those too (`panels`), with the name and colours in its `brand` table: change them to
rebrand the machine.

## Bank cards (`Config.Cards`)

| Option | Default | |
|---|---|---|
| `enabled` | `true` | Needs an inventory that stores item data (ox, qb, ps, qs) |
| `item` | `'tml_bank_card'` | Item name |
| `requireAtATM` | `true` | ATMs ask for a card and PIN first |
| `atmScope` | `'card'` | `'card'`: only the card's account at the ATM. `'all'`: every account the user can use |
| `ownerOnly` | `false` | Only the cardholder can use it (otherwise anyone holding it who knows the PIN) |
| `kinds` | personal, savings, business, credit | Account types that can have cards |
| `issueFee` / `replaceFee` | `50` / `150` | Price of a new card / a replacement for a lost one |
| `maxPerAccount` | `1` | Working cards one person may hold for one account |
| `pinMin` / `pinMax` | `4` / `6` | PIN length |
| `maxAttempts` | `3` | Wrong PINs before the card locks |
| `lockMinutes` | `30` | How long it stays locked (`0` = until the PIN is changed at a bank) |
| `retainOnLock` | `false` | The ATM keeps (cancels) a card when it locks |
| `dailyLimit` | `0` | Cash one card can withdraw per day (`0` = only `Config.Limits`) |
| `expiryDays` | `0` | Days until a card stops working (`0` = never) |
| `numberPrefix` | `'4921'` | First digits of card numbers |

## Accounts

`Config.Accounts`: `numberFormat` (`'########'`, `#` = digit), `numberPrefix`, `labelLength` (`32`), `allowRename`
(`true`).

`Config.ExtraAccounts` (savings): `enabled`, `maxPerPlayer` (`2`), `openFee` (`0`), `allowJoint` (`true`), `maxMembers`
(`4`, owner included), `closeNeedsZero` (`true`; otherwise what's left goes to the personal account).

`Config.SharedAccounts` (job and gang):

| Option | Default | |
|---|---|---|
| `jobs` / `gangs` | `true` | Make an account for every job / gang the framework lists |
| `exclude` | `{ 'unemployed' }` | Names that never get one |
| `extra` | `{}` | Accounts your framework doesn't list: `{ government = 'Government' }` |
| `requireDuty` | `false` | Job members must be on duty |
| `rights` | view, withdraw, transfer: `'boss'`; deposit: `0` | Per right: lowest grade, `'boss'` or `false` (nobody) |
| `overrides` | `{}` | Per account: `{ police = { withdraw = 3 } }` |

`Config.BusinessAccounts`: `enabled`, `maxPerPlayer` (`1`), `openFee` (`2500`), `maxMembers` (`10`), and `roles`: each
role's rights (`view`, `deposit`, `withdraw`, `transfer`, `members`). The owner can always do everything.

## Money movement

`Config.Transfers`: `enabled`, `toPlayerId` (send to a server id, `true`), `toOffline` (reach offline characters by
account number, `true`), `reasonLength` (`64`), `confirmAbove` (`10000`; `0` = always confirm).

`Config.Fees`: `account` (a shared account that receives fees, e.g. `'government'`, or `false`), then `deposit`,
`withdraw`, `transfer` (to someone else) and `internal` (between accounts the same person can use).

`Config.Limits`: `minAmount` (`1`), `maxAmount` (`10000000`), and daily per character: `depositDaily`, `withdrawDaily`,
`transferDaily` (`0` = no limit). Daily totals reset at midnight server time.

`Config.Statements`: `pageSize` (`25`), `keepDays` (`90`; `0` = keep forever).

## Interest (`Config.Interest`)

| Option | Default | |
|---|---|---|
| `enabled` | `true` | Set `false` to switch interest off entirely |
| `intervalHours` | `24` | Real hours between payouts |
| `catchUp` | `3` | Most missed periods paid after downtime |
| `rates` | savings `0.5`, others `0` | % of the balance per period, per account type |
| `minBalance` | `1000` | Accounts below this earn nothing |
| `maxBalance` | `0` | Only this much of a balance earns (`0` = no cap) |
| `maxPayout` | `0` | Most one account earns per period (`0` = no cap) |
| `personalOffline` | `false` | Pay personal-account interest to offline characters too |
| `overdraftRate` | `0` | % charged per period on negative balances (after loan defaults) |

## Loans (`Config.Loans`)

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `playerApply` | `true` | Players may apply at a bank. `false` = loans only come from scripts (`IssueLoan`) and admins; players still see and repay them |
| `maxActive` | `2` | Open loans per character |
| `products` | quick, personal, business | See below |
| `creditPricing` | on | `discount` (`30`% off the rate at the best score), `surcharge` (`30`% on top at the lowest allowed score), `minAmountFactor` (`0.5` of `max` at the lowest score) |
| `graceHours` | `6` | Hours after a payment is due before it counts as missed |
| `retryMinutes` | `30` | How often a failed payment is retried during the grace period |
| `lateFee` | `50` + `5`% | Added for each missed payment (percent of the payment) |
| `missedToDefault` | `3` | Missed payments in a row before the loan defaults |
| `allowEarlyRepay` | `true` | Players may pay extra or pay off early |
| `earlyWaiveInterest` | `50` | % of the unpaid interest waived when paying off in full early |
| `notify` | `true` | Tell online borrowers about payments, misses and defaults |

Each product: `name` (unique id), `label`, `min`, `max`, `rate` (total % interest over the whole loan),
`installments`, `intervalHours`, `minScore`, `kinds` (`'personal'`, `'business'`), `originationFee` (% kept from the
payout). Optional: `jobs = { 'police' }`, `minAccountDays` (personal account age), `playerApply = false` (only
scripts and admins can give this one), `review = true` (a banker reviews applications, see `Config.Banker`).

`Config.Loans.protection` (stops credit farming):

| Option | Default | |
|---|---|---|
| `cooldownHours` | `12` | Hours after a loan closes before that character may take another |
| `minPaymentsBeforeEarly` | `1` | Scheduled payments made before paying extra or paying off |
| `creditMinTerm` | `50` | % of the full term that must pass for paying off to earn `repaid` credit |
| `minPrincipalForCredit` | `1000` | Smaller loans never raise the score |
| `maxCreditGainDaily` | `25` | Most loans can raise a score in 24 hours (`0` = no cap). Drops are never capped |

`Config.Loans.collateral` (secured loans from scripts, see `IssueLoan` in API.md): `writeOffOnSeize`
(`false`: repossessing writes off the whole debt instead of just the collateral's value), `seizeActive` (`false`: scripts
may also seize collateral of loans that haven't defaulted).

`Config.Loans.onDefault` (applied in this order): `takeBalance` (take what's in the loan's account, `true`),
`negative` (take the rest too, below zero, `false`), `garnish` (take `garnishPercent`% of money arriving until it's
paid, `true` / `50`), `blockNewLoans` (`true`), `freezeCards` (`false`). With `garnish = false`, whatever is still owed
after the first two is written off.

## Credit score (`Config.Credit`)

`enabled`, `min` (`300`), `max` (`850`), `start` (`600`), `externalMaxChange` (most one export call from another
script may change it, `50`; `0` = no cap), `showInBank` (`true`), `historySize` (`25`), and `changes`: how loans move
the score (`paidOnTime` `4`, `paidLate` `-5`, `missed` `-25`, `repaid` `15`, `repaidEarly` `10`, `defaulted` `-120`,
`debtCleared` `30`).

## Credit cards (`Config.CreditCards`)

A credit card is an account of type `credit` whose balance may go below zero, down to its limit. Players open one at a
bank (Credit card page), order a card for it in its Cards tab, and pay it back by moving money into it.

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `limits` | 600 → 2,500 … 800 → 100,000 | `{ score, limit }` rows: the highest row the credit score reaches. Below the first row = no card |
| `openFee` | `0` | Charged from the personal account |
| `statementDays` | `7` | Days in a billing cycle |
| `graceDays` | `3` | Days after a statement to pay at least the minimum |
| `minPayment` | `10`% / `100` | The larger of the two, or the whole balance if less |
| `interest` | `3` | % per cycle on what's left of a statement after it's due |
| `lateFee` | `100` | When less than the minimum was paid |
| `cashAdvanceFee` | `10` + `5`% | Fee for taking cash from a credit card |
| `allowTransfers` | `false` | Credit cards can send money to other people |
| `followScore` | `true` | The limit moves with the credit score at every statement |
| `credit` | `5` / `1` / `-30` | Score change per statement: paid in full / paid the minimum / missed |

## Standing orders and direct debits (`Config.Payments`)

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `standingOrders` | `true` | Players set up repeating transfers at a bank (Payments page) |
| `directDebits` | `true` | Scripts register repeating payments (`RegisterDirectDebit`) |
| `playerCancelDebits` | `true` | Players may cancel direct debits themselves |
| `maxPerPlayer` | `10` | Standing orders per character |
| `minIntervalHours` | `24` | Shortest time between standing order payments |
| `retryHours` | `6` | A failed payment is retried after this long |
| `maxFails` | `3` | Failed payments in a row before it's stopped (the player is told) |

## Payroll (`Config.Payroll`)

Business account owners set a wage per role and a pay day (Payroll tab). On pay day every member with a wage is paid
into their personal account. If the account can't cover everyone, nobody is paid, the owner is told, and it's tried
again after `Config.Payments.retryHours`.

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `intervals` | `{ 24, 72, 168 }` | Hours between pay days owners can choose from |
| `maxWage` | `50000` | Largest wage per member per pay day |

## Term deposits (`Config.Terms`)

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `products` | 7 d 1.5%, 14 d 3.5%, 30 d 8% | `{ days, rate }`: total % paid on top at the end |
| `minAmount` / `maxAmount` | `1000` / `1000000` | |
| `maxActive` | `3` | Running term deposits per character |
| `allowEarly` | `true` | May be broken early: no interest, minus the penalty |
| `earlyPenalty` | `2` | % of the amount kept when broken early |

## Cheques (`Config.Cheques`)

Written in the bank window as an item (`tml_cheque`, see the install guide), cashed at any bank by whoever holds it.
By default every bank gets a **Cash cheque** target option (or the `chequeKey` with the walk-up prompt) that pays
every cheque the player carries straight into their personal account.

| Option | Default | |
|---|---|---|
| `enabled` | `true` | Needs an inventory that stores item data |
| `item` | `'tml_cheque'` | |
| `cashAt` | `'target'` | `'target'` = the Cash cheque option at banks, into the personal account; `'window'` = the bank window's Cheques page, picking the account; `'both'` |
| `reserveFunds` | `false` | Take the money when written and hold it, so cheques never bounce |
| `maxAmount` | `1000000` | |
| `expiryDays` | `30` | Can't be cashed after this (`0` = never). Held money goes back |
| `fee` | `0` | Per cheque written |
| `bounceFee` / `bounceCredit` | `100` / `-15` | Charged to / score change for the writer when a cheque bounces |

## Payslips (`Config.Payslips`)

Jobs and scripts pay players with `exports.tml_banking:IssuePayslip` (API.md).

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `item` | `'tml_payslip'` | See the install guide |
| `mode` | `'item'` | `'item'` = a payslip item, cashed at a bank like a cheque (the **Cash cheques and payslips** option). `'direct'` = paid straight into the bank with a payslip line on the statement. Offline players are always paid directly |
| `expiryDays` | `14` | A payslip not cashed by then goes back to whoever paid it (`0` = never) |
| `payroll` | `false` | Business payroll (`Config.Payroll`) pays with payslips too |
| `convertPaychecks` | `false` | Your framework's own paychecks (money added to `bank` with one of `reasons`) become payslip items instead |
| `reasons` | `paycheck`, `salary` | Words in the framework's money reason that mark a paycheck |

## Card payments (`Config.CardPayments`)

Scripts charge a player's bank or credit card with `exports.tml_banking:ChargeCard` (see the API). The player picks a
card they carry on a payment pad and enters its PIN.

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `contactlessLimit` | `200` | Payments up to this need no PIN (`0` = always ask) |
| `timeoutSeconds` | `60` | How long the pad waits |
| `merchantFee` | none | Kept from what the merchant account receives |

`Config.Cards.dailyLimit` covers card payments and ATM withdrawals together.

## Banker job (`Config.Banker`)

| Option | Default | |
|---|---|---|
| `enabled` | `false` | |
| `jobs` | `{ banker = 0 }` | Job name = lowest grade that reviews applications |
| `requireDuty` | `true` | |
| `review` | `'products'` | `'all'`: every loan needs a banker. `'products'`: products with `review = true` |
| `autoWhenNoBanker` | `true` | With no banker on duty, loans are decided automatically |
| `expireHours` | `48` | Undecided applications are dropped |
| `commission` | `0` | % of each approved loan paid to the banker (from the bank) |

Bankers see an **Applications** page at a bank with each applicant's credit score, past and open loans, debts and
how long they've banked here.

## Taxes (`Config.Taxes`)

| Option | Default | |
|---|---|---|
| `enabled` | `false` | |
| `account` | `'government'` | Shared account taxes go to (add it to `Config.SharedAccounts.extra`). `false` = removed from the economy |
| `income.percent` / `income.reasons` | `0` / paycheck, salary | % of money other scripts pay into a player's bank with one of these words in the reason |
| `transfer.percent` / `transfer.min` | `0` / `0` | On transfers to other people, paid by the sender on top |
| `wealth` | off | `percent` of the part of a balance above `above`, every `intervalHours`, for the account `kinds` listed |

## Fraud flags (`Config.Fraud`)

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `flagAbove` | `250000` | Transfers, withdrawals and card payments at or above this are flagged (still allowed) |
| `newCharacterHours` / `newCharacterMax` | `24` / `25000` | New characters can't send more than this per transfer (refused and flagged) |
| `maxTransfersPerHour` | `20` | More are refused and flagged |

Flags appear in the admin panel (Flags), in TML Monitor's logs and in the `fraud` Discord webhook.

## Overdrafts (`Config.Overdraft`)

| Option | Default | |
|---|---|---|
| `enabled` | `false` | |
| `optIn` | `true` | Players switch it on in the personal account's **Options** tab. `false` = everyone who qualifies has it |
| `limits` | 500 at 500 … 10000 at 800 | `{ score, limit }` rows: the highest row the credit score reaches. Below the first = none |
| `limit` | `1000` | The limit for everyone when credit scores are off |
| `dailyFee` | `25` + `1`% | Charged per full day overdrawn (percent of the amount overdrawn) |
| `dailyCredit` | `-2` | Credit score change per day overdrawn |
| `maxDays` | `30` | Overdrawn this many days in a row switches it off; the balance stays, but can't go lower |

The overdraft covers payments through the bank (withdrawals, transfers, card payments, standing orders, direct
debits). Other scripts that take `bank` money straight from your framework don't use it.

## Two-person approval (`Config.Approvals`)

| Option | Default | |
|---|---|---|
| `enabled` | `false` | |
| `above` | `50000` | Withdrawals and transfers at or above this need a second person |
| `kinds` | business, job, gang | Account types it applies to |
| `expireHours` | `24` | A request nobody decided is dropped |
| `collectHours` | `24` | An approved withdrawal must be collected (the same amount, at a bank or ATM) within this |

The request shows in the account's **Approvals** tab for everyone else with the same right on it; they're notified
too. An approved transfer is sent straight away.

## Safe deposit boxes (`Config.SafeBoxes`)

Needs ox_inventory, qb-inventory, ps-inventory or qs-inventory (stashes). The **Safe deposit box** page in a bank's
sidebar rents, opens, pays for and gives up boxes.

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `sizes` | small, large | Each: `name` (unique), `label`, `slots`, `weight` (grams), `rent` per period |
| `rentDays` | `7` | Days one rent payment covers; taken from the personal account |
| `maxPerPlayer` | `1` | |
| `graceDays` | `3` | Unpaid rent locks the box; after this many days locked it's closed |
| `clearOnClose` | `true` | A closed box is emptied (`false` = contents stay for an admin) |
| `anyBank` | `false` | Open the box at any bank, not only where it was rented |

## ATM cash (`Config.AtmCash`)

| Option | Default | |
|---|---|---|
| `enabled` | `false` | |
| `startCash` | `25000` | Cash in an ATM the first time it's used |
| `capacity` | `50000` | Most an ATM holds; deposits add to it |
| `lowAt` | `5000` | Below this an ATM can be refilled |
| `restockPerHour` | `0` | Cash added to every ATM each hour by itself (`0` = only refills) |
| `refill` | | `enabled`, `jobs` (`gruppe6`), `requireDuty`, `item` (used up per refill, `false` = none), `pay` (`250`), `payTo` (`'player'` or `'job'`), `blips` (refill workers see low ATMs) |

Refill workers get a **Refill ATM** target option on low ATMs. Your own job script can use `RefillAtm` instead.

## Robbery hooks (`Config.Robbery`)

`enabled` (`true`), `vaultAccount` (shared account the vault money comes out of, `false` = your robbery script pays out
itself), `closeOnRob` (`true`), `closeMinutes` (`30`). The exports are in API.md.

## Card skimming (`Config.Skimming`)

| Option | Default | |
|---|---|---|
| `enabled` | `false` | Needs bank cards |
| `item` / `cloneItem` | `tml_skimmer` / `tml_cloned_card` | See the install guide |
| `fitSeconds` | `6` | How long fitting or taking it back takes |
| `lifetimeMinutes` | `60` | A skimmer stops copying after this long |
| `maxCaptures` | `5` | Cards one skimmer copies |
| `capturePin` | `true` | The clone's description shows the PIN |
| `returnItem` | `true` | Taking the skimmer back returns the item |
| `flagUse` | `true` | Every use of a clone raises a fraud flag |
| `inspectJobs` | `police` | Jobs with a **Check for a skimmer** option on ATMs |
| `alertChance` | `25` | % chance fitting one fires `skimmerInstalled` with `alert = true` for your dispatch script |

The player with a skimmer item gets a **Fit skimmer** option on ATMs, and later **Take skimmer back** on the same
one. A clone works like the real card (same number) at ATMs and payment pads until its owner reports the card lost.

## Marked money (`Config.DirtyMoney`)

| Option | Default | |
|---|---|---|
| `enabled` | `false` | |
| `sources` | `markedbills` (worth in its data), `black_money` item | What counts. `{ item, worth? }` (`worth` = the field in the item's data holding its value; without it each counts 1) or `{ account = 'black_money' }` (an ESX money account) |
| `bank` | `'refuse'` | Paying it in at a bank: `'refuse'`, `'fee'` (accepted minus `fee`%) or `'flag'` (accepted in full and flagged for staff) |
| `fee` | `30` | % kept with `'fee'` |
| `flagAlways` | `true` | Flag `'fee'` deposits too |
| `laundering` | on | `rate` (`70`% arrives clean), `dailyMax` (`50000` clean per business account per day), `approval` (`true`: an admin allows each business account in the admin panel) |

Players carrying marked money see it above the account at a bank, with **Pay it in** and (on a business account that
may launder) **Put through the business**.

## Business insights (`Config.Insights`)

`enabled` (`true`), `weeks` (`8`). Business, job and gang accounts get an **Insights** tab: money in and out week by
week, top payers and wages over 30 days. It reads the statement, so it only covers `Config.Statements.keepDays`.

## Character deletion (`Config.Deletion`)

| Option | Default | |
|---|---|---|
| `enabled` | `false` | |
| `money` | `'beneficiary'` | Where their savings, business and term deposit money goes: `'beneficiary'` (falls back to `account`), `'account'` or `'remove'` |
| `account` | `'government'` | Shared account used for `'account'` and when there's no beneficiary (`false` = removed) |
| `beneficiary` | `true` | Players name a beneficiary account in the personal account's **Options** tab |
| `businesses` | `'manager'` | Business accounts they own: handed to a member with `managerRole`, or `'close'` |
| `managerRole` | `'manager'` | |
| `sweepHours` | `24` | How often to check for characters your framework no longer has (`0` = never) |
| `graceDays` | `3` | A character must be missing on every check for this long first |
| `maxPerSweep` | `25` | A check that finds more missing characters than this changes nothing and warns instead |

Loans are written off, cards and standing orders cancelled, credit cards and boxes closed, memberships and the score
removed. The personal balance is the framework's own money and goes with the character. Besides the regular check,
call `exports.tml_banking:CharacterDeleted(identifier)` from your character-deletion code, or run
`tmlbank_deletechar <identifier>` in the server console. The regular check needs a framework adapter with
`CharactersExist` (Qbox, QBCore, ESX, ox_core, ND_Core).

## Notifications (`Config.Notifications`)

Sent to TML Phone when it's running (otherwise an on-screen notification).

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `phone` | `true` | Use the phone when TML Phone is running |
| `incoming` / `minIncoming` | `true` / `1` | Money arriving from someone else |
| `cardUse` | `true` | A card used at an ATM or a shop |
| `payments` | `true` | Failed standing orders and direct debits, payroll, term deposits, credit card statements |

## Mobile banking (`Config.Mobile`)

What a phone app (TML Phone's Bank app) may do. Rights, fees, limits and frozen accounts apply as at a bank.

| Option | Default | |
|---|---|---|
| `enabled` | `true` | `false` = phones show only their own payments |
| `statements` | `true` | See account history |
| `transfers` | `true` | Send money |
| `cards` | `true` | Freeze, unfreeze and report cards lost |
| `loans` | `true` | See open loans |
| `loanPayments` | `true` | Pay extra or pay off (loan rules apply) |
| `credit` | `true` | Show the credit score |

## Admin panel (`Config.Admin`)

| Option | Default | |
|---|---|---|
| `enabled` | `true` | |
| `command` | `'bankadmin'` | Chat command (`''` = none; trigger the `tml_banking:openAdmin` client event instead) |
| `ace` | `'tml_banking.admin'` | ACE that grants access: `add_ace group.admin tml_banking.admin allow` |
| `groups` | `{ 'admin', 'god' }` | Framework permission groups that also grant access |
| `tools` | all on | `balances`, `freeze`, `credit`, `loans`, `issueLoans`, `launder` (allow business accounts to launder), `economy` (the economy dashboard). Off = hidden, and the server refuses it |
| `maxAdjust` | `10000000` | Largest single balance change |
| `economyDays` | `14` | Days of money in and out on the economy dashboard |

Staff with access also see an **Admin** button in the bank window.

## Discord webhooks (`config/webhooks.lua`)

Loaded on the server only, so the URLs are never sent to players.

| Option | Default | |
|---|---|---|
| `enabled` | `false` | Master switch |
| `default` | `''` | Webhook URL for any event whose own `url` is `''` |
| `username` / `avatar` | bank name / none | How the messages are posted |
| `showIdentifiers` | `true` | Include character identifiers |
| `events` | | Each: `enabled`, `url` and (money events) `minAmount` |

Events: `admin`, `transfer` (`minAmount` `50000`), `deposit`, `withdraw`, `external` (scripts through the exports),
`loanTaken`, `loanMissed`, `loanDefaulted`, `loanRepaid`, `loanForgiven`, `creditExternal`, `fraud`, `cardPayment`
(`minAmount` `10000`), `chequeBounced`, `loanDecided` (banker), `collateralSeized`, `approval`, `bankRobbed` (vaults and
ATMs), `skimmer` (found), `laundered` (`minAmount` `10000`), `characterDeleted`. A switched-on event with no
usable URL prints a warning on start.

## Compatibility (`Config.Compat`)

Answer the exports of `Renewed-Banking`, `qb-banking`, `qb-management`, `tgg-banking` and `esx_addonaccount` (all
`true`). Each is only switched on while the real resource isn't running.

## Theme and rate limits

`Config.Theme`: `sounds`, `volume`, and `vars` (any CSS variable from `web/theme.css`, e.g.
`{ ['--accent'] = '#22C55E' }`).

`Config.RateLimit` (ms between requests of one kind per player): `default` `150`, `open` `750`, `read` `250`, `write`
`1000`.
