# Vector Embeddings in Oracle AI Database

## Overview

Oracle AI Vector Search supports embedding generation both inside the database and through third-party REST providers. Oracle documents chunking, embedding generation, ONNX model import, and relational storage as part of the vector workflow.

Use this file when you need to generate embeddings, choose between database and remote providers, or build chunking-plus-embedding pipelines.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Embedding workflow topic map | Oracle AI Vector Search User's Guide, **Your Vector Documentation Map to GenAI Prompts** |
| Third-party embedding access | Oracle AI Vector Search User's Guide, **Access Third-Party Models for Vector Generation Leveraging Third-Party REST APIs** |
| `DBMS_VECTOR_CHAIN` overview | Oracle AI Vector Search User's Guide, **DBMS_VECTOR_CHAIN** |
| Package reference details | Oracle Database PL/SQL Packages and Types Reference, **DBMS_VECTOR_CHAIN** |

---

## Database and Third-Party Providers

Oracle documents two main provider patterns for embeddings:

- in-database ONNX embedding models
- third-party REST providers such as Cohere, Google AI, Hugging Face, Generative AI, OpenAI-compatible providers, Vertex AI, and local Ollama endpoints

Oracle documents provider selection through JSON parameters passed to `DBMS_VECTOR` or `DBMS_VECTOR_CHAIN` utility functions.

For the database provider, Oracle documents:

```json
{
  "provider" : "database",
  "model"    : "<in-database ONNX embedding model name>"
}
```

---

## Chunking and Embedding Functions

Oracle documents the chainable utility functions in `DBMS_VECTOR_CHAIN`:

- `UTL_TO_TEXT`
- `UTL_TO_CHUNKS`
- `UTL_TO_EMBEDDING`
- `UTL_TO_EMBEDDINGS`
- `UTL_TO_SUMMARY`
- `UTL_TO_RERANK`
- `UTL_TO_GENERATE_TEXT`

Use `UTL_TO_TEXT` and `UTL_TO_CHUNKS` before embedding when your source is a document rather than already normalized text.

---

## Example: Text to Vector

Oracle documents this `DBMS_VECTOR_CHAIN.UTL_TO_EMBEDDING` signature:

```sql
DBMS_VECTOR_CHAIN.UTL_TO_EMBEDDING (
    DATA           IN CLOB,
    PARAMS         IN JSON default NULL
) return VECTOR;
```

Oracle also documents the simple usage pattern:

```sql
select dbms_vector_chain.utl_to_embedding('Hello world', json(:params)) from dual;
```

Use this form when you want an explicit SQL-level embedding call instead of a larger pipeline.

---

## Chunking Preferences for Hybrid Pipelines

Oracle documents `DBMS_VECTOR_CHAIN.CREATE_PREFERENCE` for reusable vectorizer preferences.

The package reference shows a vectorizer example that combines chunking and hybrid-index preferences:

```sql
begin
  DBMS_VECTOR_CHAIN.CREATE_PREFERENCE(
    'my_vec_spec',
     DBMS_VECTOR_CHAIN.VECTORIZER,
     json('{ "vector_idxtype" :  "hnsw",
             "model"          :  "my_doc_model",
             "by"             :  "words",
             "max"            :  100,
             "overlap"        :  10,
             "language"       :  "english" }'));
end;
/
```

Use preferences when you need repeatable chunking and vectorization behavior across jobs or indexes.

---

## Best Practices

- Keep chunking and embedding settings stable within one corpus.
- Use an in-database ONNX model when you need database-local embedding generation.
- Use third-party providers only after reviewing their endpoint, credential, and cost model.
- Preserve source text and document metadata together with the embedding output.

---

## Common Mistakes

### Mistake 1: Mixing Providers Without a Deliberate Compatibility Plan

Oracle documents many provider options, but compatibility of embeddings is still a model-space concern.

### Mistake 2: Embedding Large Documents Without Chunking

Oracle documents chunking and text extraction utilities because document pipelines usually need them before embedding.

### Mistake 3: Treating Text Generation APIs as Embedding APIs

`UTL_TO_GENERATE_TEXT` and `UTL_TO_EMBEDDING` are different operations. Use the one that matches the workflow.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c:** Oracle AI Vector Search embedding features are not available.
- **Oracle Database 23ai:** Oracle introduces the vector workflow, including ONNX model import and embedding utilities.
- **Oracle AI Database 26ai:** Current documentation includes `DBMS_VECTOR_CHAIN`, third-party embedding access, and end-to-end chunking and embedding pipelines.

---

## Sources

- [Oracle AI Vector Search User's Guide - Your Vector Documentation Map to GenAI Prompts](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/your-vector-documentation-map-genai-prompts.html)
- [Oracle AI Vector Search User's Guide - Access Third-Party Models for Vector Generation Leveraging Third-Party REST APIs](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/access-third-party-models-vector-generation-leveraging-third-party-rest-apis.html)
- [Oracle AI Vector Search User's Guide - DBMS_VECTOR_CHAIN](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/dbms_vector_chain-vecse.html)
- [Oracle Database PL/SQL Packages and Types Reference - DBMS_VECTOR_CHAIN](https://docs.oracle.com/en/database/oracle/oracle-database/26/arpls/dbms_vector_chain1.html)
