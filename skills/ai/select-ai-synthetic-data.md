# Select AI Synthetic Data Generation in Oracle AI Database

## Overview

Oracle documents synthetic data generation as a Select AI capability for populating schemas or metadata clones without using sensitive source data. The feature is exposed through `DBMS_CLOUD_AI.GENERATE_SYNTHETIC_DATA`.

Use this file when you need schema-conforming synthetic data for development, testing, demos, or training.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Synthetic data overview | Oracle Database Select AI User's Guide, **Synthetic Data Generation** |
| Function syntax | Autonomous Database documentation, **DBMS_CLOUD_AI Package** |
| End-to-end examples | Autonomous Database documentation, **Examples of Using Select AI** |
| Monitoring status tables | Autonomous Database documentation, **Synthetic Data Generation** |
| Capability support by release | Oracle Database Select AI Capability Matrix |

---

## Single-Table and Multi-Table Syntax

Oracle documents this single-table form:

```sql
DBMS_CLOUD_AI.GENERATE_SYNTHETIC_DATA(
  profile_name        IN  VARCHAR2,
  object_name         IN  DBMS_ID,
  owner_name          IN  DBMS_ID,
  record_count        IN  NUMBER,
  user_prompt         IN  CLOB DEFAULT NULL,
  params              IN  CLOB DEFAULT NULL
);
```

Oracle documents this multi-table form:

```sql
DBMS_CLOUD_AI.GENERATE_SYNTHETIC_DATA(
  profile_name        IN  VARCHAR2,
  object_list         IN  CLOB,
  params              IN  CLOB DEFAULT NULL
);
```

---

## Documented Example

Oracle documents this multi-table example:

```sql
BEGIN
    DBMS_CLOUD_AI.GENERATE_SYNTHETIC_DATA(
        profile_name => 'GENAI',
        object_list => '[{"owner": "ADB_USER", "name": "Director","record_count":5},
                         {"owner": "ADB_USER", "name": "Movie_Actor","record_count":5},
                         {"owner": "ADB_USER", "name": "Actor","record_count":10},
                         {"owner": "ADB_USER", "name": "Movie","record_count":5,"user_prompt":"all movies are released in 2009"}]'
    );
END;
/
```

Oracle also documents `params` such as `sample_rows` and `table_statistics` for improving realism.

---

## Monitoring and Troubleshooting

Oracle documents that large synthetic data jobs are split into chunks and tracked in a status table named:

```text
SYNTHETIC_DATA$<operation_id>_STATUS
```

Use this status table to inspect chunk progress, rows loaded, and per-table aggregation after generation starts.

---

## Benefits Oracle Documents

Oracle documents synthetic data generation for:

- populating metadata clones
- starting projects when real data is unavailable
- validating user experiences with realistic data
- supporting AI and machine learning work without exposing sensitive data

Use it when you need shape and variety, not production truth.

---

## Best Practices

- Use a dedicated Select AI profile for synthetic data generation instead of sharing the same profile with NL2SQL or RAG workloads.
- Start with a small `record_count` and review output before scaling up.
- Use `user_prompt` only for targeted shaping, not as a substitute for schema design.
- Review `table_statistics` options when realism matters more than random variety.

---

## Common Mistakes

### Mistake 1: Treating Synthetic Data as a Generic `chat` or `narrate` Workflow

Oracle documents synthetic data generation as a separate `DBMS_CLOUD_AI` capability with its own function and monitoring pattern.

### Mistake 2: Ignoring the Status Table for Large Jobs

Oracle explicitly documents chunk-level status tracking. Use it instead of guessing whether the generation completed correctly.

### Mistake 3: Assuming Support Is Uniform Across Releases

The capability matrix shows that synthetic data generation support differs by service type and release line.

---

## Oracle Version Notes (19c vs 26ai)

- **Autonomous AI Database 19c:** The capability matrix marks synthetic data generation as supported.
- **Oracle AI Database 19.30:** The capability matrix marks synthetic data generation as supported.
- **Oracle AI Database 23.7+:** The capability matrix marks synthetic data generation as unavailable.
- **Oracle AI Database 23.26.1 / 26ai docs:** Current documentation includes function syntax, examples, and monitoring guidance.

---

## Sources

- [Oracle Database Select AI User's Guide - Select AI](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai.html)
- [Autonomous Database documentation - Synthetic Data Generation](https://docs.oracle.com/en-us/iaas/autonomous-database-shared/doc/select-ai-synthetic-data-generation.html)
- [Autonomous Database documentation - DBMS_CLOUD_AI Package](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/dbms-cloud-ai-package.html)
- [Autonomous Database documentation - Examples of Using Select AI](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/select-ai-examples.html)
- [Oracle Database Select AI Capability Matrix](https://docs.oracle.com/en/database/oracle/oracle-database/26/saicm/index.html)
