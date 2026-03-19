# Hybrid Vector Search in Oracle AI Database

## Overview

Hybrid vector search combines Oracle Text-style keyword search with vector similarity search. Oracle documents this both as a query pattern and as a first-class indexing path through `CREATE HYBRID VECTOR INDEX`.

Use this file when you need keyword constraints and semantic similarity in the same retrieval workflow.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Hybrid search concepts | Oracle AI Vector Search User's Guide, **Query Data With Similarity and Hybrid Searches** |
| Hybrid index DDL | Oracle AI Vector Search User's Guide, **CREATE HYBRID VECTOR INDEX** |
| SQL reference | Oracle AI Database SQL Language Reference, **CREATE HYBRID VECTOR INDEX** |
| Query API | Oracle AI Vector Search User's Guide, **DBMS_HYBRID_VECTOR** |
| Guidelines and restrictions | Oracle AI Vector Search User's Guide, **Guidelines and Restrictions for Hybrid Vector Indexes** |

---

## Hybrid Index DDL

Oracle documents this minimal DDL pattern:

```sql
CREATE HYBRID VECTOR INDEX my_hybrid_idx on
  doc_table(text_column)
  PARAMETERS('MODEL my_doc_model');
```

Oracle documents `MODEL` as the in-database ONNX embedding model used by the hybrid index.

The same syntax page documents `VECTOR_IDXTYPE` and notes that IVF is the default if you omit it.

---

## Query with `DBMS_HYBRID_VECTOR.SEARCH`

Oracle documents `DBMS_HYBRID_VECTOR.SEARCH` as a unified query API for hybrid indexes:

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

Use the `vector` section for semantic intent and the `text` section for explicit lexical conditions.

---

## Filters and Predicate Control

Oracle documents several distinct filtering and constraint paths in `DBMS_HYBRID_VECTOR.SEARCH`.

### Keyword Filters

Use the `text` section for Oracle Text-style lexical constraints:

- `text.contains`
- `text.search_text`
- `text.json_textcontains`

Use `text.contains` when you need explicit Oracle Text query syntax. Use `text.search_text` when you want Oracle to derive the text query from plain search text.

### Structured Filters

Oracle also documents a top-level `filter_by` JSON expression for structured predicates on returned rows.

Documented examples include relational-style comparisons such as:

- equals
- greater-than / less-than
- compound expressions
- JSON `passing` values

Use `filter_by` when the query needs business predicates such as price, status, region, or other non-text attributes in addition to hybrid relevance.

### Path-Based Filtering

Oracle documents `vector.inpath` as a way to restrict search to specific JSON paths that match the vectorizer path lists.

Use this when the hybrid index is built over JSON content and only certain document paths should participate in semantic search.

---

## Filter Hints for Vector Evaluation

Oracle documents `filter_type` inside the `vector` section as a vector-index hint for how filtering should interact with vector index evaluation.

The documented values are:

- `PRE_W`
- `PRE_WO`
- `IN_W`
- `IN_WO`
- `POST_WO`

Oracle documents these as index-hint choices tied to HNSW or IVF behavior, not as generic business predicates.

Use `filter_type` only when you are tuning the vector-evaluation plan. Use `filter_by` or ordinary SQL predicates when you are expressing business conditions.

Documented example:

```sql
SELECT DBMS_HYBRID_VECTOR.SEARCH(
json('{ "hybrid_index_name" : "my_hybrid_idx",
        "vector":
                { "search_text" : "leadership experience",
                  "search_mode" : "DOCUMENT",
                  "filter_type" : "IN_WO" },
        "return":
                { "topN"        : 10 }
      }'))
FROM dual;
```

---

## Additional Helpers

Oracle documents these `DBMS_HYBRID_VECTOR` package members:

- `SEARCH`
- `SEARCHPIPELINE`
- `GET_SQL`
- `GET_HYBRID_SQL`
- `GET_SQL_TEMPLATE`

Use them when you want generated SQL or search-pipeline composition instead of only a direct search call.

---

## Restrictions and Guidelines

Oracle documents hybrid vector indexes as a single domain index that combines Oracle Text and vector indexing structures. Because of that:

- failures in either the text or vector portion can fail the whole index build
- vector memory constraints still apply
- Oracle Text indexing guidelines still apply

Treat hybrid indexing as a combined search design, not as a thin wrapper over an ordinary vector index.

---

## Best Practices

- Use a hybrid vector index when documents need both meaning-based retrieval and exact text focus.
- Use `text.contains` for lexical constraints, `filter_by` for structured business predicates, and `filter_type` only for vector-plan tuning.
- Use `vector.inpath` when JSON path scope matters more than whole-document recall.
- Review both Oracle Text and vector-index restrictions before creating the index.
- Use `GET_SQL` or `GET_HYBRID_SQL` when you need to inspect generated search SQL.
- Keep model selection, text search design, and vector index type aligned in one review.

---

## Common Mistakes

### Mistake 1: Treating Hybrid Search as Purely Application-Side Logic

Oracle documents `CREATE HYBRID VECTOR INDEX` and `DBMS_HYBRID_VECTOR` as first-class database features.

### Mistake 2: Ignoring Oracle Text Requirements

A hybrid vector index includes an Oracle Text component, so text-index design constraints still matter.

### Mistake 3: Treating `filter_type` as a Business Filter

Oracle documents `filter_type` as a vector index hint. It is not the same as `filter_by`, ordinary SQL predicates, or Oracle Text query conditions.

### Mistake 4: Assuming Hybrid Index Creation Can Ignore Vector Memory

Oracle documents vector-memory and vector-index guidelines as still relevant for hybrid index creation.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c:** Hybrid vector indexes are not available.
- **Oracle Database 23ai:** Oracle documents the hybrid vector index path as part of AI Vector Search.
- **Oracle AI Database 26ai:** Current documentation includes additional predicate support and expanded hybrid-vector features.

---

## Sources

- [Oracle AI Vector Search User's Guide - Query Data With Similarity and Hybrid Searches](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/query-data-similarity-and-hybrid-searches.html)
- [Oracle AI Vector Search User's Guide - CREATE HYBRID VECTOR INDEX](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/create-hybrid-vector-index.html)
- [Oracle AI Database SQL Language Reference - CREATE HYBRID VECTOR INDEX](https://docs.oracle.com/en/database/oracle/oracle-database/26/sqlrf/create-hybrid-vector-index.html)
- [Oracle AI Vector Search User's Guide - DBMS_HYBRID_VECTOR](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/dbms_hybrid_vector-vecse.html)
- [Oracle AI Vector Search User's Guide - Guidelines and Restrictions for Hybrid Vector Indexes](https://docs.oracle.com/en/database/oracle/oracle-database/23/vecse/guidelines-and-restrictions-hybrid-vector-indexes.html)
