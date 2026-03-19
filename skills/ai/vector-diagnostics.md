# Vector Diagnostics in Oracle AI Database

## Overview

Oracle AI Vector Search includes dictionary views, initialization parameters, memory-pool views, and PL/SQL packages for operational diagnosis.

Use this file when you need to inspect vector memory usage, understand vector-specific parameters, or map a troubleshooting task to the right diagnostic view.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Topic map for diagnostics | Oracle AI Vector Search User's Guide, **Your Vector Documentation Map to GenAI Prompts** |
| Vector memory pool view | Oracle AI Vector Search User's Guide, **V$VECTOR_MEMORY_POOL** |
| Vector restrictions and vector pool sizing | Oracle Database Release Notes, **Restrictions for Oracle AI Vector Search** |
| Vector statistics and parameters | Oracle AI Vector Search topic map, **Oracle AI Vector Search Statistics and Initialization Parameters** |
| Package landscape | Oracle AI Vector Search User's Guide, **DBMS_VECTOR**, **DBMS_VECTOR_CHAIN**, **DBMS_HYBRID_VECTOR** |

---

## `V$VECTOR_MEMORY_POOL`

Oracle documents `V$VECTOR_MEMORY_POOL` for inspecting Vector Memory allocation.

The documented example is:

```sql
select CON_ID, POOL, ALLOC_BYTES/1024/1024 as ALLOC_BYTES_MB,
USED_BYTES/1024/1024 as USED_BYTES_MB
from V$VECTOR_MEMORY_POOL order by 1,2;
```

Oracle documents the pool as containing:

- a `1MB` pool for in-memory neighbor-graph index allocations
- a `64K` pool for metadata

Use this view first when troubleshooting HNSW memory pressure.

---

## Initialization Parameters

Oracle documents `VECTOR_MEMORY_SIZE` as the parameter for sizing the Vector Pool manually.

The release-notes restrictions page also documents behavior when `VECTOR_MEMORY_SIZE` is set to `1` and `sga_target` is greater than `0`: HNSW index creation can automatically grow the vector memory pool to satisfy the new index.

Use parameter review together with `V$VECTOR_MEMORY_POOL` instead of treating memory pressure as an opaque index failure.

---

## Vector-Specific Views

The Oracle AI Vector Search topic map groups diagnostics into:

- text processing views
- views related to vector indexes and hybrid vector indexes
- vector-search statistics and initialization parameters

Use the topic map as the routing index when you need a more specialized view than `V$VECTOR_MEMORY_POOL`.

---

## Package Landscape

Oracle documents the vector package landscape as:

- `DBMS_VECTOR` for index operations, model loading, accuracy reports, and memory advisory
- `DBMS_VECTOR_CHAIN` for chunking, embedding, reranking, text generation, and summarization
- `DBMS_HYBRID_VECTOR` for hybrid search SQL generation and search execution

## Best Practices

- Start with memory and parameter inspection before rebuilding HNSW indexes.
- Use the topic map to choose the right diagnostic view family instead of guessing from generic `DBA_*` views.
- Use `DBMS_VECTOR` status and advisor procedures before changing index shape or memory size.
- Keep release notes in view because some vector restrictions are platform-specific.

---

## Common Mistakes

### Mistake 1: Troubleshooting HNSW Without Checking Vector Memory

Oracle documents a dedicated vector memory pool and a dedicated view for it.

### Mistake 2: Treating All Vector Failures as DDL Problems

Oracle documents separate diagnostics for memory, package operations, and index health.

### Mistake 3: Ignoring Platform Restrictions

Oracle documents AI Vector Search restrictions in release notes, including ONNX platform limits and vector-pool sizing behavior.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c:** Vector diagnostics do not exist because AI Vector Search is not available.
- **Oracle Database 23ai:** Oracle introduces vector-specific views, parameters, and package diagnostics with AI Vector Search.
- **Oracle AI Database 26ai:** Current documentation includes the full diagnostics map, `V$VECTOR_MEMORY_POOL`, and expanded package references.

---

## Sources

- [Oracle AI Vector Search User's Guide - Your Vector Documentation Map to GenAI Prompts](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/your-vector-documentation-map-genai-prompts.html)
- [Oracle AI Vector Search User's Guide - V$VECTOR_MEMORY_POOL](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/vvector_memory_pool.html)
- [Oracle Database Release Notes - Restrictions for Oracle AI Vector Search](https://docs.oracle.com/en/database/oracle/oracle-database/26/rnrdm/issues-all-platforms-2.html)
- [Oracle AI Vector Search User's Guide - DBMS_VECTOR](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/dbms_vector-vecse.html)
- [Oracle AI Vector Search User's Guide - DBMS_VECTOR_CHAIN](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/dbms_vector_chain-vecse.html)
- [Oracle AI Vector Search User's Guide - DBMS_HYBRID_VECTOR](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/dbms_hybrid_vector-vecse.html)
