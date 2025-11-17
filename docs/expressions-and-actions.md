# Expressions & Actions

Expressions let you bind data to UI elements and perform lightweight calculations without writing custom code. Actions control what happens after user input (submissions, notifications, state updates).

## Expression Primitives

- `@state.<key>` – references local state managed by form inputs or `setState` actions.
- `@datasource.<name>.<path>` – accesses fetched data; use `[].field` when reading array items for dropdowns.
- `@selected.<field>` – exposes properties of the option currently chosen inside dropdown change handlers.

## Calculations

`calc` actions allow inline math so you can keep the JSON declarative.

```json
{
  "type": "calc",
  "target": "netAmount",
  "formula": "amount - (amount*discount/100)",
  "fixed": 2
}
```

- `target`: state key to update.
- `formula`: arithmetic expression referencing other state keys.
- `fixed`: optional decimal precision for numeric formatting.

## Submit Actions

Submit actions send state to remote endpoints and orchestrate success/failure effects.

```json
{
  "type": "submit",
  "url": "https://api.example.com/orders",
  "method": "POST",
  "body": {
    "date": "@state.date",
    "customer": "@state.customer_name",
    "item_id": "@state.item_id",
    "quantity": "@state.quantity",
    "amount": "@state.amount"
  },
  "onSuccess": [
    { "type": "snack", "message": "Order submitted" },
    { "type": "setState", "values": { "quantity": "", "discount": "", "amount": "", "netAmount": "" } }
  ]
}
```

### Side-Effect Palette

- `snack`: show transient confirmation or error hints.
- `setState`: wipe forms, apply defaults, or mirror backend responses locally.
- `navigate`: jump to another section once submission is complete.
- `refresh`: re-fetch a data source to show the newest records.

## Best Practices

- Keep formulas simple—complex business logic belongs on the server.
- Validate required state before submission; surface inline errors close to the offending field.
- Chain multiple `onSuccess` effects for richer UX (snack → refresh list → reset form).
- Plan for failure paths (e.g., add `onError` snacks) so users know what to do next.
