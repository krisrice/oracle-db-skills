# Select AI in Oracle AI Database

## Overview

Select AI generates, runs, and explains SQL from natural language prompts. Oracle also documents Select AI as supporting retrieval augmented generation with vector stores, natural language chat interactions with large language models, feedback loops for SQL quality, and agentic workflows.

This guide focuses on the current Select AI workflow documented for Oracle AI Database and Autonomous Database, including AI profiles, prompt actions, prompt augmentation, and metadata controls that improve SQL generation quality.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Concepts, usage guidelines, and supported platforms | Oracle Database Select AI User's Guide, **About Select AI** |
| Feature overview | Oracle Database Select AI User's Guide, **Use Select AI for Natural Language Interaction with your Database** |
| Prompt actions | Autonomous Database documentation, **Use AI Keyword to Enter Prompts** |
| Action and feedback examples | Autonomous Database documentation, **Examples of Using Select AI** |
| AI profiles and attributes | Autonomous Database documentation, **DBMS_CLOUD_AI Package** |
| AI profile metadata controls | SQL Developer Web documentation, **Create AI Profile** |
| Feedback and SQL refinement | Autonomous Database documentation, **Feedback** |
| Agent support | Oracle Database Select AI User's Guide, **Select AI Agent** |

---

## Related Skills

Read these next when the task narrows:

- `select-ai-profiles.md` for profile lifecycle and attributes
- `select-ai-actions.md` for `SELECT AI` / `DBMS_CLOUD_AI.GENERATE` actions
- `select-ai-metadata.md` for object-list scope and metadata controls
- `select-ai-feedback.md` for SQL refinement feedback
- `select-ai-rag.md` for vector-store-backed RAG
- `select-ai-synthetic-data.md` for synthetic data generation
- `select-ai-agent.md` for teams, tasks, tools, and `DBMS_CLOUD_AI_AGENT`

---

## Start with an AI Profile

Oracle documents Select AI around an AI profile that defines the provider, credentials, model, and the metadata made available for prompt translation.

`DBMS_CLOUD_AI.CREATE_PROFILE` creates a profile:

```sql
DBMS_CLOUD_AI.CREATE_PROFILE
   profile_name        IN  VARCHAR2,
   attributes          IN  CLOB      DEFAULT NULL,
   status              IN  VARCHAR2  DEFAULT NULL,
   description         IN  CLOB      DEFAULT NULL
);
```

`DBMS_CLOUD_AI.SET_PROFILE` enables a profile for the current session. The safe portable form is the minimal invocation:

```sql
BEGIN
  DBMS_CLOUD_AI.SET_PROFILE(profile_name => 'OPENAI');
END;
/
```

Current 26ai package surfaces can also expose optional `scope`, `username`, and `attributes` parameters. Treat the one-argument form as the minimal invocation, not as the only signature you may see in package metadata.

Oracle documents these profile attributes as especially important for SQL generation:

- `provider`
- `credential_name`
- `model`
- `embedding_model`
- `object_list`
- `comments`
- `conversation`

Oracle also documents that object metadata sent for SQL generation can include object names, owners, columns, and comments, and that this metadata is sent to the AI provider over HTTPS. That makes profile scope a governance decision, not just a convenience setting.

---

## Use the Supported Prompt Actions Deliberately

Oracle documents the `DBMS_CLOUD_AI.GENERATE` function for stateless use and `SELECT AI <action>` prompts for interactive use.

```sql
SELECT DBMS_CLOUD_AI.GENERATE(prompt       => 'how many customers',
                              profile_name => 'OPENAI',
                              action       => 'showsql')
FROM dual;
```

Current 26ai package surfaces expose both a four-argument `GENERATE` form and a five-argument overload that adds `params`. Prefer the shorter named-argument form for portable examples unless you specifically need structured parameters.

Oracle documents these actions:

- `runsql`
- `showsql`
- `showprompt`
- `explainsql`
- `narrate`
- `summarize`
- `translate`
- `chat`
- `feedback`
- `agent`

Oracle documents the `AI` keyword syntax as:

```sql
SELECT AI action natural_language_prompt
```

Examples from the documentation:

```sql
SELECT AI showsql how many customers exist;

SELECT AI narrate how many customers exist;

SELECT AI chat what is Autonomous AI Database;
```

Use `DBMS_CLOUD_AI.GENERATE` when `SELECT AI` is not available:

```sql
SELECT DBMS_CLOUD_AI.GENERATE(prompt       => 'what is oracle autonomous database',
                              profile_name => 'OPENAI',
                              action       => 'chat')
FROM dual;
```

Oracle documents that `showprompt` supports NL2SQL and RAG, but does not support synthetic data generation, `explainsql`, or `narrate`.

---

## Use `feedback` Only for NL2SQL Profiles

Oracle documents `feedback` as a 26ai feature for improving SQL generation accuracy. It is for NL2SQL workflows, not RAG profiles.

Documented examples include:

```sql
SELECT AI feedback for query "select ai showsql how many watch histories in total", please use sum instead of count;

SELECT AI feedback please use sum instead of count for sql_id 1v1z68ra6r9zf;

SELECT AI feedback the result is correct;
```

Oracle also documents `DBMS_CLOUD_AI.FEEDBACK` as the procedural interface and states that the first use creates a default vector index named `<profile_name>_FEEDBACK_VECINDEX`.

Do not use the `feedback` action in applications where multiple users share database sessions under a single database user that owns the AI profile.

---

## Understand Prompt Augmentation

Oracle documents prompt augmentation as a core part of Select AI.

For SQL generation, the database augments the user prompt with database metadata to mitigate hallucinations. For Select AI RAG, Oracle documents that content from the vector store is retrieved with semantic similarity search and added to the augmented prompt sent to the LLM. Oracle also documents `showprompt` so you can inspect the constructed prompt before relying on the answer path.

This is the key boundary:

- use metadata augmentation for natural language to SQL
- use vector-store augmentation when the answer depends on unstructured content

---

## Use Metadata Controls to Improve SQL Generation

Oracle has expanded the metadata controls available to Select AI profiles. Current documentation and release notes call out:

- `comments`
- `annotations`
- `constraints`

The SQL Developer Web AI profile flow also documents table metadata controls such as:

- `Object List Mode = All`
- `Object List Mode = Automated`
- `Object List Mode = Selected Tables`
- `Enforce Object List`

Oracle documents these effects:

- `comments` exposes table and column comments to the AI as context
- `constraints` exposes primary and foreign key information to help the LLM reason about joins
- `annotations` exposes extra descriptive metadata attached to objects
- `Automated` object list mode uses vector search on schema metadata so that Select AI can choose a smaller relevant table subset for each prompt

Oracle also documents that `Object List Mode` is not available with Oracle AI Database 19c, so do not assume that automated relevant-table detection exists on older deployments.

---

## Best Practices

- Keep `object_list` narrow when the use case is bounded. Oracle explicitly warns that object names, column names, and comments are sent to the AI provider.
- Use `showsql` when you want to inspect generated SQL before execution, and use `runsql` only after the profile and metadata are producing the expected SQL.
- Use `showprompt` when you need to inspect prompt augmentation, especially after enabling `comments`, `annotations`, automated object selection, or RAG.
- Enable `comments`, `constraints`, and `annotations` when business meaning or join paths are not obvious from table and column names alone.
- Use `Object List Mode = Automated` or selected tables when the schema is large and you want to reduce irrelevant metadata in the augmented prompt.
- Use `feedback` only with NL2SQL profiles and only after reviewing whether the correction is valid for all users of the profile.
- Use `DBMS_CLOUD_AI.GENERATE` with an explicit `profile_name` in Database Actions or APEX Service, where Oracle does not support the `AI` keyword or `SET_PROFILE`.
- Use Select AI RAG only when the answer depends on unstructured content and a vector store is available.

---

## Common Mistakes

### Mistake 1: Exposing Too Much Metadata

Oracle documents that object names, owners, columns, and comments are sent to the AI provider for natural language to SQL. Do not include sensitive objects or comments in the AI profile unless they are meant to be exposed.

### Mistake 2: Expecting Accurate Joins Without Relationship Metadata

Oracle's recent Select AI enhancements explicitly add `constraints` so the model can reason about primary and foreign keys. If the schema depends on joins, omitting that metadata reduces the odds of correct SQL generation.

### Mistake 3: Treating `chat` as a Substitute for SQL Inspection

Oracle documents separate actions for `showsql`, `showprompt`, `runsql`, `explainsql`, and `chat`. Use the action that matches the task instead of assuming one prompt style is correct for every workflow.

### Mistake 4: Assuming Table Selection Is Always Automatic

Oracle documents separate object-list modes. If you do not configure `object_list` or automated table selection intentionally, the prompt context may be either too broad or too narrow.

### Mistake 5: Ignoring Version-Specific Enhancements

Recent documentation adds `feedback`, agent workflows, comments, annotations, constraints, and automated metadata selection improvements. Do not assume an older deployment has the same Select AI behavior as the latest docs.

### Mistake 6: Assuming `SELECT AI` Works Everywhere

Oracle documents that the `AI` keyword is not supported in Database Actions or APEX Service. In those environments, use `DBMS_CLOUD_AI.GENERATE` and set `profile_name` explicitly instead of relying on `SET_PROFILE`.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle AI Database 19c:** Current SQL Developer Web documentation explicitly notes that `Object List Mode` is not available with Oracle AI Database 19c.
- **Oracle AI Database 26ai:** Current documentation includes AI profiles, `DBMS_CLOUD_AI`, `SELECT AI` prompt actions such as `showprompt`, `feedback`, and `agent`, RAG support, provider integrations, and Select AI Agent.
- **Current package surfaces:** Current 26ai deployments can expose additional `DBMS_CLOUD_AI` overloads, such as `GENERATE` with `params` and `SET_PROFILE` with optional scope controls. Prefer minimal named-argument examples when you want the skill to stay portable across service types.
- **2025-2026 documented enhancements:** Oracle documentation and release notes call out newer metadata controls and SQL-generation improvements including `comments`, `annotations`, `constraints`, automated relevant-table detection, and SQL feedback loops.

---

## Sources

- [Oracle Database Select AI User's Guide - Use Select AI for Natural Language Interaction with your Database](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai.html)
- [Oracle Database Select AI User's Guide - About Select AI](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai-about.html)
- [Autonomous Database documentation - Use AI Keyword to Enter Prompts](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/select-ai-keyword-prompts.html)
- [Autonomous Database documentation - Examples of Using Select AI](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/select-ai-examples.html)
- [Autonomous Database documentation - DBMS_CLOUD_AI Package](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/dbms-cloud-ai-package.html)
- [SQL Developer Web documentation - Create AI Profile](https://docs.oracle.com/en/database/oracle/sql-developer-web/sdwad/create-ai-profile.html)
- [Autonomous Database documentation - Feedback](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/select-ai-feedback.html)
- [Oracle Database Select AI User's Guide - Select AI Agent](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai-agent1.html)
