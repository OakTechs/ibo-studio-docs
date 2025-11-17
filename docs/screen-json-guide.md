# Building Config-Driven Screens

A screen definition JSON lets you design entire CRUD-style flows without touching the app runtime. This guide walks through the schema conventions, expressions, and UX patterns so any engineer can author their own screens confidently.

## Quick Start Workflow

1. **Plan the experience**: list the sections your users need (lists, forms, summaries).
2. **Define data sources**: map each remote resource (REST, GraphQL, static JSON) to a `dataSources` entry.
3. **Describe sections**: add `sections[]` objects for every page surface, choose a `container`, and wire the `components`.
4. **Bind data & state**: use `@datasource.*` and `@state.*` expressions to move information through the UI.
5. **Add navigation**: connect sections with `bottomNav.items[]` or other navigation primitives.
6. **Test iteratively**: load the JSON in your host app, trigger data fetches, submit forms, and polish the copy.

---

## Root Schema Overview

| Key | Purpose | Common Values |
| --- | --- | --- |
| `version` | Format revision for backward compatibility | `1` |
| `type` | High-level layout template | `screen`, `wizard`, `modal` |
| `title` | Displayed in app bar or top of page | Any string |
| `padding` | Global padding in logical pixels | Number or object {top,right,bottom,left} |
| `appBar` | Optional top-bar tweaks | `{ "centerTitle": true }`, icons, actions |
| `state` | Initial local state bag | `{}` or pre-filled defaults |
| `dataSources` | Remote/static data fetchers | See below |
| `sections` | Ordered content blocks | Cards, forms, lists, etc. |
| `bottomNav` | Persistent navigation | `items[]` linking to section IDs |

> 💡 **Design tip**: keep root keys predictable. Consumers can validate quickly and lint for required fields before shipping.

---

## Data Sources

`dataSources` centralizes every remote dependency. Each key represents a named dataset accessible through `@datasource.<name>`.

```json
"dataSources": {
  "orders": {
    "type": "http",
    "method": "GET",
    "url": "https://api.example.com/orders?limit=50"
  },
  "catalog": {
    "type": "http",
    "method": "GET",
    "url": "https://api.example.com/catalog/master",
    "cache": true,
    "once": true
  }
}
```

**Guidelines**
- Use verbs for dynamic feeds (`orders`, `reservations`) and nouns for static dictionaries (`catalog`, `paymentModes`).
- Prefer `cache: true` plus `once: true` for lookups that rarely change to avoid redundant requests.
- Keep URLs environment-agnostic by injecting base paths at runtime or via build variables.

---

## Sections & Layout Containers

Every `sections[]` entry defines a slice of the screen and typically maps 1:1 with a tab, card, or scroll area.

```json
{
  "id": "list",
  "title": "Latest Orders",
  "container": "none",
  "padding": { "top": 0, "right": 0, "bottom": 0, "left": 0 },
  "components": [ /* ... */ ]
}
```

- `id` powers navigation (`bottomNav.sectionId`) and in-screen links.
- `container` can be `none`, `card`, or `sheet` to control visual chrome.
- `padding` overrides root spacing for precise layouts.

### Common Section Types
- **Feed section**: `ExpandableCardList`, `Table`, or `Chart` bound to a data source.
- **Form section**: One or more `FormGrid` blocks with helper `Text`, `Divider`, and summary components.
- **Utility section**: static content, setup instructions, or future placeholders.

---

## Components Catalogue

Mix and match components inside `components[]`. Each entry has a `type`, `props`, and optional `data` binding. Below are patterns extracted from the reference screen.

### ExpandableCardList

Great for transactional feeds with metadata chips.

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

Key ideas:
- `itemTitle`/`itemSubtitle` accept field names from the bound dataset.
- Chips make secondary attributes scannable; rename with `chipLabels` for clarity.

### SummaryCard

Use for real-time totals computed from state or data.

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

Bind to `@state` for instant feedback as inputs change.

### Text & Divider

`Text` supports `style` tokens (`title`, `subtitle`, `body`) to keep typography consistent.
`Divider` visually separates logical groups—use sparingly to avoid noise.

### FormGrid

The workhorse for forms. `columns` controls responsive layout. Each field entry includes a `type`, `props`, and optionally `data` or `onChange` logic.

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

**Field Tips**
- Provide `prefixIcon` sparingly to aid recognition.
- Use `hint` for microcopy; keep it short (<30 characters).
- Keep computed fields read-only and styled differently (`filled: true`, subdued text color).

### Button with Submit Action

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

Actions support composable side-effects (snackbars, state resets, navigation) so users get instant feedback.

---

## Expression Language

- `@state.<key>` references local state, writable via form inputs or `setState` actions.
- `@datasource.<name>.<path>` traverses fetched data. Use `[].field` when binding to arrays in dropdowns.
- `@selected.<field>` pulls the currently selected option within dropdown change handlers.
- `calc` actions let you define lightweight formulas (`rate*quantity`). Use `fixed` to format decimals.

Keep formulas simple; for complex logic, consider server-computed values or reusable derived-state helpers.

---

## Bottom Navigation

```json
"bottomNav": {
  "items": [
    { "label": "List", "icon": "list", "sectionId": "list" },
    { "label": "Add", "icon": "add", "sectionId": "form" },
    { "label": "Items", "icon": "items", "sectionId": "catalog" }
  ]
}
```

- Keep labels to one word when possible.
- Use icons from your shared asset catalog to maintain brand consistency.
- Ensure every `sectionId` exists; lints should catch mismatches early.

---

## Building a New Screen Step-by-Step

1. **Kickoff**
   - Interview stakeholders, capture required datasets, CRUD actions, and KPIs.
2. **Draft schema**
   - Start with metadata (`version`, `title`, `appBar`).
   - Declare every `dataSources` entry even if the endpoint is stubbed.
3. **Design sections**
   - Sketch the layout (pen + paper is fine) before writing JSON.
   - Group related fields into `FormGrid` clusters of 2 columns; switch to 1 column on mobile by lowering the count.
4. **Wire expressions**
   - Define `state` defaults for all form fields to avoid undefined values.
   - Add `onChange` calculators for dependent totals.
5. **Polish UX**
   - Insert `SummaryCard` at the top of forms for at-a-glance totals.
   - Add `Divider` and `Text` labels for readability.
6. **QA**
   - Validate required fields; ensure `required: true` matches backend constraints.
   - Test offline/slow-network scenarios—make sure cached sources have `cache: true`.
7. **Document**
   - Embed the final JSON in your repo, cite endpoint contracts, and link this guide for future authors.

---

## Best Practices & Design Notes

- **Consistency first**: Align field names with API payload keys to minimize mapping glue.
- **Short feedback loop**: Use `snack` actions for confirmations and `setState` resets to ready the next entry.
- **Security**: Never hardcode secrets or auth tokens inside JSON. Rely on the host app to inject headers.
- **Accessibility**: Provide descriptive `label`s and avoid icon-only buttons.
- **Localization**: Keep user-facing strings (`title`, chip labels, button text) centralized for translation.
- **Versioning**: Increment `version` when you introduce incompatible schema changes so clients can migrate safely.

---

## Reference Template

Below is a condensed template you can duplicate and customize. Replace placeholders (ALL_CAPS) with your project-specific names.

```json
{
  "version": 1,
  "type": "screen",
  "title": "SCREEN_TITLE",
  "padding": 2,
  "appBar": { "centerTitle": true },
  "state": {
    "date": "",
    "customer_name": "",
    "item_id": "",
    "quantity": "",
    "rate": "",
    "discount": "0",
    "amount": "0.00",
    "netAmount": "0.00"
  },
  "dataSources": {
    "primaryFeed": {
      "type": "http",
      "method": "GET",
      "url": "https://API_BASE/primary"
    },
    "masterData": {
      "type": "http",
      "method": "GET",
      "url": "https://API_BASE/master",
      "cache": true,
      "once": true
    }
  },
  "sections": [
    { "id": "feed", "container": "none", "components": [/* list components */] },
    { "id": "form", "title": "Add Entry", "container": "card", "components": [/* form components */] },
    { "id": "extras", "title": "Extras", "components": [/* placeholder */] }
  ],
  "bottomNav": {
    "items": [
      { "label": "Feed", "icon": "feed", "sectionId": "feed" },
      { "label": "Add", "icon": "add", "sectionId": "form" },
      { "label": "More", "icon": "more", "sectionId": "extras" }
    ]
  }
}
```

---

## Where to Go Next

- Add schema validation (AJV, Zod) to catch mistakes before deployment.
- Build a playground that hot-reloads this JSON for rapid prototyping.
- Layer in role-based access by toggling sections/components at runtime.

Happy building! Add this guide to your internal docs site so future contributors can spin up new screens in hours instead of days.
