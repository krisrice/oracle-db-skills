# Vector Embeddings in Oracle AI Database

## Overview

Oracle AI Vector Search supports embedding generation both inside the database and through third-party REST providers. Oracle documents chunking, embedding generation, ONNX model import, custom vocabulary and language data, and relational storage as part of the vector workflow.

Use this file when you need to generate embeddings, choose between SQL and PL/SQL vector-generation surfaces, choose between database and remote providers, or build chunking-plus-embedding pipelines.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Embedding workflow topic map | Oracle AI Vector Search User's Guide, **Your Vector Documentation Map to GenAI Prompts** |
| Vector-generation overview and stage model | Oracle AI Vector Search User's Guide, **Generate Vector Embeddings** |
| SQL and PL/SQL generation surfaces | Oracle AI Vector Search User's Guide, **About SQL Functions to Generate Embeddings** and **About PL/SQL Packages to Generate Embeddings** |
| Chunking utility details | Oracle AI Vector Search User's Guide, **UTL_TO_CHUNKS** |
| Third-party embedding access | Oracle AI Vector Search User's Guide, **Access Third-Party Models for Vector Generation Leveraging Third-Party REST APIs** |
| ONNX import and in-database embeddings | Oracle AI Vector Search User's Guide, **Import ONNX Models into Oracle AI Database End-to-End Example** |
| Custom vocabulary and language data | Oracle AI Vector Search User's Guide, **Create and Use Custom Vocabulary** and **Create and Use Custom Language Data** |
| `DBMS_VECTOR_CHAIN` overview | Oracle AI Vector Search User's Guide, **DBMS_VECTOR_CHAIN** |
| `CREATE_PREFERENCE` for reusable vectorizer settings | Oracle AI Vector Search User's Guide, **CREATE_PREFERENCE** |
| Package reference details | Oracle Database PL/SQL Packages and Types Reference, **DBMS_VECTOR_CHAIN** |

---

## Choose the Execution Surface

Oracle documents two major execution surfaces for vector generation:

- SQL functions such as `VECTOR_CHUNKS` and `VECTOR_EMBEDDING`
- PL/SQL packages such as `DBMS_VECTOR` and `DBMS_VECTOR_CHAIN`

Oracle positions SQL functions as the path for on-the-fly or parallel chunking and embedding operations inside the database. Oracle positions PL/SQL packages as the path for chunking, embedding, text generation, text processing, and similarity-search workflows that can also reach external REST providers.

Oracle also documents an internal package split:

- `DBMS_VECTOR` is the lightweight package for common vector operations
- `DBMS_VECTOR_CHAIN` is the advanced package with text processing, `UTL_TO_TEXT`, `UTL_TO_SUMMARY`, and chunker helper procedures

Use SQL functions when the workflow is centered on relational queries and scoring. Use `DBMS_VECTOR` for lighter-weight embedding workflows. Use `DBMS_VECTOR_CHAIN` when the workflow needs chainable transformation steps, text extraction, chunker helper procedures, or provider JSON payloads.

---

## Stage Model for Embedding Pipelines

Oracle documents vector generation as a staged transformation process:

1. optional file-to-text conversion
2. optional chunking
3. embedding generation
4. relational storage and later indexing or retrieval

This stage model is why Oracle exposes multiple utilities instead of only one embedding call.

Typical mappings:

- file to text: `UTL_TO_TEXT`
- text to chunks: `UTL_TO_CHUNKS`
- one text string to one vector: `UTL_TO_EMBEDDING`
- many chunks to many vectors: `UTL_TO_EMBEDDINGS`

---

## Database and Third-Party Providers

Oracle documents two main provider patterns for embeddings:

- in-database ONNX embedding models
- third-party REST providers such as Cohere, Google AI, Hugging Face, Generative AI, OpenAI-compatible providers, Vertex AI, Mistral AI, and local Ollama or Private AI endpoints

Oracle documents provider selection through JSON parameters passed to vector utility functions.

For the database provider, Oracle documents:

```json
{
  "provider" : "database",
  "model"    : "<in-database ONNX embedding model name>"
}
```

Oracle documents additional provider details that matter during design:

- text embedding can use `cohere`, `googleai`, `huggingface`, `ocigenai`, `openai`, `vertexai`, `mistralai`, `ollama`, or `privateai`
- image embedding is only documented with Vertex AI
- local providers can use `host: "local"` for supported providers such as `ollama` and `privateai`

Use in-database providers when you want Oracle-managed ONNX execution. Use REST providers when the model lives outside the database or when your organization standardizes on an external embedding service.

---

## `UTL_TO_TEXT`

Oracle documents `UTL_TO_TEXT` as the conversion step for binary or text documents before chunking.

Documented parameters include:

- `plaintext`
- `charset`
- `format`

Oracle documents these `format` values:

- `BINARY` for rich content such as PDF or Word
- `TEXT` for plain text
- `IGNORE` for no conversion

Use `UTL_TO_TEXT` when the source is a file-oriented document instead of already normalized text.

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

## `UTL_TO_CHUNKS` Parameters That Matter

Oracle documents `UTL_TO_CHUNKS` with these key JSON parameters:

- `by`
- `max`
- `overlap`
- `split`
- `custom_list`
- `vocabulary`
- `language`
- `normalize`
- `norm_options`
- `extended`

These parameters drive most chunk-quality decisions:

- `by` can be `characters`, `words`, or `vocabulary`
- `max` changes meaning depending on `by`
- `split` can be `none`, `newline`, `blankline`, `space`, `recursively`, `sentence`, or `custom`
- `overlap` is documented as `5%` to `20%` of `max`
- `normalize` defaults to `all`, with options such as `punctuation`, `whitespace`, and `widechar`

Oracle also documents that sentence-aware chunking depends on language-specific punctuation and abbreviation rules, which is why `language` and custom language data matter.

---

## Custom Vocabulary and Language Data

Oracle documents two helper procedures for improving chunking:

- `CREATE_VOCABULARY`
- `CREATE_LANG_DATA`

Use `CREATE_VOCABULARY` when chunking by tokenizer vocabulary instead of by words or characters. Oracle explicitly notes that the vocabulary file should match the embedding model used for chunking.

Use `CREATE_LANG_DATA` when sentence boundaries or abbreviations need language-specific handling beyond the built-in defaults.

These helpers matter most when:

- the embedding model uses token boundaries that differ materially from whitespace words
- the corpus uses domain-specific abbreviations
- the language is segmented or punctuation-heavy

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

## `UTL_TO_EMBEDDINGS` for Batch Pipelines

Oracle documents `UTL_TO_EMBEDDINGS` as the array-oriented embedding call for chunk pipelines.

Oracle's documented example chains text extraction, chunking, and embedding in one relational flow:

```sql
CREATE TABLE doc_chunks AS
(SELECT dt.id doc_id, et.embed_id, et.embed_data, TO_VECTOR(et.embed_vector) embed_vector
   FROM documentation_tab dt,
        dbms_vector.utl_to_embeddings(
          dbms_vector.utl_to_chunks(
            dbms_vector.utl_to_text(dt.data),
            json('{"normalize":"all"}')),
          json('{"provider":"database", "model":"doc_model"}')) t,
        JSON_TABLE(
          t.column_value, '$[*]'
          COLUMNS (
            embed_id NUMBER PATH '$.embed_id',
            embed_data VARCHAR2(4000) PATH '$.embed_data',
            embed_vector CLOB PATH '$.embed_vector')) et
);
```

Use this shape when the workflow needs reproducible relational storage of chunk text plus embeddings.

---

## In-Database ONNX Model Import

Oracle documents the in-database ONNX path around `DBMS_VECTOR.LOAD_ONNX_MODEL` and `VECTOR_EMBEDDING`.

The documented flow is:

1. prepare a directory object
2. grant directory access and `CREATE MINING MODEL`
3. load the ONNX embedding model with `DBMS_VECTOR.LOAD_ONNX_MODEL`
4. generate embeddings with `VECTOR_EMBEDDING`

Documented examples:

```sql
EXECUTE DBMS_VECTOR.LOAD_ONNX_MODEL(
  'DM_DUMP',
  'my_embedding_model.onnx',
  'doc_model');

SELECT VECTOR_EMBEDDING(doc_model USING 'hello' AS data) AS embedding;
```

Use this path when the model should execute inside Oracle AI Database instead of through a remote provider.

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

- Choose SQL functions for scoring-oriented SQL flows and PL/SQL utilities for chainable transformation workflows.
- Keep chunking and embedding settings stable within one corpus.
- Match `CREATE_VOCABULARY` output, `by: "vocabulary"` chunking, and the embedding model tokenizer when using token-aware chunking.
- Use an in-database ONNX model when you need database-local embedding generation.
- Use third-party providers only after reviewing their endpoint, credential, and cost model.
- Preserve source text and document metadata together with the embedding output.

---

## Common Mistakes

### Mistake 1: Mixing Providers Without a Deliberate Compatibility Plan

Oracle documents many provider options, but compatibility of embeddings is still a model-space concern.

### Mistake 2: Embedding Large Documents Without Chunking

Oracle documents chunking and text extraction utilities because document pipelines usually need them before embedding.

### Mistake 3: Using Vocabulary Chunking Without Matching the Tokenizer

Oracle explicitly notes that the chosen model should match the vocabulary file used for chunking.

### Mistake 4: Treating Text Generation APIs as Embedding APIs

`UTL_TO_GENERATE_TEXT` and `UTL_TO_EMBEDDING` are different operations. Use the one that matches the workflow.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c:** Oracle AI Vector Search embedding features are not available.
- **Oracle Database 23ai:** Oracle introduces the vector workflow, including ONNX model import and embedding utilities.
- **Oracle AI Database 26ai:** Current documentation includes `DBMS_VECTOR_CHAIN`, third-party embedding access, and end-to-end chunking and embedding pipelines.

---

## Sources

- [Oracle AI Vector Search User's Guide - Your Vector Documentation Map to GenAI Prompts](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/your-vector-documentation-map-genai-prompts.html)
- [Oracle AI Vector Search User's Guide - Understand the Stages of Data Transformations](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/understand-stages-data-transformations.html)
- [Oracle AI Vector Search User's Guide - About SQL Functions to Generate Embeddings](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/sql-functions-generate-embeddings.html)
- [Oracle AI Vector Search User's Guide - About Chainable Utility Functions and Common Use Cases](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/chainable-utility-functions-and-common-use-cases.html)
- [Oracle AI Vector Search User's Guide - UTL_TO_TEXT](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/utl_to_text.html)
- [Oracle AI Vector Search User's Guide - UTL_TO_CHUNKS](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/utl_to_chunks-dbms_vector_chain.html)
- [Oracle AI Vector Search User's Guide - UTL_TO_EMBEDDING and UTL_TO_EMBEDDINGS](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/utl_to_embedding-and-utl_to_embeddings-dbms_vector_chain.html)
- [Oracle AI Vector Search User's Guide - Access Third-Party Models for Vector Generation Leveraging Third-Party REST APIs](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/access-third-party-models-vector-generation-leveraging-third-party-rest-apis.html)
- [Oracle AI Vector Search User's Guide - Import ONNX Models into Oracle AI Database End-to-End Example](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/import-onnx-models-oracle-ai-database-end-end-example.html)
- [Oracle AI Vector Search User's Guide - SQL Quick Start Using a Vector Embedding Model Uploaded into the Database](https://docs.oracle.com/en/database/oracle/oracle-database/23/vecse/sql-quick-start-using-vector-embedding-model-uploaded-database.html)
- [Oracle AI Vector Search User's Guide - DBMS_VECTOR](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/dbms_vector-vecse.html)
- [Oracle AI Vector Search User's Guide - CREATE_VOCABULARY](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/create_vocabulary.html)
- [Oracle AI Vector Search User's Guide - CREATE_LANG_DATA](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/create_lang_data.html)
- [Oracle AI Vector Search User's Guide - CREATE_PREFERENCE](https://docs.oracle.com/en/database/oracle/oracle-database/23/vecse/create_preference.html)
- [Oracle AI Vector Search User's Guide - DBMS_VECTOR_CHAIN](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/dbms_vector_chain-vecse.html)
- [Oracle Database PL/SQL Packages and Types Reference - DBMS_VECTOR_CHAIN](https://docs.oracle.com/en/database/oracle/oracle-database/26/arpls/dbms_vector_chain1.html)
