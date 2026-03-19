# Select AI RAG in Oracle AI Database

## Overview

Select AI with Retrieval Augmented Generation (RAG) augments prompts with content retrieved from a vector store by semantic similarity search. Oracle documents this as a way to reduce hallucinations and ground responses in enterprise content.

Use this file when you need to configure Select AI for vector-store-backed answers instead of pure NL2SQL.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| RAG concepts and workflow | Oracle Database Select AI User's Guide, **Select AI with Retrieval Augmented Generation (RAG)** |
| RAG setup examples | Autonomous Database documentation, **Examples of Using Select AI** |
| RAG capability and attributes | Oracle Database Select AI Capability Matrix |
| Vector-index procedures in Select AI | Autonomous Database documentation, **DBMS_CLOUD_AI Package** |

---

## RAG Workflow

Oracle documents the RAG flow at a high level as:

1. the user submits a prompt
2. Select AI generates embeddings for the prompt using the configured embedding model
3. a vector index retrieves semantically similar content
4. the retrieved content is added to the augmented prompt
5. the LLM produces a grounded response

Oracle documents `narrate` as the main action used in the end-to-end RAG examples.

---

## RAG Profile Attributes

The capability matrix documents these RAG-related profile attributes:

- `embedding_model`
- `vector_index_name`
- `enable_sources`

Use `embedding_model` for prompt and chunk embeddings, `vector_index_name` to bind the profile to the prepared vector index, and `enable_sources` when you want source visibility in the response.

---

## Create and Manage Vector Indexes for Select AI

Oracle documents `DBMS_CLOUD_AI` procedures to create and manage vector indexes for Select AI:

- `CREATE_VECTOR_INDEX`
- `UPDATE_VECTOR_INDEX`
- `ENABLE_VECTOR_INDEX`
- `DISABLE_VECTOR_INDEX`
- `DROP_VECTOR_INDEX`
- `GRANT_VECTOR_INDEX_ACCESS`
- `REVOKE_VECTOR_INDEX_ACCESS`

The capability matrix documents vector-index attributes such as:

- `chunk_overlap`
- `chunk_size`
- `location`
- `match_limit`
- `object_storage_credential_name`
- `pipeline_name`
- `profile_name`
- `refresh_rate`
- `similarity_threshold`
- `vector_db_provider`
- `vector_dimension`
- `vector_distance_metric`
- `vector_table_name`

---

## In-Database Transformer Models

Oracle documents that Select AI RAG can use imported ONNX transformer models inside the database.

Oracle's examples page shows `embedding_model` using the `database:` prefix:

```sql
BEGIN
  DBMS_CLOUD_AI.CREATE_PROFILE(
     profile_name => 'OCI_GENAI',
     attributes   => '{"provider": "oci",
                       "model": "meta.llama-3.3-70b-instruct",
                       "credential_name": "GENAI_CRED",
                       "vector_index_name": "MY_INDEX",
                       "embedding_model": "database: MY_ONNX_MODEL"}'
  );
END;
/
```

Use this path when you want in-database embedding generation instead of a remote embedding provider.

---

## Sources in Responses

Oracle documents source-aware output for RAG. When source visibility is enabled, the response can include the documents used for grounding.

Use this when reviewability matters more than terse output.

---

## Best Practices

- Use RAG when the answer depends on unstructured content, not when the answer is already in relational tables.
- Keep the AI profile and vector index aligned on embedding model and dimensions.
- Enable sources when you need reviewable answers.
- Treat vector-index lifecycle procedures as part of the application pipeline, not as one-time setup.

---

## Common Mistakes

### Mistake 1: Using RAG and NL2SQL Interchangeably

Oracle documents RAG as a vector-store retrieval path. It is not the same mechanism as NL2SQL prompt augmentation over relational metadata.

### Mistake 2: Forgetting That RAG Support Is Version-Dependent

The capability matrix does not show RAG support everywhere that basic Select AI exists.

### Mistake 3: Configuring a Profile Without a Matching Vector Index

`vector_index_name` and `embedding_model` are part of the profile contract. Configure them deliberately.

---

## Oracle Version Notes (19c vs 26ai)

- **Autonomous AI Database 19c:** The capability matrix marks RAG as unavailable.
- **Oracle AI Database 19.30:** The capability matrix marks RAG as unavailable.
- **Oracle AI Database 23.26.1 / 26ai docs:** Current documentation includes RAG, vector-index management, `enable_sources`, and in-database transformer model support.

---

## Sources

- [Oracle Database Select AI User's Guide - Select AI with Retrieval Augmented Generation (RAG)](https://docs.oracle.com/en/database/oracle/oracle-database/26/selai/select-ai-retrieval-augmented-generation.html)
- [Autonomous Database documentation - Examples of Using Select AI](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/select-ai-examples.html)
- [Oracle Database Select AI Capability Matrix](https://docs.oracle.com/en/database/oracle/oracle-database/26/saicm/index.html)
- [Autonomous Database documentation - DBMS_CLOUD_AI Package](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/dbms-cloud-ai-package.html)
