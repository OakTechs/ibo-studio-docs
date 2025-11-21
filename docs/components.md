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
- Pair with `searchStateKey`, `searchFields[]`, and `minSearchLength` when you want in-memory filtering, or leave them out for simple scroll-only feeds.
- Enable `manualPagination` + `pageSize` if you need the component to request more rows from the server (works well with the `pagination` block in your data source).

## SearchBar

Surface lightweight filtering in the same section as your list.

```json
{
  "type": "SearchBar",
  "props": {
    "stateKey": "schemeFilter",
    "placeholder": "Search schemes or codes",
    "leadingIcon": "search",
    "showClearButton": true,
    "filled": true,
    "borderRadius": 24,
    "minSearchLength": 3,
    "padding": { "left": 12, "right": 12, "top": 8, "bottom": 4 }
  }
}
```

- `stateKey` wires the input to `@state.<key>`; reuse that key inside `searchStateKey` on downstream lists.
- `minSearchLength` defers list filtering until the user types enough characters.
- `showClearButton`, `leadingIcon`, and `padding` keep the control accessible and inline with your layout density.

> Keep helper text short and let the empty state on your list explain what to try next.

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

### Field Types Cheat-Sheet

- `DateField` – native date picker wired to ISO strings; always pair with `required` when submissions depend on it.
- `TextField` – workhorse input; use the `keyboard` hint (`number`, `email`, etc.) to summon the right keypad on mobile.
- `Dropdown` – reference array data via `value`/`display_value` plus a `data.source`; lean on `onChangeSet` + `onChange` (see *Expressions & Actions*) to cascade derived values.

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
