# Screen JSON Overview

This overview introduces the philosophy behind config-driven screens and highlights the root schema you will reference everywhere else in the guide.

## Why Config-Driven?

- Ship UI tweaks without redeploying the runtime.
- Keep design, product, and engineering stakeholders aligned on a human-readable contract.
- Reuse common layout patterns (lists, forms, nav bars) across teams.

## Quick Start Workflow

1. **Plan the experience** – outline the sections users need (lists, forms, summaries, help panels).
2. **Define data sources** – declare every remote or static dataset inside `dataSources`.
3. **Compose sections** – wire up `sections[]`, choose `container` styles, and drop in `components`.
4. **Bind data & state** – rely on `@datasource.*` and `@state.*` expressions to move values around.
5. **Add navigation** – expose sections via `bottomNav.items[]`, tabs, or inline links.
6. **Iterate quickly** – load the JSON, trigger fetches, run submissions, and polish copy before launch.

## Root Schema Reference

| Key | Purpose | Common Values |
| --- | --- | --- |
| `version` | Backward-compatible schema version | `1` |
| `type` | High-level layout template | `screen`, `wizard`, `modal` |
| `title` | App bar or page title | Any string |
| `padding` | Global padding (single number or object) | `2`, `{ "top": 8, ... }` |
| `appBar` | Toolbar tweaks or actions | `{ "centerTitle": true }` |
| `state` | Initial local state bag | `{}`, or defaults for form fields |
| `dataSources` | Remote/static data bindings + optional `pagination` blocks | `http`, `graphql`, `static` |
| `sections` | Ordered content blocks | Cards, tables, forms |
| `bottomNav` | Persistent navigation items | `items[] -> sectionId` |

> 💡 **Design tip**: Keep root keys consistent across projects so linters and schema validators can detect mistakes before shipping.
