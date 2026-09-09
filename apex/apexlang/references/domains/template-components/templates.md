# Template & Rule References — Template Components

## Authoritative Policies
- `references/policies/governance/00-governance.md`
- `references/policies/memory-bank/00-guard/ai.guard.md`
- `references/policies/memory-bank/10-global/apex.global.md`
- `references/policies/memory-bank/40-components/apex.templates.md`
- `references/policies/memory-bank/40-components/apex.items.md`
- `references/policies/memory-bank/20-data/apex.sql.md`
- `references/policies/memory-bank/20-data/apex.logic.md`
- `assets/rules-mapping.json`

## Operational References
## Templates
- `templates/template-components/avatar/avatar._common.md`
- `templates/template-components/avatar/avatar._index.md`
- `templates/template-components/avatar/avatar.report-initials.md`
- `templates/template-components/avatar/avatar.report-icon.md`
- `templates/template-components/avatar/avatar.report-image-url-column.md`
- `templates/template-components/content-row/content-row._common.md`
- `templates/template-components/content-row/content-row._index.md`
- `templates/template-components/content-row/content-row.report-minimal.md`
- `templates/template-components/content-row/content-row._index.md`
- `templates/template-components/button/button._common.md`
- `templates/template-components/button/button._index.md`
- `templates/template-components/metric-card/metric-card._common.md`
- `templates/template-components/metric-card/metric-card._index.md`
- `templates/template-components/timeline/timeline.report-sample-data.md`
- `templates/template-components/timeline/timeline._index.md`
- `templates/template-components/timeline/timeline._common.md`
- `templates/template-components/timeline/timeline._template_options.md`
- `templates/template-components/avatar/avatar._template_options.md`
- `templates/template-components/badge/badge._common.md`
- `templates/template-components/badge/badge._index.md`
- `templates/template-components/badge/badge.report-minimal.md`
- `templates/template-components/badge/badge.report-link.md`
- `templates/template-components/badge/badge._template_options.md`
- `templates/items/select-list/select-list._index.md`
- `templates/items/text-field/text-field._index.md`
- `templates/buttons/buttons._index.md`

For content-row page or region creation, load `content-row._common.md` first, then `content-row._index.md`, and default to `content-row.report-minimal.md` unless the prompt explicitly requests richer features. When badge iconography or placement is requested, use `plugin-badge.icon` and `plugin-badge.position` from the Content Row contract.

For standalone Avatar report regions, load `avatar/avatar._common.md` first, then choose exactly one initials, icon, or URL-column image scenario through `avatar/avatar._index.md`. Emit named template-component columns with uppercase snake_case identifiers plus `source.databaseColumn` and `source.dataType`; lowercase or hyphenated external identifiers fail live validation, and generic `columnName` and `show` properties are invalid for this family. Emit `settings.initials` as the direct source-column identifier, not `&COLUMN_NAME.` substitution syntax; reserve substitutions for free-text properties such as `description`. Require descriptions for meaningful Avatars and record dynamic-source evidence provenance; decorative outputs omit both the description property and its column. Build URL images in SQL only from `:APP_FILES` or `:APEX_FILES` plus a static relative path, validate literal and column-backed icons against the build-pinned Font APEX index, and keep custom CSS static, component-scoped, and documented. When access is restricted, use `mustNotBePublicUser` or an `@alias` resolving to a declared shared authorization scheme. The Avatar export declares no action templates, so do not turn its separate `link` action position into an inferred action-template scenario. Do not infer BLOB, grouping, link-action, or partial-mode structures from embedded Avatar behavior in another component.

For a standalone Badge region, load `badge._index.md`, `badge._common.md`, and `badge._template_options.md`, then select `badge.report-minimal.md` or `badge.report-link.md` according to whether navigation is requested. Do not confuse this report-mode Badge region with a nested `plugin-badge` partial configuration.

Consult `assets/rules-mapping.json` to load only the listed rules per request.

For Timeline, load `timeline.md`, `timeline/README.md`, `timeline/timeline._index.md`, and the selected Timeline example. The sample-data example is an exact report-mode shape; use a source-specific variation only after source mappings are proven. Load `timeline/timeline._template_options.md`, plus avatar or badge option inventories only when the corresponding Timeline setting is requested.
