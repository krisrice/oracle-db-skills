# VECTOR Data Type in Oracle AI Database

## Overview

The `VECTOR` data type is the storage foundation for Oracle AI Vector Search. Oracle documents it as a built-in type for storing embeddings directly in database tables, alongside relational data.

Use this file when you need to define vector columns correctly, choose fixed versus flexible shapes, and understand the documented restrictions on vector columns.

---

## Documentation Map

| Topic | Oracle documentation |
|---|---|
| `VECTOR` overview and examples | Oracle AI Vector Search User's Guide, **Create Tables Using the VECTOR Data Type** |
| New feature notes | Oracle AI Database 26ai New Features Guide, **Vector Data Type** |
| Constructors and descriptors | Oracle AI Vector Search User's Guide, **Vector Constructors, Converters, Descriptors, Arithmetic Operators and Aggregate Functions** |

---

## Minimal and Fixed Definitions

Oracle documents the minimal form as:

```sql
CREATE TABLE my_vectors (id NUMBER, embedding VECTOR);
```

Oracle also documents fixed-shape columns:

```sql
CREATE TABLE my_vectors (id NUMBER, embedding VECTOR(768, INT8));
```

When you omit dimensions and format, Oracle allows vectors of different dimensions and formats in the same column. Oracle also warns that vectors from different embedding models are not comparable for similarity search.

---

## Dimension Element Formats

Oracle documents these dimension element formats:

- `INT8`
- `FLOAT32`
- `FLOAT64`
- `BINARY`

Oracle also documents `DENSE` and `SPARSE` storage forms.

```sql
VECTOR(number_of_dimensions, dimension_element_format, SPARSE)
```

Use a fixed dimension and format when your embedding model is stable. Use a flexible column only when experimentation requires it.

---

## Restrictions Oracle Documents

Important documented restrictions include:

- IVF vector indexes cannot be created on `SPARSE` vectors
- a `VECTOR` column cannot be part of a primary key
- a `VECTOR` column cannot be part of a foreign key
- a `VECTOR` column cannot be part of a unique constraint
- a `VECTOR` column cannot be part of a check constraint
- a `VECTOR` column cannot be a partitioning key
- non-vector indexes such as B-tree, bitmap, text, and spatial indexes cannot be created on a `VECTOR` column

These are table-design constraints, not only index-design constraints.

---

## Related Functions

Oracle documents vector functions and descriptors around the data type, including:

- `TO_VECTOR`
- `FROM_VECTOR`
- `VECTOR_SERIALIZE`
- `VECTOR_NORM`
- `VECTOR_DIMS`
- `VECTOR_DIMENSION_COUNT`
- `VECTOR_DIMENSION_FORMAT`

Use these when you need conversion, inspection, or SQL-level manipulation of vector values.

---

## Best Practices

- Fix dimensions and element format when your model contract is stable.
- Keep vectors from different embedding models out of the same production search set.
- Use `SPARSE` only when you understand the resulting index restrictions.
- Store business metadata alongside the vector column so results remain filterable and explainable.

---

## Common Mistakes

### Mistake 1: Using One Flexible Column for Mixed Embedding Spaces

Oracle explicitly warns that vectors from different embedding models are not comparable for similarity search.

### Mistake 2: Expecting Ordinary Index Types on a `VECTOR` Column

Oracle documents that vector columns cannot use ordinary B-tree, bitmap, text, or spatial indexes.

### Mistake 3: Designing Constraints Around the Vector Column Itself

Oracle documents that vector columns cannot participate in several common constraint and key patterns.

---

## Oracle Version Notes (19c vs 26ai)

- **Oracle Database 19c:** The `VECTOR` data type is not available.
- **Oracle Database 23ai:** Oracle introduces the `VECTOR` data type. Oracle documents `COMPATIBLE` requirements beginning with the 23ai vector feature line.
- **Oracle AI Database 26ai:** Oracle documents expanded support such as sparse vectors and more vector functions around the type.

---

## Sources

- [Oracle AI Vector Search User's Guide - Create Tables Using the VECTOR Data Type](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/create-tables-using-vector-data-type.html)
- [Oracle AI Database 26ai New Features Guide - Vector Data Type](https://docs.oracle.com/en/database/oracle/oracle-database/26/nfcoa/vector-data-type.html)
- [Oracle AI Vector Search User's Guide - Your Vector Documentation Map to GenAI Prompts](https://docs.oracle.com/en/database/oracle/oracle-database/26/vecse/your-vector-documentation-map-genai-prompts.html)
