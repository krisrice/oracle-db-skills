# Vector Operations in Oracle AI Database

## Overview

Oracle AI Vector Search exposes SQL operators and functions for distance calculation, exact search, approximate search, vector arithmetic, and related vector operations.

Use this file when you need to write vector SQL, choose a distance metric, or understand how Oracle expects query predicates to interact with vector indexes.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| Distance metrics | Oracle AI Vector Search User's Guide, **VECTOR_DISTANCE** and the vector topic map |
| Distance function and operators | Oracle AI Vector Search User's Guide, **VECTOR_DISTANCE** |
| Similarity-search SQL | Oracle AI Vector Search User's Guide, **Query Data With Similarity and Hybrid Searches** |
| Vector constructors and descriptors | Oracle AI Vector Search User's Guide, **Vector Constructors, Converters, Descriptors, Arithmetic Operators and Aggregate Functions** |

---

## Distance Metrics

Oracle documents these vector distance metrics:

- `COSINE`
- `DOT`
- `EUCLIDEAN`
- `EUCLIDEAN_SQUARED`
- `MANHATTAN`
- `HAMMING`
- `JACCARD`

Oracle explicitly documents that `JACCARD` requires `BINARY` vectors.

---

## `VECTOR_DISTANCE` and Shorthand Operators

Oracle documents `VECTOR_DISTANCE(expr1, expr2, metric)` as the general function.

Oracle also documents shorthand operators:

```sql
expr1 <-> expr2
expr1 <=> expr2
expr1 <#> expr2
```

Oracle documents the mappings as:

- `<->` -> Euclidean / `L2_DISTANCE`
- `<=>` -> cosine distance
- `<#>` -> negative inner product / dot-product ordering

---

## Exact and Approximate Search

Oracle documents:

- exact similarity search without a vector index
- approximate similarity search using a vector index

Use approximate search when you want vector-index acceleration. Use exact search when correctness is more important than latency or when validating relevance.

---

## Similarity Search SQL

Oracle documents vector search as ordinary SQL that can be combined with filters, joins, and other relational predicates.

Use this as the default mental model: vector search is not outside SQL. It is SQL with vector predicates and vector-aware indexes.

---

## Constructors and Descriptors

Oracle documents functions around vector inspection and manipulation, including:

- `TO_VECTOR`
- `FROM_VECTOR`
- `VECTOR_SERIALIZE`
- `VECTOR_NORM`
- `VECTOR_DIMS`
- `VECTOR_DIMENSION_COUNT`
- `VECTOR_DIMENSION_FORMAT`

Use these when you need conversion, inspection, or debugging rather than only search.

---

## Best Practices

- Match the metric to the embedding model's expected vector space.
- Keep the query metric aligned with the vector index metric.
- Use exact search as a validation baseline before tuning approximate search.
- Combine vector predicates with ordinary SQL filters when business scope matters.

---

## Common Mistakes

### Mistake 1: Treating All Metrics as Semantically Equivalent

Oracle documents several metrics because they are not interchangeable.

### Mistake 2: Using `JACCARD` on Non-Binary Vectors

Oracle documents `JACCARD` specifically for `BINARY` vectors.

### Mistake 3: Forgetting That Search Is Still SQL

Oracle vector operations are designed to participate in ordinary SQL. Use relational filters and joins where they belong.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c:** Vector operations are not available.
- **Oracle Database 23ai:** Oracle introduces vector functions and operators together with AI Vector Search.
- **Oracle AI Database 26ai:** Current documentation includes newer metric coverage such as Jaccard distance and a broader vector-operations surface.

---

## Sources

- [Oracle AI Vector Search User's Guide - VECTOR_DISTANCE](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/vector_distance.html)
- [Oracle AI Vector Search User's Guide - Query Data With Similarity and Hybrid Searches](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/query-data-similarity-and-hybrid-searches.html)
- [Oracle AI Vector Search User's Guide - Your Vector Documentation Map to GenAI Prompts](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/your-vector-documentation-map-genai-prompts.html)
