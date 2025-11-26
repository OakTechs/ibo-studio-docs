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
- `CheckBox` – boolean selection for agreements, consents, and independent toggles.
- `RadioGroup` – single selection from 2-7 mutually exclusive options; use `Dropdown` for longer lists.
- `Switch` – on/off toggle for settings with immediate effect; more prominent than checkboxes.

## DateField

Native date picker with extensive customization options for forms and data entry.

```json
{
  "type": "DateField",
  "props": {
    "name": "sale_date",
    "label": "Sale Date",
    "required": true,
    "displayFormat": "long",
    "filled": true,
    "prefixIcon": "event",
    "suffixIcon": "calendar_today",
    "hint": "Select sale date",
    "minDate": "2024-01-01",
    "maxDate": "today",
    "borderRadius": 12,
    "primaryColor": "#6750A4"
  }
}
```

**Key Properties**
- `displayFormat` – controls visual presentation: `"default"` (ISO), `"us"` (MM/dd/yyyy), `"eu"` (dd/MM/yyyy), `"long"` (November 26, 2024), `"short"` (Nov 26, 2024), or `"custom"` with `customFormat` pattern
- `minDate` / `maxDate` – constrain selectable range; use `"today"` or ISO strings like `"2024-01-01"`
- `yearRange` – years before/after current (default: 10); set to 100 for birth dates
- `filled` – enable background fill for modern appearance
- `borderRadius` – corner roundness (8-16 typical); pair with `borderColor` and `focusedBorderColor`
- `primaryColor` – theme color for calendar UI and selection highlights
- `prefixIcon` / `suffixIcon` – Material icons (`event`, `calendar_today`, `date_range`)

**Common Patterns**

Birth date (past only):
```json
{
  "name": "birth_date",
  "label": "Date of Birth",
  "maxDate": "today",
  "yearRange": 100,
  "displayFormat": "long"
}
```

Future bookings:
```json
{
  "name": "appointment_date",
  "label": "Appointment Date",
  "minDate": "today",
  "yearRange": 1,
  "helperText": "Available dates: Mon-Fri"
}
```

**Tips**
- Date values always stored as ISO strings (yyyy-MM-dd) regardless of display format
- Use `helperText` to communicate date constraints clearly
- Match `primaryColor` and `iconColor` to your theme for visual consistency
- Combine `filled: true` with light `fillColor` for Material Design 3 aesthetic

## CheckBox

Boolean selection with a checkmark for agreements, toggles, and multi-select options.

```json
{
  "type": "CheckBox",
  "props": {
    "name": "acceptTerms",
    "label": "I accept the terms and conditions",
    "required": true
  }
}
```

**Key Properties**
- `name` – state key for the boolean value (`true`/`false`)
- `label` – text displayed next to the checkbox
- `required` – adds asterisk (*) to label for mandatory fields
- `initialValue` – pre-check the box; supports boolean or `@state`/`@datasource` references
- `onChange` – trigger actions when checked/unchecked

**Common Patterns**

Terms acceptance:
```json
{
  "name": "consent",
  "label": "I consent to data processing",
  "required": true
}
```

Multi-select preferences:
```json
{
  "type": "FormGrid",
  "props": {
    "columns": 1,
    "fields": [
      { "type": "CheckBox", "props": { "name": "newsletter", "label": "Subscribe to newsletter" } },
      { "type": "CheckBox", "props": { "name": "updates", "label": "Receive product updates" } },
      { "type": "CheckBox", "props": { "name": "promotions", "label": "Receive promotional offers" } }
    ]
  }
}
```

**Tips**
- Use for agreements, consents, and independent boolean choices
- Store values as `@state.<name>` for form submission
- Prefer `Switch` for settings with immediate effect; use `CheckBox` for form submissions

## RadioGroup

Mutually exclusive single selection from a visible list of options.

```json
{
  "type": "RadioGroup",
  "props": {
    "name": "paymentMethod",
    "label": "Payment Method",
    "required": true,
    "options": [
      { "value": "card", "label": "Credit Card" },
      { "value": "paypal", "label": "PayPal" },
      { "value": "bank", "label": "Bank Transfer" }
    ]
  }
}
```

**Key Properties**
- `name` – state key storing the selected option's `value`
- `options` – array of `{ "value": "...", "label": "..." }` objects
- `initialValue` – pre-select an option by its `value`
- `required` – mark as mandatory
- `data` – load options dynamically from a data source

**Dynamic Options**

```json
{
  "type": "RadioGroup",
  "data": {
    "source": "departments",
    "valueKey": "id",
    "labelKey": "name"
  },
  "props": {
    "name": "department",
    "label": "Select Department"
  }
}
```

**Common Patterns**

Size selection:
```json
{
  "name": "size",
  "label": "T-Shirt Size",
  "initialValue": "medium",
  "options": [
    { "value": "small", "label": "Small" },
    { "value": "medium", "label": "Medium" },
    { "value": "large", "label": "Large" }
  ]
}
```

Rating scale:
```json
{
  "name": "satisfaction",
  "label": "How satisfied are you?",
  "required": true,
  "options": [
    { "value": "5", "label": "Very Satisfied" },
    { "value": "4", "label": "Satisfied" },
    { "value": "3", "label": "Neutral" },
    { "value": "2", "label": "Dissatisfied" }
  ]
}
```

**Tips**
- Best for 2-7 options; use `Dropdown` for 8+ options
- Always mutually exclusive—only one selection allowed
- Vertical layout improves accessibility
- Use `onChange` to cascade related field updates

## Switch

Toggle control for on/off states with immediate visual feedback, ideal for settings.

```json
{
  "type": "Switch",
  "props": {
    "name": "notifications",
    "label": "Enable Notifications",
    "activeColor": "#4CAF50"
  }
}
```

**Key Properties**
- `name` – state key for the boolean value (`true`/`false`)
- `label` – text displayed next to the switch
- `activeColor` – hex color when toggled on (e.g., `"#4CAF50"`)
- `initialValue` – start in on/off state
- `onChange` – trigger actions on toggle

**Common Patterns**

Settings panel:
```json
{
  "type": "FormGrid",
  "props": {
    "columns": 1,
    "fields": [
      { "type": "Switch", "props": { "name": "darkMode", "label": "Dark Mode", "activeColor": "#2196F3" } },
      { "type": "Switch", "props": { "name": "autoSave", "label": "Auto-save Changes", "activeColor": "#2196F3" } },
      { "type": "Switch", "props": { "name": "soundEffects", "label": "Sound Effects", "activeColor": "#2196F3" } }
    ]
  }
}
```

With API sync:
```json
{
  "name": "betaFeatures",
  "label": "Beta Features",
  "activeColor": "#9C27B0",
  "onChange": [
    {
      "type": "submit",
      "url": "/api/user/preferences",
      "method": "PATCH",
      "body": { "betaFeatures": "@state.betaFeatures" }
    }
  ]
}
```

**Tips**
- Use for settings that take immediate effect
- More prominent than checkboxes—better for feature toggles
- Consistent `activeColor` creates visual hierarchy
- Prefer over `CheckBox` when the action is instant (not form-based)

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
