# Build Playbook

This checklist walks you through the lifecycle of authoring a new config-driven screen, from kickoff to QA and documentation.

## Step-by-Step

1. **Kickoff**
   - Interview stakeholders; identify required datasets, CRUD actions, KPIs.
2. **Draft schema**
   - Set `version`, `type`, `title`, and `appBar` defaults.
   - Declare `dataSources` even if endpoints are mocked.
3. **Design sections**
   - Sketch the layout before writing JSON (pen, whiteboard, or Figma is fine).
   - Split large flows into multiple sections for clarity.
4. **Wire expressions**
   - Seed `state` with defaults to avoid undefined references.
   - Add `calc` actions wherever totals or discounts must auto-update.
5. **Polish UX**
   - Place `SummaryCard` components at the top for at-a-glance metrics.
   - Use `Divider` and `Text` to label form groups clearly.
6. **QA**
   - Confirm `required: true` aligns with backend validation.
   - Simulate slow/offline states to ensure cached sources behave.
7. **Document**
   - Commit the JSON along with endpoint contracts and release notes.
   - Link back to this guide so future authors follow the same rules.

## Best Practices

- **Consistency first**: keep field names aligned with API payload keys.
- **Short feedback loop**: combine `snack` notifications with `setState` resets.
- **Security**: never embed secrets or auth tokens; let the host app add headers.
- **Accessibility**: provide descriptive labels, avoid icon-only buttons, and respect tab order.
- **Localization**: isolate user-facing strings so translators can batch updates.
- **Versioning**: bump `version` when you introduce breaking changes to the schema or component contracts.
