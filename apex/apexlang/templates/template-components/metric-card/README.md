# Metric Card Templates

## Purpose
Canonical Metric Card template pack for `themeTemplateComponent/metricCard`, using MD-first scenario templates with one canonical APX skeleton.

## Usage
- Load `metric-card._common.md` first for variable contract and generation guardrails.
- Use `metric-card._index.md` to confirm the routing entrypoint and load order.
- Metric Card regions always emit `type: themeTemplateComponent/metricCard` with `componentAppearance.display: report`.
- Metric Card regions should always emit a region `appearance {}` block.
- Dashboard KPI strips default to `template: @/blank-with-attributes` so the Metric Card has no visible standard region chrome.
- Use `template: @/standard` only when the design explicitly needs a titled or landmarked visible region wrapper.
- Emit `advanced { htmlDomId: ... }` only when external page logic must target a known region DOM id; native runtime behavior does not require it.
- Column source mappings use the `column-metric-card` child template.
- Metric Card child columns support the documented `column.source.dataType` inventory in `metric-card._common.md`; emit those tokens exactly.
- Report-mode Metric Card requires explicit child `column (...)` metadata for every delivered source projection. Do not satisfy the compiler minimum by adding only one column when the source projects more than one field.
- Metric Card `settings` expose more than `title` and `metric`; use `meta`, the CSS-class hooks, and layout/item CSS properties from `metric-card._template_options.md` when the design needs them.
- Metric Card may also use `plugin-avatar`, `plugin-badge`, and `rowSelection` in accepted scenarios. Do not force those property names to mirror child-column names; they are value hooks, not a second column declaration system.
- For Metric Card avatar rendering, use `plugin-avatar.displayAvatar` plus the typed avatar payload inside `plugin-avatar`.
- Do not emit `settings.displayAvatar` for Metric Card; use `plugin-avatar.displayAvatar` instead.
- For Metric Card badge rendering, use `plugin-badge.displayBadge` plus badge fields inside `plugin-badge`.
- Do not emit `settings.displayBadge` for Metric Card; use `plugin-badge.displayBadge` instead.
- Metric Card `rowSelection` can also be focus-only. Use `focusOnly` with no page-item bindings, `singleSelection` with `currentSelectionPageItem`, or `multipleSelection` with both `currentSelectionPageItem` and `selectAllPageItem`.
- When Metric Card uses any `rowSelection` mode, one child column must carry `source.primaryKey: true`.
- A single Metric Card region can render multiple cards from multiple source rows.
- When one region must show several independent metrics, prefer a normalized multi-row SQL source. The default pattern is `UNION ALL` across one SELECT per metric so every row projects the same aliases in the same order.
- Load `metric-card._template_options.md` for Metric Card-specific layout and grouping values.
- Load `../avatar/avatar._template_options.md` for accepted Metric Card avatar value inventory.
- Load `../badge/badge._template_options.md` for accepted Metric Card badge value inventory.

## Template Catalog
- `metric-card._common.md`
- `metric-card._index.md`
- `metric-card._template_options.md`

## Maintenance
- Keep this README synchronized with actual files in this directory.
- New or updated scenario docs should include: `# Purpose`, `# Output Template`, `# Conditional Rendering Rules`, `# Validation Checklist`.
