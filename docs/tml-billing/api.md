Amounts are whole numbers. Exports are stable within a major version.

## Server exports

### `CreateInvoice(issuer, recipient, data) -> number|nil, string?`

Sends an invoice. Returns the invoice id, or `nil` and an error code.

| Param | Type | Notes |
|---|---|---|
| `issuer` | number or `nil` | Server id of the player sending it. `nil` = sent by your script (no distance or job check, no commission) |
| `recipient` | number | Server id of the player billed (must be online) |
| `data.business` | string | Key in `Config.Businesses`. Required when `issuer` is `nil` |
| `data.items` | table | `{ { label = string, quantity = number, price = number }, ... }` |
| `data.note` | string? | Shown on the invoice |
| `data.issuerName` | string? | Name shown as the issuer when `issuer` is `nil` (default: the business label) |

```lua
-- A speed camera fine
exports.tml_billing:CreateInvoice(nil, source, {
  business = 'police',
  items = { { label = 'Speed camera: 112 mph in a 60 zone', quantity = 1, price = 400 } },
  issuerName = 'Speed camera',
})
```

Error codes: `invalid`, `no_permission`, `bad_recipient`, `too_far`, `bad_items`, `too_much`, `not_ready`.

### `GetUnpaidTotal(source) -> number`

Total a player owes on unpaid invoices, including late fees so far.

### `GetInvoices(source) -> table[]`

The player's invoices as shown in their "My bills" list (`id`, `label`, `kind`, `items`, `amount`, `fee`, `total`,
`status`, `created`, `due`, `overdue`, ...).

### `CancelInvoice(id) -> boolean`

Cancels an unpaid invoice.

## Client exports

| Export | What it does |
|---|---|
| `OpenBilling()` | Opens the billing window |

## Server events

Fired with `TriggerEvent` for your own server scripts. They aren't net events, so clients can't fire them.

| Event | Arguments |
|---|---|
| `tml_billing:created` | `(id, issuerSource or false, recipientSource, business or false, amount)` |
| `tml_billing:paid` | `(id, payerSource, business or false, total)`. `total` includes any late fee |
| `tml_billing:declined` | `(id, recipientSource)` |
| `tml_billing:cancelled` | `(id, source)` |

```lua
AddEventHandler('tml_billing:paid', function(id, payer, business, total)
  if business == 'police' then print(('Fine #%d paid: $%d'):format(id, total)) end
end)
```

## Bridge

Adapters expose a fixed set of functions (see `bridge/README.md`). Other resources shouldn't call them.
