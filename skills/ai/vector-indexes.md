# Vector Indexes in Oracle AI Database

## Overview

Oracle AI Vector Search supports approximate vector indexes for high-speed similarity search. Oracle documents IVF and HNSW as the two primary vector-index families and provides SQL DDL plus `DBMS_VECTOR` management procedures.

Use this file when you need to choose an index family, create indexes, or inspect vector-index health and memory planning.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Vector index guidance | Oracle AI Vector Search User's Guide, **Guidelines for Using Vector Indexes** |
| IVF syntax | Oracle AI Vector Search User's Guide, **Inverted File Flat CREATE INDEX** |
| SQL reference | Oracle AI Database SQL Language Reference, **CREATE VECTOR INDEX** |
| Index status, checkpoint, and advisor procedures | Oracle AI Vector Search User's Guide, **Vector Index Status, Checkpoint, and Advisor Procedures** |
| New feature notes | Oracle AI Database 26ai New Features Guide, **Vector Indexes** |

---

## Index Families

Oracle documents these two primary vector-index families:

- IVF: `ORGANIZATION NEIGHBOR PARTITIONS`
- HNSW: `ORGANIZATION INMEMORY NEIGHBOR GRAPH`

Oracle documents vector indexes as the approximate-search path that trades some accuracy for speed.

---

## Documented IVF Example

Oracle documents this IVF example:

```sql
CREATE VECTOR INDEX galaxies_ivf_idx ON galaxies (embedding)
ORGANIZATION NEIGHBOR PARTITIONS
DISTANCE COSINE
WITH TARGET ACCURACY 95;
```

Oracle documents IVF parameters including:

- `DISTANCE`
- `WITH TARGET ACCURACY`
- `TYPE IVF`
- `NEIGHBOR PARTITIONS`
- `SAMPLES_PER_PARTITION`
- `MIN_VECTORS_PER_PARTITION`

---

## Metric Matching

Oracle's index guidance documents that the distance function in the SQL statement must match the distance function used by the vector index for the optimizer to use that index.

Keep metric choice aligned across:

- the embedding model
- the vector index
- the query predicate

---

## Restrictions and Planning

Oracle documents several important restrictions and planning points:

- IVF cannot index `SPARSE` vectors
- HNSW uses the Vector Memory Pool
- unsupported targets include external tables, IOTs, global temporary tables, materialized views, immutable tables, and function-based vector indexes

Use HNSW when low-latency approximate search and memory-backed structures fit the workload. Use IVF when its partitioned layout and target-accuracy model fit better.

---

## `DBMS_VECTOR` for Index Operations

Oracle documents `DBMS_VECTOR` procedures and functions for operational work around vector indexes, including:

- `CREATE_INDEX`
- `REBUILD_INDEX`
- `GET_INDEX_STATUS`
- `INDEX_ACCURACY_QUERY`
- `INDEX_ACCURACY_REPORT`
- `INDEX_VECTOR_MEMORY_ADVISOR`

Oracle documents the memory advisor with examples such as:

```sql
exec DBMS_VECTOR.INDEX_VECTOR_MEMORY_ADVISOR(
    INDEX_TYPE=>'HNSW',
    NUM_VECTORS=>10000,
    DIM_COUNT=>768,
    DIM_TYPE=>'FLOAT32',
    PARAMETER_JSON=>'{"accuracy":90}',
    RESPONSE_JSON=>:response_json);
```

Use the advisor before assuming the current Vector Memory Pool is sufficient for HNSW.

---

## Best Practices

- Choose the index family after deciding metric, memory model, and target latency.
- Use the same distance metric in queries and indexes.
- Use `INDEX_VECTOR_MEMORY_ADVISOR` before large HNSW deployments.
- Revisit IVF organization when Oracle indicates reorganization or rebuild is needed.

---

## Common Mistakes

### Mistake 1: Treating IVF and HNSW as Interchangeable

Oracle documents different storage, memory, and maintenance behavior for the two index families.

### Mistake 2: Ignoring Memory Planning for HNSW

Oracle documents HNSW as an in-memory neighbor graph. Size the Vector Memory Pool deliberately.

### Mistake 3: Expecting Approximate Indexes to Fix a Bad Embedding Design

Oracle documents indexing as a search-acceleration feature, not as a correction layer for bad embeddings or wrong metrics.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c:** Vector indexes are not available.
- **Oracle Database 23ai:** Oracle introduces vector indexes and the `CREATE VECTOR INDEX` statement.
- **Oracle AI Database 26ai:** Oracle documents additional vector-index features such as auto IVF reorganization, scalar-quantized HNSW, and RAC-aware HNSW enhancements.

---

## Sources

- [Oracle AI Vector Search User's Guide - Guidelines for Using Vector Indexes](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/guidelines-using-vector-indexes.html)
- [Oracle AI Vector Search User's Guide - Inverted File Flat CREATE INDEX](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/inverted-file-flat-index-creation-syntax-and-parameters.html)
- [Oracle AI Database SQL Language Reference - CREATE VECTOR INDEX](https://docs.oracle.com/en/database/oracle/oracle-database/26/sqlrf/create-vector-index.html)
- [Oracle AI Vector Search User's Guide - Vector Index Status, Checkpoint, and Advisor Procedures](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/vector-index-status-checkpoint-and-advisor-procedures.html)
- [Oracle AI Database 26ai New Features Guide - Vector Indexes](https://docs.oracle.com/en/database/oracle/oracle-database/26/nfcoa/vector-indexes.html)
