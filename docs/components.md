# Component Catalogue

Mix and match components inside each section’s `components[]` array. Every component defines a `type`, `props`, and optional `data` binding. Below are the most common building blocks.

## ExpandableCardList

Ideal for transactional feeds with quick-glance metadata.

```json
{
  "type": "ExpandableCardList",
  "props": {
    "itemTitle": "customer_name",
    "itemSubtitle": "date",
    "leadingKey": "id",
    "trailingKey": "net_amount",
    "headerChips": ["item_name", "horse_name", "qty", "mode_name"],
    "chipLabels": {
      "item_name": "Item",
      "horse_name": "Horse",
      "qty": "Qty",
      "mode_name": "Payment"
    }
  },
  "data": { "source": "orders" }
}
```

**Tips**
- Reserve chips for high-signal fields; too many chips reduce scannability.
- Use `chipLabels` to translate backend keys into human-friendly text.

## SummaryCard

Surface KPI-style values computed from state or data.

```json
{
  "type": "SummaryCard",
  "props": {
    "items": [
      { "label": "Amount", "value": "@state.amount" },
      { "label": "Net", "value": "@state.netAmount" }
    ]
  }
}
```

Place summary cards before forms so users see totals update live.

## Text + Divider

`Text` uses style tokens (`title`, `subtitle`, `body`, `caption`) to keep typography consistent. Pair with `Divider` to separate logical groups without overwhelming the layout.

## FormGrid

The workhorse for multi-field forms. `columns` controls responsive layout; fields stack automatically on smaller breakpoints.

```json
{
  "type": "FormGrid",
  "props": {
    "columns": 2,
    "fields": [
      {
        "type": "Dropdown",
        "props": {
          "name": "item_id",
          "label": "Item",
          "required": true,
          "value": "@datasource.catalog.items[].id",
          "display_value": "@datasource.catalog.items[].name",
          "onChangeSet": { "rate": "@selected.rate" }
        },
        "data": { "source": "catalog" }
      },
      {
        "type": "TextField",
        "props": {
          "name": "quantity",
          "label": "Quantity",
          "keyboard": "number",
          "onChange": [
            { "type": "calc", "target": "amount", "formula": "rate*quantity", "fixed": 2 },
            { "type": "calc", "target": "netAmount", "formula": "amount - (amount*discount/100)", "fixed": 2 }
          ]
        }
      }
    ]
  }
}
```

**Field Guidelines**
- Use `prefixIcon` sparingly for quick recognition.
- Keep hints under 30 characters.
- Mark computed fields read-only and visually distinct (`filled: true`, muted text color).

## Button + Submit Action

```json
{
  "type": "Button",
  "props": {
    "text": "Submit",
    "icon": "send",
    "action": {
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
  }
}
```

Actions support composable side effects:
- `snack` – toast/snackbar notifications.
- `setState` – reset form fields or prime defaults.
- `navigate` – move to another section or screen.

## Extending the Library

When introducing new component types:
- Document required props so linting rules stay reliable.
- Provide sensible defaults (labels, icon sets) to reduce JSON verbosity.
- Keep visual style tokens centralized to maintain brand consistency.
