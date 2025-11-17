# Sections & Navigation

Every `sections[]` entry defines a slice of your screen surface. The host app stitches them together in the order they appear, so keep the JSON readable and predictable.

## Section Anatomy

```json
{
  "id": "list",
  "title": "Latest Orders",
  "container": "none",
  "padding": { "top": 0, "right": 0, "bottom": 0, "left": 0 },
  "components": [ /* ... */ ]
}
```

- `id`: unique handle for navigation (e.g., `bottomNav.sectionId`) and deep links.
- `title`: optional heading shown in headers or cards.
- `container`: `none`, `card`, `sheet`, etc. controls chrome.
- `padding`: overrides root spacing for tight layouts.

## Common Section Patterns

- **Feed section** – use `ExpandableCardList`, `Table`, or `Chart` bound to a data source.
- **Form section** – combine `SummaryCard`, `FormGrid`, `Text`, and `Divider` components.
- **Utility section** – placeholders, setup instructions, or future integrations.

## Layout Tips

- Group related fields into separate sections instead of massive monoliths.
- Keep per-section copy short; use helper text components when extra guidance is needed.
- Reuse sections across nav entries when you need the same layout with different filters (clone the JSON object, change the `data.source`).

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

- Align `sectionId` with actual entries; linters should fail fast if mismatched.
- Prefer one-word labels and brand-approved icons.
- Consider hiding nav items with feature flags when rolling out gradually.

## Padding Strategy

When sections require different density, override root padding per section:

```json
"padding": {
  "top": 8,
  "right": 0,
  "bottom": 0,
  "left": 0
}
```

This keeps feeds edge-to-edge while forms remain spacious.
