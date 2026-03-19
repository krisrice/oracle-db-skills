# Select AI Metadata Controls in Oracle AI Database

## Overview

Select AI quality depends on the metadata made available to the model. Oracle documents controls for object-list scoping, comments, annotations, constraints, case sensitivity, and data access management.

Use this file when you need to control what metadata Select AI can see and how that metadata is used for NL2SQL generation.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Metadata controls in profile creation | SQL Developer Web documentation, **Create AI Profile** |
| NL2SQL metadata attributes by release | Oracle Database Select AI Capability Matrix |
| Prompt augmentation and metadata scope | Oracle Autonomous Database documentation, **About Select AI** |
| SQL-generation improvement examples | Autonomous Database documentation, **Examples of Using Select AI** |
| Data access controls | Autonomous Database documentation, **DBMS_CLOUD_AI Package** |

---

## Object List and Object List Modes

Oracle documents `object_list` as the primary NL2SQL scoping control. SQL Developer Web also documents object-list modes:

- `None`
- `All`
- `Automated`
- `Selected Tables`

Oracle documents `Enforce Object List` as an additional control that restricts the AI to the chosen object list.

The capability matrix also documents:

- `object_list`
- `object_list_mode`
- `enforce_object_list`

Use `object_list` when you want deterministic scope. Use `object_list_mode = Automated` when you want Oracle to narrow schema context dynamically on supported releases.

---

## Comments, Annotations, and Constraints

Oracle documents these profile metadata controls:

- `comments`
- `annotations`
- `constraints`

Their purpose is distinct:

- `comments` exposes table and column comments to the AI
- `annotations` exposes extra descriptive metadata attached to objects
- `constraints` exposes primary-key, foreign-key, and related relationship information

Use these controls when schema names alone are not enough for correct SQL generation.

---

## Case Sensitivity and Values

The capability matrix documents `case_sensitive_values` as an NL2SQL profile attribute.

Use it when values must preserve case-sensitive behavior instead of being normalized loosely by prompt interpretation.

---

## Data Access Controls

Oracle documents `ENABLE_DATA_ACCESS` and `DISABLE_DATA_ACCESS` in `DBMS_CLOUD_AI`.

Use these controls when administrators need to prevent table data or vector-search documents from being sent to the LLM. Oracle notes that disabling such access also disables related response paths such as `narrate` for RAG workflows.

---

## Practical Scope Example

Oracle's examples show `object_list` as JSON entries containing owner and object name pairs:

```sql
"object_list": [{"owner": "SH", "name": "customers"},
                {"owner": "SH", "name": "countries"}]
```

Use this pattern to keep NL2SQL scope narrow instead of exposing a whole schema by default.

---

## Best Practices

- Keep `object_list` as small as the use case allows.
- Enable `constraints` when joins matter.
- Enable `comments` and `annotations` when business meaning is not obvious from identifiers alone.
- Use `Enforce Object List` when you need stronger guardrails around what the AI may consider.
- Review data access controls before enabling RAG or broad metadata augmentation.

---

## Common Mistakes

### Mistake 1: Enabling a Broad Schema Without Reviewing Metadata Exposure

Oracle documents that schema metadata can be sent to the AI provider. Broad scope is a governance choice, not only a usability choice.

### Mistake 2: Expecting Accurate Multi-Table SQL Without Relationship Metadata

If joins matter, omitting `constraints` removes documented relationship context that helps the model reason about keys.

### Mistake 3: Assuming `Object List Mode` Exists on Older Releases

SQL Developer Web documentation explicitly notes that `Object List Mode` is not available with Oracle AI Database 19c.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c (generic):** Select AI metadata controls are not part of the generic 19c baseline.
- **Oracle AI Database 19c / 19.30:** The capability matrix documents `object_list`, `comments`, `constraints`, and `case_sensitive_values`, but not newer features such as `object_list_mode` or `annotations` everywhere.
- **Oracle AI Database 23.26.1 / 26ai docs:** Current documentation includes `object_list_mode`, `annotations`, and automated relevant-object selection.

---

## Sources

- [SQL Developer Web documentation - Create AI Profile](https://docs.oracle.com/en/database/oracle/sql-developer-web/sdwad/create-ai-profile.html)
- [Oracle Database Select AI Capability Matrix](https://docs.oracle.com/en/database/oracle/oracle-database/26/saicm/index.html)
- [Oracle Database Select AI User's Guide - About Select AI](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai-about.html)
- [Autonomous Database documentation - Examples of Using Select AI](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/select-ai-examples.html)
- [Autonomous Database documentation - DBMS_CLOUD_AI Package](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/dbms-cloud-ai-package.html)
