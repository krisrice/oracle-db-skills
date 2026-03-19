# Select AI Actions in Oracle AI Database

## Overview

Select AI exposes prompt actions through the `AI` keyword and through `DBMS_CLOUD_AI.GENERATE`. These actions cover NL2SQL, prompt inspection, chat, summarization, translation, feedback, and agent execution.

Use this file when you need to choose the right Select AI action and understand the exact syntax Oracle documents for prompt execution. If you need the Python object methods such as `Profile.run_sql()` or `AsyncProfile.show_sql()`, start with `select-ai-python.md` and use this file for the underlying action semantics.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| `AI` keyword syntax and action list | Autonomous Database documentation, **Use AI Keyword to Enter Prompts** |
| Action examples | Autonomous Database documentation, **Examples of Using Select AI** |
| Function-based execution | Autonomous Database documentation, **DBMS_CLOUD_AI Package** |
| Capability support by release | Oracle Database Select AI Capability Matrix |

---

## `AI` Keyword Syntax

Oracle documents the `AI` keyword syntax as:

```sql
SELECT AI action natural_language_prompt
```

Oracle explicitly documents that you cannot run PL/SQL statements, DDL statements, or DML statements using the `AI` keyword.

---

## Action Families

Oracle documents these actions for current Select AI environments:

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

Use them by outcome:

- SQL generation and execution: `runsql`, `showsql`, `explainsql`
- Prompt inspection: `showprompt`
- Natural-language output: `narrate`, `chat`, `summarize`, `translate`
- Refinement and orchestration: `feedback`, `agent`

---

## Documented `SELECT AI` Examples

Oracle's examples page includes these exact forms:

```sql
SELECT AI how many customers exist;

SELECT AI showsql how many customers exist;

SELECT AI narrate how many customers exist;

SELECT AI explainsql how many customers in San Francisco are married;

SELECT AI chat what is Autonomous AI Database;
```

Use `runsql` when you want the executed result. Use `showsql` when you want to inspect generated SQL first.

---

## `DBMS_CLOUD_AI.GENERATE`

Oracle documents `DBMS_CLOUD_AI.GENERATE` for stateless use from SQL and PL/SQL contexts:

```sql
SELECT DBMS_CLOUD_AI.GENERATE(prompt       => 'how many customers',
                              profile_name => 'OPENAI',
                              action       => 'showsql')
FROM dual;
```

Use named arguments and only pass `params` when the feature you are using documents it explicitly.

---

## `showprompt`

Oracle documents `showprompt` as a way to inspect the constructed prompt that Select AI sends to the model.

Oracle also documents functional limits:

- `showprompt` supports NL2SQL and RAG
- `showprompt` does not support synthetic data generation
- `showprompt` does not support `explainsql`
- `showprompt` does not support `narrate`

Use `showprompt` when debugging metadata augmentation, object-list selection, or RAG grounding.

---

## `summarize` and `translate`

Oracle treats summarization and translation as first-class Select AI actions and also documents `DBMS_CLOUD_AI.SUMMARIZE` and `DBMS_CLOUD_AI.TRANSLATE` as functional interfaces.

Use `summarize` for large input text or files when you want condensed output. Use `translate` when your target is language translation, not SQL generation.

---

## Best Practices

- Use `showsql` before `runsql` for NL2SQL validation.
- Use `showprompt` when debugging why the model is seeing the wrong context.
- Use `DBMS_CLOUD_AI.GENERATE` with explicit `profile_name` in environments where the `AI` keyword is not available.
- Keep `chat` separate from SQL inspection workflows. Oracle documents them as different actions for a reason.

---

## Common Mistakes

### Mistake 1: Treating `chat` as the Default Action for SQL Work

If the task is NL2SQL, Oracle already provides `showsql`, `runsql`, and `explainsql`. Use those first.

### Mistake 2: Assuming `SELECT AI` Works in Every Client Surface

Oracle documents that the `AI` keyword is not supported in Database Actions or APEX Service.

### Mistake 3: Using `showprompt` Where Oracle Does Not Support It

Oracle documents clear exclusions for `showprompt`. Do not assume it works for every action family.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c (generic):** Select AI actions are not part of the generic 19c baseline.
- **Autonomous AI Database 19c / Oracle AI Database 19.30:** Core actions such as NL2SQL and chat are documented in the capability matrix, but newer actions such as `feedback` are not universally available.
- **Oracle AI Database 23.26.1 / 26ai docs:** Current documentation includes the broadest action surface, including `showprompt`, `feedback`, and `agent`.

---

## Sources

- [Autonomous Database documentation - Use AI Keyword to Enter Prompts](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/select-ai-keyword-prompts.html)
- [Autonomous Database documentation - Examples of Using Select AI](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/select-ai-examples.html)
- [Autonomous Database documentation - DBMS_CLOUD_AI Package](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/dbms-cloud-ai-package.html)
- [Oracle Database Select AI Capability Matrix](https://docs.oracle.com/en/database/oracle/oracle-database/26/saicm/index.html)
