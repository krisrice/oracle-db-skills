# Select AI Feedback in Oracle AI Database

## Overview

Oracle documents `feedback` as a Select AI capability for improving SQL generation quality. Feedback is aimed at NL2SQL correction and refinement, not RAG grounding.

Use this file when you need to understand the `feedback` action, the `DBMS_CLOUD_AI.FEEDBACK` procedure, and the operational constraints around profile-wide SQL refinement.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Feedback feature overview | Autonomous Database documentation, **Feedback** |
| Feedback examples | Autonomous Database documentation, **Examples of Using Select AI** |
| Capability support by release | Oracle Database Select AI Capability Matrix |
| Package support | Autonomous Database documentation, **DBMS_CLOUD_AI Package** |

---

## What Feedback Is For

Oracle documents feedback as a way to improve subsequent SQL generation for NL2SQL actions such as:

- `runsql`
- `showsql`
- `explainsql`

Oracle also documents that feedback is not for RAG profiles. Use it when the generated SQL is wrong or when you want to affirm that the generated SQL is correct.

---

## Documented `SELECT AI feedback` Forms

Oracle's examples page documents these feedback patterns:

```sql
SELECT AI feedback for query "select ai showsql how many watch histories in total", please use sum instead of count;

SELECT AI feedback please use sum instead of count for sql_id 1v1z68ra6r9zf;

SELECT AI feedback the result is correct;
```

These three forms correspond to:

- feedback on a specific prompt text
- feedback on a specific `SQL_ID`
- feedback on the last generated SQL

---

## `DBMS_CLOUD_AI.FEEDBACK`

Oracle documents `DBMS_CLOUD_AI.FEEDBACK` as the procedural interface for the same feature.

Use the procedure when you need feedback control from SQL or PL/SQL workflows instead of the `AI` keyword interaction style.

Oracle also documents that first use of feedback creates a default vector index named `<profile_name>_FEEDBACK_VECINDEX`.

---

## Operational Constraints

Oracle explicitly documents these constraints:

- feedback is for NL2SQL, not RAG
- to use the last generated SQL, server output configuration matters
- querying `V$MAPPED_SQL` requires the needed `SELECT` privileges
- do not use the feedback action in applications where multiple users share database sessions under one database user that owns the AI profile

That last point matters because feedback changes profile-level behavior, not only the current end user experience.

---

## Best Practices

- Use feedback only after inspecting the generated SQL or its result.
- Use prompt-text or `SQL_ID` targeting when you need deterministic correction scope.
- Treat positive feedback as a deliberate signal, not as routine click-through.
- Keep separate profiles for distinct business domains so feedback stays semantically coherent.

---

## Common Mistakes

### Mistake 1: Using Feedback on a RAG Profile

Oracle documents feedback for NL2SQL refinement. It is not the correction mechanism for RAG grounding.

### Mistake 2: Sharing One Feedback-Enabled Profile Across Unrelated Users

Oracle explicitly warns against using feedback in shared-session application patterns.

### Mistake 3: Assuming Feedback Is Session-Local

Oracle documents a profile-level feedback vector index. Treat feedback as a maintained asset, not a temporary session note.

---

## Oracle Version Notes (19c vs 26ai)

- **Autonomous AI Database 19c:** The capability matrix marks `feedback` as unavailable.
- **Oracle AI Database 19.30:** The capability matrix marks `feedback` as available.
- **Oracle AI Database 23.26.1 / 26ai docs:** Current documentation includes `feedback`, examples, and the `DBMS_CLOUD_AI.FEEDBACK` procedure.

---

## Sources

- [Autonomous Database documentation - Feedback](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/select-ai-feedback.html)
- [Autonomous Database documentation - Examples of Using Select AI](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/select-ai-examples.html)
- [Oracle Database Select AI Capability Matrix](https://docs.oracle.com/en/database/oracle/oracle-database/26/saicm/index.html)
- [Autonomous Database documentation - DBMS_CLOUD_AI Package](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/dbms-cloud-ai-package.html)
