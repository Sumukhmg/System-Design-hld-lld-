# Database Indexing

[← HLD Index](../README.md) | [Back to Hub](../../README.md)

---

## What is an Index?

An **index** is an auxiliary data structure that lets the database find rows **without scanning the entire table**. It's analogous to the index at the back of a book: instead of reading every page, you jump straight to the right one.

```
Without index:  SELECT * FROM users WHERE email='x'  → full table scan O(N)
With index:     same query                            → index lookup O(log N)
```

> **Trade-off:** indexes **speed up reads** but **slow down writes** (every insert/update/delete must also update the index) and **use extra storage**.

---

## How It Works (B-Tree)

Most relational indexes use a **B-Tree / B+ Tree** — a balanced, sorted tree giving **O(log N)** lookups, range scans, and ordered traversal.

```
                [ M ]
              /       \
        [ D  H ]     [ R  W ]
        / | \        / | \
     leaves (sorted keys → row pointers)
```

- **B+ Tree:** all values in leaves, leaves linked → great for **range queries** (`WHERE age BETWEEN 20 AND 30`) and `ORDER BY`.
- Used by MySQL (InnoDB), PostgreSQL default.

### Hash Index
`hash(key) → bucket`. **O(1)** for exact-match equality, but **no range queries** and no ordering. Good for `WHERE id = ?`.

### LSM-Tree + SSTables
Used by write-heavy stores (Cassandra, RocksDB, LevelDB). Buffers writes in memory (memtable), flushes sorted files (SSTables), compacts in background.
- ✅ Extremely fast **writes** (sequential appends).
- ❌ Reads may check multiple SSTables (mitigated by Bloom filters).
- **B-Tree = read-optimized; LSM-Tree = write-optimized.**

---

## Types of Indexes

| Type | Description |
|------|-------------|
| **Primary index** | On the primary key; defines physical row order (clustered) |
| **Secondary index** | On non-key columns; points to rows |
| **Clustered index** | Table rows stored **in index order** (one per table) |
| **Non-clustered index** | Separate structure with pointers to rows (many allowed) |
| **Composite index** | On multiple columns `(a, b, c)` — order matters! |
| **Unique index** | Enforces uniqueness |
| **Covering index** | Contains all columns a query needs → no table lookup |
| **Partial index** | Indexes only rows matching a condition |
| **Full-text index** | For text search (or use Elasticsearch) |
| **Geospatial index** | R-tree / geohash for location queries → [Uber](../case-studies/uber.md) |

---

## Clustered vs Non-Clustered

```
Clustered (data IS the index, sorted by key):
  [10|row][20|row][30|row] ...   ← one per table, defines storage order

Non-clustered (index → pointer → data):
  index: [Alice→ptr][Bob→ptr]    data heap: rows in insertion order
```

| | Clustered | Non-clustered |
|---|-----------|---------------|
| Count per table | One | Many |
| Stores | Actual rows | Pointers to rows |
| Range scans | Very fast | Extra lookups |

---

## Composite Index & the Leftmost-Prefix Rule

A composite index `(last_name, first_name)` is sorted by `last_name`, then `first_name`. It can serve:
- ✅ `WHERE last_name = ?`
- ✅ `WHERE last_name = ? AND first_name = ?`
- ❌ `WHERE first_name = ?` alone (can't skip the leftmost column)

> **Order columns by selectivity and query pattern.** Put equality-filter columns before range-filter columns.

---

## When NOT to Index
- Small tables (scan is cheap).
- Columns with **low cardinality** (e.g., boolean `is_active`) — index barely narrows results.
- **Write-heavy** tables where read speed isn't critical (indexes tax every write).
- Columns rarely used in `WHERE`/`JOIN`/`ORDER BY`.

> Over-indexing is a real anti-pattern: it bloats storage and slows writes. Index **purposefully**, based on actual query patterns.

---

## Reading a Query Plan

Use `EXPLAIN` (or `EXPLAIN ANALYZE`) to see how the DB executes a query:
- **Seq Scan / Full Table Scan** → no usable index (often bad for big tables).
- **Index Scan / Index Seek** → using an index (good).
- **Index Only Scan** → covering index, no table access (best).

```sql
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 42;
-- Look for "Index Scan using idx_user_id" vs "Seq Scan on orders"
```

---

## Indexing Trade-off Summary

| Aspect | With Index | Without Index |
|--------|-----------|---------------|
| Read speed | ⚡ Fast (O(log N)) | 🐌 Slow (O(N)) |
| Write speed | 🐌 Slower (maintain index) | ⚡ Fast |
| Storage | More | Less |
| Range/sort queries | Fast (B-Tree) | Slow |

---

## Interview Talking Points
- "This query filters on `user_id` and `created_at`, so I'll add a **composite index `(user_id, created_at)`** — it serves the equality + range + ordering in one structure."
- "Since this is **write-heavy time-series**, I'd lean on an **LSM-tree store (Cassandra)** rather than over-indexing a B-tree."
- "For text search I'd offload to **Elasticsearch** rather than a SQL full-text index."
- "I'll verify with `EXPLAIN` that we get an **index scan**, not a seq scan."

---

## Key Takeaways
- Indexes turn **O(N) scans into O(log N) lookups** — at the cost of slower writes & more storage.
- **B-Tree** = read/range optimized (RDBMS); **LSM-Tree** = write optimized (Cassandra/RocksDB); **Hash** = O(1) equality only.
- Know **clustered vs non-clustered**, **composite (leftmost-prefix)**, and **covering** indexes.
- Don't index low-cardinality, small, or write-hot tables blindly; index by **real query patterns**.
- Use **`EXPLAIN`** to confirm the optimizer uses your index.

---
[← HLD Index](../README.md) | [Back to Hub](../../README.md)
