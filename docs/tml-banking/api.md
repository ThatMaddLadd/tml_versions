All exports are server-side. Amounts are whole numbers. Exports are stable within a major version.

**"account"** arguments accept any of: an account number, a job or gang name, a character identifier (that
character's personal account), or a player's server id (their personal account).

Exports that read an offline character's personal balance wait on the database, so call them from a thread
(`CreateThread`) when that can happen. Until the bank has loaded, exports return `false, 'not_ready'`
(`IsReady()` tells you).

## Accounts

| Export | Returns | |
|---|---|---|
| `IsReady()` | boolean | The bank has loaded its accounts |
| `GetAccount(account)` | table or nil | `{ number, kind, owner, label, frozen, balance, created }` |
| `GetPersonalAccount(who)` | table or nil | `who` = server id or identifier |
| `GetAccounts(who)` | table[] | Server id: every account that player can see, with `rights`. Identifier: personal + savings/business they own or are on |
| `GetBalance(account)` | number or nil | |
| `CreateAccount(kind, owner, label, balance?)` | number or nil | `kind` `'job'`/`'gang'` (owner = name; returns the existing one) or `'savings'`/`'business'` (owner = identifier) |
| `SetFrozen(account, frozen)` | boolean | |
| `AddMember(account, identifier, name, role?)` | boolean | Savings (`role` ignored) or business (a role from the config) |
| `RemoveMember(account, identifier)` | boolean | |

`kind` is one of `personal`, `savings`, `business`, `job`, `gang`, `credit`.

## Money

| Export | Returns | |
|---|---|---|
| `AddMoney(account, amount, reason?, title?)` | `true, ref` or `false, err` | Statement line shows your resource |
| `RemoveMoney(account, amount, reason?, title?)` | `true, ref` or `false, err` | Fails with `insufficient_funds` when it can't afford it |
| `Transfer(from, to, amount, reason?, title?)` | `true, ref` or `false, err` | |
| `LogTransaction(account, data)` | `true, ref` | A statement line without moving money: `data = { amount, type = 'deposit'\|'withdraw'\|'transfer', title, reason?, counterparty? }` |
| `GetTransactions(account, limit?, offset?)` | table[] | Newest first, up to 500 |

```lua
-- Pay a mechanic job account for a repair
exports.tml_banking:AddMoney('mechanic', 450, 'Repair for ' .. plate, 'Repair')
```

## Loans

| Export | Returns | |
|---|---|---|
| `IssueLoan(who, spec, amount, account?, opts?)` | `true, loanId` or `false, err` | Give a player a loan (works with `Config.Loans.playerApply = false`) |
| `ForgiveLoan(id)` | `true` or `false, err` | Writes off everything left on an open loan |
| `GetLoanProducts()` | table[] | The products in `Config.Loans.products` |
| `GetLoans(who)` | table[] | Open loans: `{ id, product, label, account, status, principal, total, paid, owed, arrears, missed, nextDue }` |
| `GetLoanDebt(who)` | number | Owed on defaulted loans |
| `HasOverdueLoan(who)` | boolean | A payment overdue or a defaulted debt |
| `GetCollateralLoan(type, id)` | table or nil | The open loan secured on this collateral (same shape as the admin panel's loans) |
| `SeizeCollateral(loanId, value?)` | `true, debtLeft` or `false, err` | You repossessed the collateral of a defaulted loan |

`IssueLoan`:

- `who`: server id or character identifier (they may be offline).
- `spec`: a product name from the config, or custom terms
  `{ label = string, rate = number, installments = number, intervalHours = number, originationFee = number? }`.
- `account`: a personal or business account the borrower owns (default: their personal account).
- `opts.ignoreRules = true` skips the loan limit, cooldown, credit score, debt and amount checks (the account must
  still be the borrower's). `opts.credit = false` means this loan never changes their score.
- `opts.collateral = { type, id, label?, value? }` secures the loan on something your script owns, e.g.
  `{ type = 'vehicle', id = plate, label = 'Sultan RS', value = 45000 }`. One open loan per collateral.

```lua
-- A car dealer financing a sale: 20 payments, one a day, 12% interest, paid into the buyer's account
local ok, id = exports.tml_banking:IssueLoan(source, {
  label = 'Car finance', rate = 12, installments = 20, intervalHours = 24,
}, price, nil, { ignoreRules = true })
```

### Secured loans (repossession)

When a secured loan defaults, `tml_banking:server:loanDefaulted` fires with the collateral as its fourth argument.
Take the car (or house) back, then tell the bank:

```lua
AddEventHandler('tml_banking:server:loanDefaulted', function(loanId, borrower, owed, collateral)
  if not collateral or collateral.type ~= 'vehicle' then return end
  if impoundVehicle(collateral.id) then
    exports.tml_banking:SeizeCollateral(loanId) -- the car's value comes off what they owe
  end
end)
```

With `Config.Loans.collateral.writeOffOnSeize` the whole debt is written off instead. When the loan is paid, forgiven
or recovered, `tml_banking:server:collateralReleased` fires so you can lift any hold (for example, allow the car to
be sold). Before selling a car, `GetCollateralLoan('vehicle', plate)` tells you if it's still being paid for.

## Payslips

| Export | Returns | |
|---|---|---|
| `IssuePayslip(who, amount, opts?)` | `true, payslipNumber?` or `false, err` | Pays a player (`Config.Payslips`) |

`opts = { from?, employer?, period? }`: `from` = the account it's paid from (job name, account number; `nil` = new
money, like a framework paycheck), `employer` = name on the payslip (default: the account's name), `period` = e.g.
`'Week 12'`. With `mode = 'item'` the player gets a payslip item to cash at a bank (offline players, or players with no
room, are paid directly); with `'direct'` it goes straight into their bank.

```lua
-- A job script paying a shift from the job's account
exports.tml_banking:IssuePayslip(source, 850, { from = 'mechanic', period = 'Shift ' .. os.date('%d/%m') })
```

## Overdrafts, robbery and ATMs

| Export | Returns | |
|---|---|---|
| `GetOverdraft(who)` | number | The overdraft limit in use (0 = none) |
| `SetBankClosed(bank, minutes, reason?)` | boolean | `bank` = index in `Config.Banks`, its label, or a position near its teller. `minutes = true` until reopened, `false`/`0` reopens |
| `IsBankClosed(bank)` | boolean | |
| `RobBankVault(bank, amount)` | number | Takes up to `amount` from `Config.Robbery.vaultAccount` (or just reports it when that's `false`) and closes the bank with `closeOnRob`. Returns what was taken |
| `StealFromAtm(coords, amount)` | number | Takes cash out of an ATM (`Config.AtmCash`). Returns what there was |
| `SetAtmOutOfOrder(coords, minutes)` | boolean | The ATM refuses to open for a while (`0` = back in order) |
| `RefillAtm(coords, amount?)` | number or nil | Fills an ATM (to capacity, or by `amount`) from your own job script |
| `GetAtmCash(coords)` | number or nil | |
| `GetLowAtms()` | table[] | `{ x, y, z, cash }` for ATMs below `lowAt` |

```lua
-- A bank heist script
exports.tml_banking:SetBankClosed('Pacific Standard', 45, 'robbery')
local taken = exports.tml_banking:RobBankVault('Pacific Standard', math.random(150000, 300000))
```

## Characters

| Export | Returns | |
|---|---|---|
| `CharacterDeleted(identifier)` | `true, summary` or `false, err` | Call from your character-deletion code (`Config.Deletion`) |

The console command `tmlbank_deletechar <identifier>` does the same.

## Card payments

### `ChargeCard(src, amount, opts?) -> boolean, table|string`

Asks a player to pay by card: a payment pad lists the bank and credit cards they carry, they pick one and enter its PIN
(not needed up to `Config.CardPayments.contactlessLimit`). **Waits** for the answer (up to `timeoutSeconds`), so call it
from a thread. Returns `true, { card = last4, account = number, credit = boolean }` or `false, errorCode`
(`no_card`, `cancelled`, `timeout`, `busy`, `insufficient_funds`, `card_frozen`, `card_locked`, `card_limit`, ...).

| opts | |
|---|---|
| `merchant` | Name shown on the pad and the statement |
| `to` | Account it's paid into: job/gang name, account number or identifier. `nil` = the money leaves and your script handles the sale |
| `reason` | Statement note |

```lua
CreateThread(function()
  local ok, info = exports.tml_banking:ChargeCard(source, 1250, { merchant = "Benny's Motorworks", to = 'bennys', reason = 'Respray' })
  if ok then giveVehicleUpgrade(source) end
end)
```

## Direct debits

| Export | Returns | |
|---|---|---|
| `RegisterDirectDebit(who, spec)` | id or `nil, err` | A repeating payment from a player's account |
| `CancelDirectDebit(id)` | `true` or `false, err` | |
| `GetDirectDebits(who)` | table[] | Every direct debit on that character, from any resource |

`spec = { amount, intervalHours, label, account?, to?, count?, firstInHours? }`: `account` it's taken from (default:
their personal account), `to` = account it's paid into (job name, account number; `nil` = the money leaves and you
handle it on `debitPaid`), `count` = number of payments (default: until cancelled), `firstInHours` = delay before the
first payment (default: straight away).

```lua
-- Weekly rent for an apartment, paid to the realestate job's account
local id = exports.tml_banking:RegisterDirectDebit(source, { amount = 1200, intervalHours = 168, label = 'Rent: Alta St 4B', to = 'realestate' })
```

A payment that can't be made is retried after `Config.Payments.retryHours`; after `maxFails` in a row the debit is
stopped and `debitFailed` fires with `final = true`, so your script can evict, cancel the policy and so on.

## Credit cards and banker

| Export | Returns | |
|---|---|---|
| `GetCreditCard(who)` | table or nil | `{ card = { number, limit, balance, available, statement, minimum, paid, dueAt, nextStatement } }`, or `{ offer = { limit, fee } }` |
| `IsBanker(src)` | boolean | A banker on duty (`Config.Banker`) |

## Credit score

| Export | Returns | |
|---|---|---|
| `GetCreditScore(who)` | number or nil | nil when credit scores are off |
| `AdjustCreditScore(who, change, reason)` | new score | Capped to `Config.Credit.externalMaxChange` per call |
| `SetCreditScore(who, score, reason)` | new score | |
| `GetCreditHistory(who, limit?)` | table[] | `{ change_by, score, reason, resource, created }`, newest first |

```lua
exports.tml_banking:AdjustCreditScore(source, -20, 'Unpaid parking fines')
```

## Mobile banking (for phone apps)

Used by TML Phone's Bank app; any phone script can use them. They follow `Config.Mobile` and apply the bank's own
rights, fees and limits. Each returns data, or `nil` and an error code.

| Export | |
|---|---|
| `MobileOverview(src)` | Accounts (with `canSend`/`canView`), credit, transfer settings, cards, open loans |
| `MobileStatement(src, number, page)` | `{ rows, more }`, 25 per page |
| `MobileTransfer(src, from, to, amount, note?)` | `to` = account number, or a server id |
| `MobileCardFreeze(src, card, frozen)` | |
| `MobileCardLost(src, card)` | |
| `MobileLoanPay(src, id, amount?)` | No amount = pay off |
| `ErrorText(code)` | The bank's own message for an error code, in its language |

## Events (server)

Listen with `AddEventHandler`. They're local server events, so clients can't fire them.

| Event | Arguments |
|---|---|
| `tml_banking:server:transaction` | `row`: one statement line (`ref`, `account`, `amount`, `fee`, `balance`, `kind`, `title`, `reason`, `counterparty`, `counter_account`, `actor`, `actor_name`, `resource`, `created`, `card`, `accountKind`, `accountLabel`, `accountOwner`). A transfer fires once per side |
| `tml_banking:server:creditChanged` | `identifier, score, change, reason, resource` (`resource` is nil for the bank's own changes) |
| `tml_banking:server:loanTaken` | `id, borrower, amount, issuer` |
| `tml_banking:server:loanPayment` | `id, borrower, amount` |
| `tml_banking:server:loanMissed` | `id, borrower, missedInARow` |
| `tml_banking:server:loanDefaulted` | `id, borrower, stillOwed, collateral` (`collateral` = `{ type, id, label, value }` or nil) |
| `tml_banking:server:collateralSeized` | `id, borrower, collateral, taken, stillOwed` |
| `tml_banking:server:collateralReleased` | `id, borrower, collateral, status` |
| `tml_banking:server:loanRepaid` | `id, borrower` |
| `tml_banking:server:loanForgiven` | `id, borrower` |
| `tml_banking:server:debitPaid` | `id, kind, owner, amount, resource` (`kind` = `'order'` or `'debit'`) |
| `tml_banking:server:debitFailed` | `id, kind, owner, amount, failsInARow, final, resource` |
| `tml_banking:server:scheduleCancelled` | `id, kind, owner, byPlayer` |
| `tml_banking:server:payroll` | `account, total, paidCount` |
| `tml_banking:server:cardPayment` | `src, amount, card, fromAccount, toAccount, resource` |
| `tml_banking:server:chequeCashed` | `number, writer, cashedBy, amount` |
| `tml_banking:server:chequeBounced` | `number, writer, amount` |
| `tml_banking:server:loanApplied` | `identifier, product, amount` |
| `tml_banking:server:loanDecided` | `applicationId, identifier, approved, bankerIdentifier` |
| `tml_banking:server:fraudFlag` | `identifier, kind, account, amount, detail, refused` |
| `tml_banking:server:payslipIssued` | `number, identifier, amount, employer` |
| `tml_banking:server:approvalRequested` | `account, action, amount, requester` |
| `tml_banking:server:approvalDecided` | `id, account, action, amount, requester, approver` |
| `tml_banking:server:bankClosed` | `bankIndex, closed, reason` |
| `tml_banking:server:bankRobbed` | `bankIndex, taken` |
| `tml_banking:server:atmRobbed` | `atmId, coords, taken` |
| `tml_banking:server:atmRefilled` | `atmId, coords, added, src` |
| `tml_banking:server:skimmerInstalled` | `src, coords, alert` (`alert` = roll `Config.Skimming.alertChance` passed: tell your dispatch) |
| `tml_banking:server:skimmerFound` | `src, coords, owner, cardsCopied` |
| `tml_banking:server:laundered` | `src, account, markedAmount, cleanAmount` |
| `tml_banking:server:characterDeleted` | `identifier, name, moved, toAccount, by` |
| `tml_banking:server:adminAction` | `src, action, text, info`: `info = { identifier?, account?, amount?, reason?, targetName?, targetSrc? }` (who or what it was on) |

## Client

`TriggerEvent('tml_banking:openAdmin')` opens the admin panel (the server checks the permission).

## Error codes

`not_ready`, `not_loaded`, `no_account`, `no_player`, `bad_amount`, `account_frozen`, `target_frozen`,
`insufficient_funds`, `no_permission`, `limit_reached`, `same_account`, `recipient_offline`, `rate_limited`, `failed`,
and for loans `no_product`, `no_loan`, `loan_max`, `loan_same`, `loan_blocked`, `loan_cooldown`, `loan_score`,
`loan_job`, `loan_new_customer`, `loan_wrong_account`, `loan_too_soon`, and `fraud_new_character`, `fraud_velocity`,
`no_card`, `cancelled`, `busy`, `card_timeout`, `bad_interval`, `no_schedule`, `bad_collateral`, `collateral_taken`,
`loan_not_defaulted`, `approval_sent`, `bank_robbed`, `atm_out_of_order`, `atm_empty`, `atm_low_cash`, `no_bank`. Each has
a message `err_<code>` in the locale files.

## Compatibility exports

With `Config.Compat`, scripts written for these keep working without changes, as long as the real resource isn't
running: Renewed-Banking (`getAccountMoney`, `addAccountMoney`, `removeAccountMoney`, `handleTransaction`, ...),
qb-banking, qb-management, tgg-banking and esx_addonaccount society accounts.
