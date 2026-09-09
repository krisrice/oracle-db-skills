---
templateId: region.metric-card.common
componentType: region
version: 1.0
description: Shared contract for metric-card regions.
---

# Purpose

Document the native metric-card region emitted as `themeTemplateComponent/metricCard`.

# Generation Rules (MANDATORY)

1. Use the dedicated `region-metric-card` template.
2. Always emit a region `appearance {}` block for Metric Card.
3. Use a valid region template such as `@/blank-with-attributes` or `@/standard`.
4. For dashboard KPI strips, default `appearance.template` to `@/blank-with-attributes` so the Metric Card renders without visible standard region chrome.
5. Use `@/standard` for Metric Card only when the requested design explicitly needs a titled or landmarked visible region wrapper.
6. Emit `advanced { htmlDomId: ... }` only when external client code, dynamic actions, or other page logic must target a known region DOM id.
7. Keep `display: report` in `componentAppearance`.
8. Use `column-metric-card` for source-column mappings.
9. Keep plugin attribute names and enumerated values aligned with `metric-card._template_options.md`, `../avatar/avatar._template_options.md`, and `../badge/badge._template_options.md`.
10. In report mode, treat child `column (...)` metadata as part of the emitted Metric Card source contract, not as an optional compiler appeasement detail.
11. Emit explicit child `column (...)` metadata for every delivered source projection before finals. Do not satisfy the compiler by adding only one placeholder column when the source projects multiple columns.
12. A single Metric Card region can render multiple cards. When the prompt asks for several independent metrics in one region, prefer a multi-row SQL source that normalizes the projections for each card, commonly by `UNION ALL`-ing one row per metric into a shared column shape.
13. Metric Card `settings`, `plugin-avatar`, `plugin-badge`, and `rowSelection` expose more value hooks than just `title` and `metric`. Use the accepted property surface from `metric-card._template_options.md`, `../avatar/avatar._template_options.md`, and `../badge/badge._template_options.md` when those behaviors are needed.
14. Do not require settings property names to match child-column names one-for-one. Those properties may bind to literals, `&COLUMN_NAME.` substitutions, or other accepted placeholders; the child `column (...)` blocks still describe the delivered source projection.
15. Metric Card avatar support is export-backed in this runtime through `plugin-avatar {}`. Use `plugin-avatar.displayAvatar` for visibility and keep avatar configuration in that block.
16. Metric Card badge support is export-backed in this runtime through `plugin-badge {}`. Use `plugin-badge.displayBadge` for visibility and keep badge configuration in that block.
17. When `rowSelection` is emitted with any non-null mode, at least one child `column (...)` must mark the row identity with `source.primaryKey: true`.
18. For every emitted child column, set `source.dataType` to one exact token from the Metric Card Column Data Types inventory below.

# Variable Contract

| Name | Required | Type | Notes |
|------|----------|------|-------|
| regionStaticId | yes | string | Region identifier. |
| name | yes | string | Display name. |
| type | yes | enum | Always `themeTemplateComponent/metricCard`. |
| appearance.template | yes | alias | Region template alias such as `@/standard` or `@/blank-with-attributes`. |
| appearance.templateOptions | optional | array/string | Template modifiers. Default to `#DEFAULT#` unless the chosen region template documents another accepted value. |
| advanced.htmlDomId | optional | string | Use only when external page logic must target a known rendered DOM id for the metric-card region. |
| settings.title | optional | string | Title binding or static text. |
| settings.titleCssClasses | optional | string | Optional title CSS classes. May be literal or substitution-backed. |
| settings.metric | optional | string | Metric binding or static text. |
| settings.metricCssClasses | optional | string | Optional metric CSS classes. May be literal or substitution-backed. |
| settings.meta | optional | string | Optional meta text/value binding. |
| settings.metaCssClasses | optional | string | Optional meta CSS classes. May be literal or substitution-backed. |
| settings.layout | optional | enum | Layout token from `metric-card._template_options.md`, for example `2Columns`, `3Columns`, `stacked`, or `overflow`. |
| settings.itemCssClasses | optional | string | Optional per-card/item CSS classes when report layout needs them. |
| source.sqlQuery | conditional | string | When one Metric Card region must render several cards, prefer a multi-row query with one normalized row per card. `UNION ALL` across per-metric SELECTs is the default pattern when the metrics originate from separate aggregates. |
| plugin-avatar.displayAvatar | optional | boolean | Enables avatar rendering for the metric card. Emit inside `plugin-avatar`, not inside `settings`. |
| plugin-avatar.type | conditional | enum | Avatar type when avatar rendering is enabled. Supported values include `image`, `initials`, and `icon`. |
| plugin-avatar.icon | conditional | string | Avatar icon mapping/value when `type` is `icon`. |
| plugin-avatar.initials | conditional | string | Avatar initials mapping/value when `type` is `initials`. |
| plugin-avatar.image.type | conditional | enum | Image source type when `plugin-avatar.type` is `image`. Supported values include `blobColumn`, `url`, and `urlColumn` when the owning source delivers the needed image payload. |
| plugin-avatar.image.blobColumn | conditional | string | Image blob column when `plugin-avatar.image.type` is `blobColumn`. |
| plugin-avatar.image.filenameColumn | conditional | string | Filename companion column when a blob-column avatar image is used. |
| plugin-avatar.image.mimeTypeColumn | conditional | string | MIME-type companion column when a blob-column avatar image is used. |
| plugin-avatar.image.lastUpdatedColumn | conditional | string | Last-updated companion column when a blob-column avatar image is used. |
| plugin-badge.displayBadge | optional | boolean | Enables badge rendering for the metric card. Emit inside `plugin-badge`, not inside `settings`. |
| plugin-badge.label | conditional | string | Badge label/value hook when badge rendering is enabled. |
| plugin-badge.value | conditional | string | Badge value hook when badge rendering is enabled. |
| plugin-badge.state | optional | string | Badge state/value hook when badge rendering is enabled. |
| plugin-badge.icon | optional | string | Badge icon when the design calls for one. |
| plugin-badge.style | optional | enum | Badge style token from `badge._template_options.md`, for example `subtle` or `outline`. |
| plugin-badge.shape | optional | enum | Badge shape token from `badge._template_options.md`, for example `circular`, `rounded`, or `square`. |
| plugin-badge.size | optional | enum | Badge size token from `badge._template_options.md`, for example `small`, `medium`, or `large`. |
| plugin-badge.displayLabel | optional | boolean | Optional label visibility toggle when the design needs it. |
| rowSelection.type | optional | enum | Optional row-selection mode when the Metric Card region participates in row-selection behavior. Use `focusOnly`, `singleSelection`, or `multipleSelection`. |
| rowSelection.currentSelectionPageItem | conditional | string | Current-selection page item for row-selection modes that persist selection state. Omit it for `focusOnly`. |
| rowSelection.selectAllPageItem | conditional | string | Select-all page item for `multipleSelection`. Omit it for `focusOnly` and `singleSelection`. |
| column.name | conditional | string | Required for each explicit child column emitted in report mode. |
| column.layout.sequence | conditional | number | Required for each emitted child column. |
| column.source.databaseColumn | conditional | string | Required for each emitted child column. |
| column.source.dataType | conditional | enum | Required for each emitted child column. Use one exact value from Metric Card Column Data Types. |
| column.source.primaryKey | optional | boolean | Emit `true` only on the identity column when row identity matters. |
| columns | conditional | list | Required in report mode. Generate one explicit child `column (...)` block for every delivered source projection. |

# Metric Card Column Data Types

Supported `column.source.dataType` values for Metric Card child columns:

- `bfile`
- `blob`
- `boolean`
- `clob`
- `date`
- `intervalDayToSecond`
- `intervalYearToMonth`
- `number`
- `rowid`
- `sdoGeometry`
- `timestamp`
- `timestampWithLocalTimeZone`
- `timestampWithTimeZone`
- `varchar2`

# Output Template – Full

```apexlang
region {{regionStaticId}} (
  name: {{name}}
  type: themeTemplateComponent/metricCard
  appearance {
    template: {{appearance.template}}
    templateOptions: {{appearance.templateOptions}}
  }
  componentAppearance {
    display: report
  }
  advanced {
    htmlDomId: {{advanced.htmlDomId}}
  }
  settings {
    title: {{settings.title}}
    titleCssClasses: {{settings.titleCssClasses}}
    metric: {{settings.metric}}
    metricCssClasses: {{settings.metricCssClasses}}
    meta: {{settings.meta}}
    metaCssClasses: {{settings.metaCssClasses}}
    layout: {{settings.layout}}
    itemCssClasses: {{settings.itemCssClasses}}
  }
  plugin-avatar {
    displayAvatar: {{pluginAvatar.displayAvatar}}
    type: {{pluginAvatar.type}}
    icon: {{pluginAvatar.icon}}
    initials: {{pluginAvatar.initials}}
    image: {{pluginAvatar.image}}
  }
  plugin-badge {
    displayBadge: {{pluginBadge.displayBadge}}
    label: {{pluginBadge.label}}
    value: {{pluginBadge.value}}
    state: {{pluginBadge.state}}
    icon: {{pluginBadge.icon}}
    style: {{pluginBadge.style}}
    shape: {{pluginBadge.shape}}
    size: {{pluginBadge.size}}
    displayLabel: {{pluginBadge.displayLabel}}
  }
  rowSelection {
    type: {{rowSelection.type}}
    currentSelectionPageItem: {{rowSelection.currentSelectionPageItem}}
    selectAllPageItem: {{rowSelection.selectAllPageItem}}
  }
  column {{column.name}} (
    layout {
      sequence: {{column.layout.sequence}}
    }
    source {
      databaseColumn: {{column.source.databaseColumn}}
      dataType: {{column.source.dataType}}
      primaryKey: {{column.source.primaryKey}}
    }
  )
  {{columns}}
)
```

# Conditional Rendering Rules

- Do not omit the region `appearance {}` block.
- For dashboard KPI strips, default to `appearance.template: @/blank-with-attributes` to avoid visible standard region chrome.
- Use `appearance.template: @/standard` only when the design explicitly needs a titled or landmarked visible region wrapper.
- Keep avatar or meta blocks only when the owning design requires them.
- Keep metric-card-specific layout settings in the `settings` block.
- Metric Card `settings` may include title/metric/meta bindings plus their CSS-class companions, layout tokens, and item-level CSS classes from `metric-card._template_options.md`.
- Metric Card settings/plugin value hooks do not need to have the same names as child columns. Bind them to literals, `&COLUMN_NAME.` substitutions, or other accepted placeholders as needed.
- For Metric Card avatar rendering, use `plugin-avatar.displayAvatar` for visibility and keep avatar configuration in `plugin-avatar`.
- For `plugin-avatar.type: image`, emit the nested `image: { ... }` object using the accepted source mode. The export-backed blob-column shape is `type: blobColumn` plus `blobColumn`, `filenameColumn`, `mimeTypeColumn`, and `lastUpdatedColumn`.
- For Metric Card badge rendering, use `plugin-badge.displayBadge` for visibility and keep badge configuration in `plugin-badge`.
- Omit `advanced { htmlDomId: ... }` unless external page logic must target a known rendered DOM id. Native runtime behavior can use the generated region id without requiring an explicit `htmlDomId`.
- A Metric Card region may render multiple cards from multiple result rows.
- When the page needs several independent metrics in one Metric Card region, prefer a normalized multi-row source with one row per card.
- The default SQL pattern for independent aggregates is `UNION ALL` across per-metric SELECTs that project the same aliases in the same order, for example a card title/label column plus a metric value column.
- Do not emit `settings.displayAvatar` for Metric Card; use `plugin-avatar.displayAvatar` instead.
- Do not emit `settings.displayBadge` for Metric Card; use `plugin-badge.displayBadge` instead.
- Use `rowSelection` only when the Metric Card region truly participates in row-selection behavior; otherwise omit it.
- For `rowSelection.type: focusOnly`, emit only the `type` property and no selection page items.
- For `rowSelection.type: singleSelection`, emit `currentSelectionPageItem` and omit `selectAllPageItem`.
- For `rowSelection.type: multipleSelection`, emit both `currentSelectionPageItem` and `selectAllPageItem`.
- When any `rowSelection` mode is used, keep one child column marked with `source.primaryKey: true`.
- In report mode, emit explicit child `column (...)` metadata for every delivered source projection before finals.
- Do not stop after the first compiler-satisfying child column when the source projects multiple fields.
- For child column `source.dataType`, emit only exact values from the Metric Card Column Data Types inventory; do not normalize them to uppercase SQL names or Interactive Report `STRING`/`NUMBER`/`DATE` tokens.
- When the agent cannot rely on `column-metric-card` helper coverage, fall back to the explicit multiline child-column skeleton shown in this file.

# Guardrails

- Metadata export lookup: search for `Metric Card`, `themeTemplateComponent/metricCard`, and `column-metric-card`.
- If the requested Metric Card behavior depends on external page logic targeting the region container directly, treat the chosen region template plus optional `advanced.htmlDomId` as part of the runtime contract, not as incidental styling.
