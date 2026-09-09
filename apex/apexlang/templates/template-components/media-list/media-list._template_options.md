# Media List Template Options

These entries come from `create_plugin_attribute` and `create_plugin_attr_value`, not from `wwv_flow_template_options`.

Apply these values through the owning component's settings plus the shared avatar and badge owner files when those substructures are enabled.
Load `../avatar/avatar._template_options.md` and `../badge/badge._template_options.md` for the shared substructures used by Media List.
Emit only exact accepted values from this inventory. Do not concatenate adjacent values into one token and do not substitute labels or implementation details.

When a row includes `values=`, pass the left-hand side of each `name=>return_value` pair.

## Media List (`mediaList`)
Supported: `PARTIAL:REPORT`

- `description` | prompt=`Description` | type=`SESSION STATE VALUE` | scope=`COMPONENT` | required=`false` | default=`--` | group=`--` | depends=`--`
- `displayAvatar` | prompt=`Display Avatar` | type=`CHECKBOX` | scope=`COMPONENT` | required=`false` | default=`N` | group=`--` | depends=`--`
- `displayBadge` | prompt=`Display Badge` | type=`CHECKBOX` | scope=`COMPONENT` | required=`false` | default=`N` | group=`--` | depends=`--`
- `title` | prompt=`Title` | type=`SESSION STATE VALUE` | scope=`COMPONENT` | required=`true` | default=`--` | group=`--` | depends=`--`
- `applyThemeColors` | prompt=`Apply Theme Colors` | type=`CHECKBOX` | scope=`REPORT` | required=`false` | default=`Y` | group=`--` | depends=`--`
- `layout` | prompt=`Layout` | type=`SELECT LIST` | scope=`REPORT` | required=`false` | default=`--` | group=`--` | depends=`--` | values=`2ColumnGrid=>t-MediaList--cols t-MediaList--2cols, 3ColumnGrid=>t-MediaList--cols t-MediaList--3cols, 4ColumnGrid=>t-MediaList--cols t-MediaList--4cols, 5ColumnGrid=>t-MediaList--cols t-MediaList--5cols, horizontalSpan=>t-MediaList--horizontal`
- `size` | prompt=`Size` | type=`SELECT LIST` | scope=`REPORT` | required=`false` | default=`--` | group=`--` | depends=`--` | values=`large=>t-MediaList--large force-fa-lg`
- `groupIcon` | prompt=`Icon` | type=`ICON` | scope=`REPORT_GROUP` | required=`false` | default=`--` | group=`Grouping` | depends=`groupTitle NOT_NULL`
- `groupTitle` | prompt=`Title` | type=`HTML` | scope=`REPORT_GROUP` | required=`false` | default=`--` | group=`Grouping` | depends=`--`
