# AI Vector Search in Oracle AI Database

## Overview

Oracle AI Vector Search adds semantic retrieval to Oracle Database. Oracle documents it as combining vector search over unstructured content with relational search over business data in one system.

This file is the entry point for the AI Vector Search skill set. Start here when the question is broad, then load the narrower skill that matches the storage, query, indexing, or diagnostics task.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Core concepts and workflow | Oracle AI Vector Search User's Guide, **Overview of Oracle AI Vector Search** |
| `VECTOR` column definitions | Oracle AI Vector Search User's Guide, **Create Tables Using the VECTOR Data Type** |
| Distance functions and shorthand operators | Oracle AI Vector Search User's Guide, **VECTOR_DISTANCE** |
| Similarity and hybrid search | Oracle AI Vector Search User's Guide, **Query Data With Similarity and Hybrid Searches** |
| Vector index guidance | Oracle AI Vector Search User's Guide, **Guidelines for Using Vector Indexes** |
| Hybrid vector indexing | Oracle AI Database SQL Language Reference, **CREATE HYBRID VECTOR INDEX** |
| Packaged APIs | Oracle AI Vector Search User's Guide, **DBMS_VECTOR**, **DBMS_VECTOR_CHAIN**, and **DBMS_HYBRID_VECTOR** |

---

## Start Here When

- you need a broad orientation to Oracle AI Vector Search before choosing a design path
- you are not yet sure whether the task is mainly about `VECTOR` storage, embedding pipelines, vector SQL, vector indexes, or hybrid search
- you want the right next skill instead of reading every vector-search file

If you already know the exact task, go straight to `skills/ai/SKILLS.md` or use the routing table below.

---

## Minimal Workflow

Oracle's current AI Vector Search documentation fits this mental model:

1. Store embeddings in a `VECTOR` column with the right dimensions and format.
2. Query with `VECTOR_DISTANCE` or shorthand operators.
3. Add a vector index when approximate search is needed for performance.
4. Use a hybrid vector index when the workload needs both keyword and semantic retrieval in one path.

Minimal documented entry points:

```sql
CREATE TABLE my_vectors (
  id        NUMBER,
  embedding VECTOR(768, FLOAT32)
);

SELECT id
FROM   my_vectors
ORDER  BY VECTOR_DISTANCE(embedding, :query_vector, COSINE)
FETCH  FIRST 10 ROWS ONLY;

CREATE VECTOR INDEX my_vectors_ivf_idx ON my_vectors (embedding)
ORGANIZATION NEIGHBOR PARTITIONS
DISTANCE COSINE;
```

---

## Routing by Task

| Task | Read next |
|------|-----------|
| define `VECTOR` columns, dense versus sparse storage, formats, or restrictions | `vector-data-type.md` |
| build chunking, ONNX, or embedding pipelines | `vector-embeddings.md` |
| choose metrics, operators, or exact versus approximate SQL patterns | `vector-operations.md` |
| choose between IVF and HNSW or use advisor procedures | `vector-indexes.md` |
| build keyword + semantic retrieval with `CREATE HYBRID VECTOR INDEX` or `DBMS_HYBRID_VECTOR` | `hybrid-vector-search.md` |
| inspect memory, parameters, views, or packaged API surface area | `vector-diagnostics.md` |

For the full task index across the AI category, read `skills/ai/SKILLS.md`.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c (generic):** AI Vector Search is not part of the generic 19c baseline.
- **Oracle AI Database 23.4 / 23ai:** `VECTOR`, similarity search, and vector indexing enter the product surface. Some newer capabilities in later docs require a higher `COMPATIBLE` setting.
- **Oracle AI Database 23.6 / 26ai docs:** Current documentation includes hybrid vector indexes, richer packaged APIs, release-update guidance, and additional diagnostics content.

---

## Sources

- [Oracle AI Vector Search User's Guide - Overview of Oracle AI Vector Search](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/overview-ai-vector-search.html)
- [Oracle AI Vector Search User's Guide - Create Tables Using the VECTOR Data Type](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/create-tables-using-vector-data-type.html)
- [Oracle AI Vector Search User's Guide - VECTOR_DISTANCE](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/vector_distance.html)
- [Oracle AI Vector Search User's Guide - Query Data With Similarity and Hybrid Searches](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/query-data-similarity-searches.html)
- [Oracle AI Vector Search User's Guide - Guidelines for Using Vector Indexes](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/guidelines-using-vector-indexes.html)
- [Oracle AI Database SQL Language Reference - CREATE VECTOR INDEX](https://docs.oracle.com/en/database/oracle/oracle-database/26/sqlrf/create-vector-index.html)
- [Oracle AI Database SQL Language Reference - CREATE HYBRID VECTOR INDEX](https://docs.oracle.com/en/database/oracle/oracle-database/26/sqlrf/create-hybrid-vector-index.html)
