# Select AI Security in Oracle AI Database

## Overview

Select AI security is not one switch. Oracle documents separate controls for privilege grants, metadata scope, data exposure to LLMs, network access to providers, private model connectivity, and sidecar-style federation.

Use this file when the question is about securing Select AI usage, reviewing what can be sent to the LLM, or deciding which Oracle control applies to NL2SQL, `narrate`, RAG, synthetic data generation, or AI proxy database patterns.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Select AI prerequisites and privilege grants | Oracle Autonomous Database documentation, **Manage AI Profiles** |
| Agent package privilege coverage | Oracle documentation, **Select AI for Python** |
| Metadata augmentation and NL2SQL usage guidelines | Oracle Autonomous Database documentation, **Generate SQL from Natural Language Prompts Using Select AI** |
| Data-access switches for `narrate`, RAG, and synthetic data | Oracle Autonomous Database documentation, **DBMS_CLOUD_AI Package** |
| Private model connectivity | Oracle Autonomous Database documentation, **Private Endpoint Access for Select AI Models** |
| AI proxy database and sidecar architecture | Oracle Database Select AI User's Guide, **Use an AI Proxy Database for Select AI NL2SQL** |
| Capability coverage by release | Oracle Database Select AI Capability Matrix |

---

## Security Boundaries by Workflow

Oracle documents different data-exposure behavior for different Select AI workflows.

For NL2SQL prompt augmentation:

- the database augments the prompt with schema metadata only
- this metadata may include schema definitions, table and column comments, and data-dictionary or catalog content
- for SQL generation, Select AI does not provide table or view contents to the LLM

Oracle separately documents that `narrate` can provide query results to the LLM, and that RAG can provide retrieved vector-store content to the LLM.

This distinction matters:

- securing NL2SQL starts with metadata scope
- securing `narrate`, RAG, and synthetic data starts with both metadata scope and data-access controls

---

## Prerequisites and Privilege Controls

Oracle documents these baseline prerequisites for Select AI:

- `EXECUTE` on `DBMS_CLOUD_AI`
- network ACL access for the AI provider endpoint
- provider credentials

For RAG-oriented Select AI, Oracle also documents:

- `EXECUTE` on `DBMS_CLOUD_PIPELINE`
- sufficient quota in a tablespace for vector-index storage

For Select AI Agent, Oracle separately documents:

- `EXECUTE` on `DBMS_CLOUD_AI_AGENT`

Representative documented privilege examples:

```sql
GRANT EXECUTE ON DBMS_CLOUD_AI TO ADB_USER;

GRANT EXECUTE ON DBMS_CLOUD_PIPELINE TO ADB_USER;

GRANT EXECUTE ON DBMS_CLOUD_AI_AGENT TO ADB_USER;
```

Representative documented network ACL example:

```sql
BEGIN
  DBMS_NETWORK_ACL_ADMIN.APPEND_HOST_ACE(
       host => 'api.openai.com',
       ace  => xs$ace_type(privilege_list => xs$name_list('http'),
                           principal_name => 'ADB_USER',
                           principal_type => xs_acl.ptype_db)
 );
END;
/
```

Use package privileges, network ACLs, and provider credentials as separate review items. They control different parts of the attack surface.

---

## Metadata Scope Controls

Oracle documents `object_list`, `object_list_mode`, and `enforce_object_list` as profile controls for narrowing what metadata Select AI may use.

Use these controls to restrict the metadata sent for NL2SQL generation:

- `object_list` to specify schemas, tables, or views
- `enforce_object_list` to restrict SQL generation to the chosen objects
- `object_list_mode = automated` on supported releases to limit metadata to relevant tables dynamically

Oracle also documents `comments`, `annotations`, and `constraints` as metadata sources that may be added to the prompt.

Use `select-ai-metadata.md` for the detailed semantics of those attributes. Use this file when the question is whether a broader metadata scope is acceptable from a governance standpoint.

---

## Data Access Controls for Results and Documents

Oracle documents `ENABLE_DATA_ACCESS` and `DISABLE_DATA_ACCESS` as administrator-only procedures.

These procedures control whether Select AI may send actual database or vector-store content to the LLM for:

- `narrate`
- Retrieval Augmented Generation (RAG)
- synthetic data generation

Documented syntax:

```sql
BEGIN
  DBMS_CLOUD_AI.DISABLE_DATA_ACCESS();
END;
/

BEGIN
  DBMS_CLOUD_AI.ENABLE_DATA_ACCESS();
END;
/
```

Use these procedures when administrators need a hard control over whether result rows or retrieved documents can leave the database boundary for LLM processing.

---

## Private Connectivity to Models

Oracle documents private endpoint support for Select AI model access through Ollama or Llama.cpp deployed behind a private endpoint inside a VCN.

Oracle's documented architecture uses:

- a jump server in a public subnet for controlled SSH access
- private subnets for the Autonomous AI Database and AI model servers
- security lists and controlled routing

Use this pattern when the requirement is to keep model access and prompt traffic off the public internet.

Private endpoint guidance is about network isolation. It does not replace object-list scope, data-access switches, or ordinary database privilege review.

---

## AI Proxy Database and Sidecar Security

Oracle documents Select AI sidecar architecture as an AI proxy database pattern.

In this pattern:

- Select AI runs in Autonomous AI Database or Oracle AI Database
- remote data remains in its source system
- Select AI reads metadata from local views, external tables, or federated tables that expose remote objects
- the AI proxy database coordinates federated execution across Oracle and non-Oracle systems

Oracle explicitly documents that this approach relies on metadata, not physical data movement, and that external systems remain authoritative for their data and enforce their own security controls.

Oracle also documents that Select AI uses existing database roles, security mechanisms, and policies in place to protect data across linked databases.

Use this pattern when you want to extend NL2SQL to external data without duplicating data into the Select AI database.

---

## Routing to Core Oracle Security Skills

Select AI-specific controls do not replace normal Oracle security controls. Use the existing security skills when the question goes deeper into these areas:

- `skills/security/privilege-management.md` for least privilege and role design
- `skills/security/row-level-security.md` for VPD or FGAC
- `skills/security/auditing.md` for Unified Auditing and FGA
- `skills/security/network-security.md` for ACLs, TLS, and network hardening

Select AI sits on top of those controls. It does not bypass them.

---

## Best Practices

- Keep `object_list` narrow and review it as a security boundary, not only a usability setting.
- Distinguish metadata exposure from data exposure. Oracle documents them separately.
- Disable data access when `narrate`, RAG, or synthetic data should not send actual content to the LLM.
- Treat private endpoint access as a network-isolation control, not as a substitute for least privilege.
- In sidecar deployments, review both the AI proxy database controls and the source-system controls.

---

## Common Mistakes

### Mistake 1: Assuming NL2SQL Sends Table Data by Default

Oracle documents that SQL-generation prompt augmentation uses schema metadata only, not table or view contents.

### Mistake 2: Forgetting That `narrate`, RAG, and Synthetic Data Have a Different Exposure Model

Oracle documents separate data-access procedures for these features because they can send actual data or retrieved content to the LLM.

### Mistake 3: Treating `object_list` as Only a Convenience Feature

Oracle documents `object_list` and `enforce_object_list` as concrete scope controls. They are part of the security posture.

### Mistake 4: Treating AI Proxy Databases as Data Copies

Oracle documents the sidecar pattern as metadata-driven federation where source systems remain authoritative and enforce their own controls.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c (generic):** Select AI security controls are not part of the generic 19c baseline.
- **Autonomous AI Database 19c / Oracle AI Database 19.30:** Oracle documents core Select AI prerequisites, package privileges, ACL setup, and metadata-scoping controls, but newer controls such as automated object-list mode are not part of every earlier release line.
- **Oracle AI Database 23.26.1 / 26ai docs:** Current documentation includes the broadest Select AI security-related surface, including private endpoint guidance, AI proxy database guidance, annotations, and automated relevant-object selection.

---

## Sources

- [Oracle Autonomous Database documentation - Manage AI Profiles](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/manage-ai-profiles.html)
- [Oracle documentation - Select AI for Python Release 1.2.2](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/pysai/G41793_04.pdf)
- [Oracle Autonomous Database documentation - Generate SQL from Natural Language Prompts Using Select AI](https://docs.oracle.com/en-us/iaas/autonomous-database/doc/use-select-ai-generate-sql-natural-language-prompts.html)
- [Oracle Autonomous Database documentation - DBMS_CLOUD_AI Package](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/dbms-cloud-ai-package.html)
- [Oracle Autonomous Database documentation - Private Endpoint Access for Select AI Models](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/private-endpoint-access-select-ai-models.html)
- [Oracle Database Select AI User's Guide - Use an AI Proxy Database for Select AI NL2SQL](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai-sidecar-databases.html)
- [Oracle Database Select AI Capability Matrix](https://docs.oracle.com/en/database/oracle/oracle-database/26/saicm/index.html)
