# Vector Packages in Oracle AI Database

## Overview

Oracle AI Vector Search exposes package-level APIs in addition to SQL DDL and query syntax. Oracle documents three main package families:

- `DBMS_VECTOR`
- `DBMS_VECTOR_CHAIN`
- `DBMS_HYBRID_VECTOR`

Use this file when you need to choose the right package for vector index operations, embedding and chunking pipelines, reranking and text-generation helpers, or hybrid-search execution.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Vector package overview | Oracle AI Vector Search User's Guide, **DBMS_VECTOR**, **DBMS_VECTOR_CHAIN**, and **DBMS_HYBRID_VECTOR** |
| `DBMS_VECTOR` operations | Oracle AI Vector Search User's Guide, **DBMS_VECTOR** |
| `DBMS_VECTOR_CHAIN` operations | Oracle AI Vector Search User's Guide, **DBMS_VECTOR_CHAIN** |
| `DBMS_HYBRID_VECTOR` operations | Oracle AI Vector Search User's Guide, **DBMS_HYBRID_VECTOR** |
| Package reference details | Oracle Database PL/SQL Packages and Types Reference, **DBMS_VECTOR_CHAIN** |

---

## Choose the Package by Task

Oracle's current package split is functional:

- use `DBMS_VECTOR` for vector index creation, rebuild, status, accuracy reporting, model loading, and memory advisory
- use `DBMS_VECTOR_CHAIN` for text extraction, chunking, embedding generation, reranking, summarization, and text generation
- use `DBMS_HYBRID_VECTOR` for hybrid search execution and generated SQL helpers

If the task is ordinary vector SQL or DDL rather than package APIs, start with `vector-operations.md`, `vector-indexes.md`, or `hybrid-vector-search.md` instead.

---

## `DBMS_VECTOR`

Oracle documents `DBMS_VECTOR` as the operational package for vector indexes and related utilities.

Representative procedures and functions documented in the user guide include:

- `CREATE_INDEX`
- `REBUILD_INDEX`
- `GET_INDEX_STATUS`
- `INDEX_ACCURACY_QUERY`
- `INDEX_ACCURACY_REPORT`
- `INDEX_VECTOR_MEMORY_ADVISOR`

Documented example:

```sql
exec DBMS_VECTOR.INDEX_VECTOR_MEMORY_ADVISOR(
    INDEX_TYPE=>'HNSW',
    NUM_VECTORS=>10000,
    DIM_COUNT=>768,
    DIM_TYPE=>'FLOAT32',
    PARAMETER_JSON=>'{"accuracy":90}',
    RESPONSE_JSON=>:response_json);
```

Use `DBMS_VECTOR` when the question is about index lifecycle, index health, or vector-memory planning.

---

## `DBMS_VECTOR_CHAIN`

Oracle documents `DBMS_VECTOR_CHAIN` for chainable document and model workflows.

Representative functions documented in the user guide and package reference include:

- `UTL_TO_TEXT`
- `UTL_TO_CHUNKS`
- `UTL_TO_EMBEDDING`
- `UTL_TO_EMBEDDINGS`
- `UTL_TO_SUMMARY`
- `UTL_TO_RERANK`
- `UTL_TO_GENERATE_TEXT`

Documented example:

```sql
select dbms_vector_chain.utl_to_embedding('Hello world', json(:params)) from dual;
```

Use `DBMS_VECTOR_CHAIN` when the workflow is about chunking, embeddings, reranking, summarization, or text generation rather than index management.

---

## `DBMS_HYBRID_VECTOR`

Oracle documents `DBMS_HYBRID_VECTOR` for hybrid-search execution and helper SQL generation.

Representative package members documented in the user guide include:

- `SEARCH`
- `SEARCHPIPELINE`
- `GET_SQL`
- `GET_HYBRID_SQL`
- `GET_SQL_TEMPLATE`

Documented example:

```sql
SELECT DBMS_HYBRID_VECTOR.SEARCH(
json('{ "hybrid_index_name"     : "my_hybrid_idx",
        "vector":
                { "search_text" : "stock fraud" },
        "text"  :
                { "contains"    : "$ABC AND $Corporation" },
        "return":
                { "topN"        : 10 }
      }'))
FROM dual;
```

Use `DBMS_HYBRID_VECTOR` when the workload needs hybrid retrieval with both lexical and semantic components.

---

## Routing to Neighbor Skills

| If the task is... | Read |
|-------------------|------|
| vector SQL operators, distance metrics, or exact versus approximate SQL | `vector-operations.md` |
| vector index family selection or `CREATE VECTOR INDEX` | `vector-indexes.md` |
| chunking, provider choice, and embedding workflows | `vector-embeddings.md` |
| hybrid vector index design and restrictions | `hybrid-vector-search.md` |
| views, memory pool, and initialization parameters | `vector-diagnostics.md` |

---

## Best Practices

- Choose the package by workflow type before looking for subprogram names.
- Use `DBMS_VECTOR` for index management, not document-pipeline work.
- Use `DBMS_VECTOR_CHAIN` when the task includes reranking, summarization, or text generation.
- Use `DBMS_HYBRID_VECTOR` only when the query path is explicitly hybrid.

---

## Common Mistakes

### Mistake 1: Treating `DBMS_VECTOR` as the Package for All Vector Work

Oracle splits index management and pipeline helpers across multiple packages.

### Mistake 2: Looking for Reranking in the Wrong Package

Oracle documents reranking in `DBMS_VECTOR_CHAIN`, not `DBMS_VECTOR`.

### Mistake 3: Using `DBMS_HYBRID_VECTOR` for Pure Vector SQL

Oracle documents it for hybrid-search execution and generated SQL helpers, not as the general package for all vector queries.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c:** Vector packages are not available because AI Vector Search is not available.
- **Oracle Database 23ai:** Oracle introduces the vector package surface together with AI Vector Search.
- **Oracle AI Database 26ai:** Current documentation includes the broadest package surface across index operations, chainable embedding workflows, and hybrid-search helpers.

---

## Sources

- [Oracle AI Vector Search User's Guide - DBMS_VECTOR](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/dbms_vector-vecse.html)
- [Oracle AI Vector Search User's Guide - DBMS_VECTOR_CHAIN](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/dbms_vector_chain-vecse.html)
- [Oracle AI Vector Search User's Guide - DBMS_HYBRID_VECTOR](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/dbms_hybrid_vector-vecse.html)
- [Oracle Database PL/SQL Packages and Types Reference - DBMS_VECTOR_CHAIN](https://docs.oracle.com/en/database/oracle/oracle-database/26/arpls/dbms_vector_chain1.html)
