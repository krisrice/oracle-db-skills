# Metric Card Template Options

These entries come from `create_plugin_attribute` and `create_plugin_attr_value`, not from `wwv_flow_template_options`.

Apply these values through `settings`, `plugin-appearance`, and `plugin-grouping`.
Load `../avatar/avatar._template_options.md` and `../badge/badge._template_options.md` for the shared avatar and badge substructures used by Metric Card.
Emit only exact accepted values from this inventory. Do not concatenate adjacent values into one token and do not substitute labels or implementation details.

When a row includes `values=`, pass the left-hand side of each `name=>return_value` pair.

## Metric Card (`metricCard`)
Supported: `PARTIAL:REPORT`

- `meta` | prompt=`Meta` | type=`TEXT` | scope=`COMPONENT` | required=`false` | default=`--` | group=`--` | depends=`--`
- `metaCssClasses` | prompt=`Meta CSS Classes` | type=`TEXT` | scope=`COMPONENT` | required=`false` | default=`--` | group=`--` | depends=`--`
- `metric` | prompt=`Metric` | type=`TEXT` | scope=`COMPONENT` | required=`true` | default=`--` | group=`--` | depends=`--`
- `metricCssClasses` | prompt=`Metric CSS Classes` | type=`TEXT` | scope=`COMPONENT` | required=`false` | default=`--` | group=`--` | depends=`--`
- `title` | prompt=`Title` | type=`TEXT` | scope=`COMPONENT` | required=`false` | default=`--` | group=`--` | depends=`--`
- `titleCssClasses` | prompt=`Title CSS Classes` | type=`TEXT` | scope=`COMPONENT` | required=`false` | default=`--` | group=`--` | depends=`--`
- `layout` | prompt=`Layout` | type=`SELECT LIST` | scope=`REPORT` | required=`false` | default=`--` | group=`--` | depends=`--` | values=`2Columns=>2cols, 3Columns=>3cols, 4Columns=>4cols, 5Columns=>5cols, autoWrapping=>auto, overflow=>overflow, stacked=>stacked`
- `itemCssClasses` | prompt=`Item CSS Classes` | type=`TEXT` | scope=`REPORT` | required=`false` | default=`--` | group=`Advanced` | depends=`--`
- `groupIcon` | prompt=`Group Icon` | type=`ICON` | scope=`REPORT_GROUP` | required=`false` | default=`--` | group=`Grouping` | depends=`groupTitle NOT_NULL`
- `groupTitle` | prompt=`Group Title` | type=`TEXT` | scope=`REPORT_GROUP` | required=`false` | default=`--` | group=`Grouping` | depends=`--`
