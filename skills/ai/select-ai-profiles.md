# Select AI Profiles in Oracle AI Database

## Overview

Select AI is configured through AI profiles. A profile binds together the AI provider, credentials, model selection, conversation settings, NL2SQL metadata scope, and optional RAG vector-index settings.

Use this file when you need to create, activate, inspect, or update Select AI profiles. Use the other Select AI skills for prompt actions, metadata controls, feedback, RAG, synthetic data, or agents. If you need the Python wrappers `select_ai.Profile` or `ProfileAttributes`, start with `select-ai-python.md` and return here for profile semantics.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Profile concepts and provider support | Oracle Database Select AI User's Guide, **About Select AI** |
| Capability and attribute support by release | Oracle Database Select AI Capability Matrix |
| `DBMS_CLOUD_AI` profile procedures | Autonomous Database documentation, **DBMS_CLOUD_AI Package** |
| Create profile UI and object-list settings | SQL Developer Web documentation, **Create AI Profile** |
| End-to-end profile examples | Autonomous Database documentation, **Examples of Using Select AI** |

---

## Create and Activate a Profile

Oracle documents `DBMS_CLOUD_AI.CREATE_PROFILE` as the starting point for profile creation:

```sql
DBMS_CLOUD_AI.CREATE_PROFILE
   profile_name        IN  VARCHAR2,
   attributes          IN  CLOB      DEFAULT NULL,
   status              IN  VARCHAR2  DEFAULT NULL,
   description         IN  CLOB      DEFAULT NULL
);
```

Oracle documents `DBMS_CLOUD_AI.SET_PROFILE` as the session-level activation step:

```sql
BEGIN
  DBMS_CLOUD_AI.SET_PROFILE(profile_name => 'GENAI');
END;
/
```

Use `DBMS_CLOUD_AI.GET_PROFILE` to inspect the active profile for the current session.

---

## Attribute Groups

The capability matrix groups profile attributes into stable categories.

### AI Provider Attributes

Representative provider attributes documented in the capability matrix include:

- `provider`
- `credential_name`
- `provider_endpoint`
- `region`
- `azure_deployment_name`
- `azure_embedding_deployment_name`
- `azure_resource_name`
- `oci_apiformat`
- `oci_compartment_id`
- `oci_endpoint_id`
- `oci_runtimetype`

### General and LLM Attributes

- `conversation`
- `model`
- `max_tokens`
- `seed`
- `stop_tokens`
- `temperature`

### NL2SQL Attributes

- `object_list`
- `object_list_mode`
- `enforce_object_list`
- `comments`
- `annotations`
- `constraints`
- `case_sensitive_values`

### RAG Attributes

- `embedding_model`
- `vector_index_name`
- `enable_sources`

### Translation Attributes

- `source_language`
- `target_language`

Use the capability matrix when you need the exact support status of an individual attribute on Autonomous AI Database 19c, Oracle AI Database 19.30, Oracle AI Database 23.26.1, or later environments.

---

## Profile Lifecycle Procedures

Oracle documents these profile-management procedures and functions in `DBMS_CLOUD_AI`:

- `CREATE_PROFILE`
- `GET_PROFILE`
- `SET_PROFILE`
- `SET_ATTRIBUTE`
- `SET_ATTRIBUTES`
- `ENABLE_PROFILE`
- `DISABLE_PROFILE`
- `DROP_PROFILE`
- `GRANT_PROFILE_ACCESS`
- `REVOKE_PROFILE_ACCESS`
- `ENABLE_DATA_ACCESS`
- `DISABLE_DATA_ACCESS`

Use the lifecycle procedures instead of recreating profiles for every change. Keep the profile name stable and update attributes deliberately.

---

## Example Attribute Payload

Oracle's examples page shows profile creation with a JSON attribute payload:

```sql
BEGIN
  DBMS_CLOUD_AI.CREATE_PROFILE(
      profile_name =>'GENAI',
      attributes   =>'{"provider": "oci",
        "credential_name": "GENAI_CRED",
        "object_list": [{"owner": "SH", "name": "customers"},
                        {"owner": "SH", "name": "countries"},
                        {"owner": "SH", "name": "supplementary_demographics"},
                        {"owner": "SH", "name": "profits"},
                        {"owner": "SH", "name": "promotions"},
                        {"owner": "SH", "name": "products"}]
       }');
END;
/
```

This example is intentionally small but shows the two most common profile concerns:

- selecting a provider and credential path
- constraining NL2SQL scope through `object_list`

---

## Best Practices

- Keep one profile per clear use case instead of one profile that mixes unrelated schemas, models, and RAG settings.
- Use `SET_ATTRIBUTE` or `SET_ATTRIBUTES` for controlled updates instead of dropping and recreating a profile unnecessarily.
- Keep provider credentials and profile scope separate in your design review. They are different risk surfaces.
- Use the capability matrix before depending on attributes such as `object_list_mode`, `annotations`, `embedding_model`, or `vector_index_name`.

---

## Common Mistakes

### Mistake 1: Treating Profile Creation as Provider Setup Only

Oracle profiles are not only provider configuration. They also define metadata scope, NL2SQL behavior, conversations, and optional RAG settings.

### Mistake 2: Assuming All Attributes Exist on All Releases

The capability matrix explicitly varies support by release and service type. Do not assume `object_list_mode`, `annotations`, or RAG attributes exist everywhere.

### Mistake 3: Activating a Profile Without Reviewing Its Metadata Scope

Oracle documents that schema metadata can be sent to the AI provider. Profile activation is therefore a governance decision, not only a session convenience.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c (generic):** Select AI is not a standard 19c on-premises baseline feature.
- **Autonomous AI Database 19c / Oracle AI Database 19.30:** The capability matrix documents support for core profile management, but not every newer NL2SQL or RAG attribute.
- **Oracle AI Database 23.26.1 / 26ai docs:** Current documentation includes the broadest profile surface, including newer attributes for metadata controls and RAG.

---

## Sources

- [Oracle Database Select AI User's Guide - About Select AI](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai-about.html)
- [Oracle Database Select AI Capability Matrix](https://docs.oracle.com/en/database/oracle/oracle-database/26/saicm/index.html)
- [Autonomous Database documentation - DBMS_CLOUD_AI Package](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/dbms-cloud-ai-package.html)
- [SQL Developer Web documentation - Create AI Profile](https://docs.oracle.com/en/database/oracle/sql-developer-web/sdwad/create-ai-profile.html)
- [Autonomous Database documentation - Examples of Using Select AI](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/select-ai-examples.html)
