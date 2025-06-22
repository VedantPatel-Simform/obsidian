# Complete Guide to PostgreSQL Query Processing and Storage Internals

## Prerequisites and Foundation Concepts

### Database Storage Fundamentals

Before diving into PostgreSQL specifics, you need to understand these core concepts:

- **Database Files**: Databases store data in files on disk, not just in memory
- **Pages/Blocks**: Data is organized in fixed-size chunks called pages (think of them as "containers")
- **Transactions**: Units of work that must complete entirely or not at all
- **Concurrency**: Multiple users accessing the database simultaneously
- **Indexing**: Data structures that speed up data retrieval (like a book's index)

### Key PostgreSQL Terminology

- **Tuple**: A single row of data in PostgreSQL terminology
- **Relation**: A table in PostgreSQL terminology
- **CTID**: A unique identifier for each tuple's physical location
- **OID**: Object identifier used internally by PostgreSQL
- **Snapshot**: A view of the database at a specific point in time

---

## 1. PostgreSQL Database File Structure and Storage Architecture

### Physical File Organization

PostgreSQL organizes data in a hierarchical file structure on disk:

```
$PGDATA/                    # PostgreSQL data directory
├── base/                   # Contains database subdirectories
│   ├── 12345/             # Database OID directory
│   │   ├── 16384          # Table file (relfilenode)
│   │   ├── 16384.1        # Continuation file (if table > 1GB)
│   │   ├── 16384_fsm      # Free Space Map
│   │   ├── 16384_vm       # Visibility Map
│   │   └── 16385          # Another table or index
│   └── template1/
├── global/                 # Cluster-wide tables
├── pg_wal/                # Write-Ahead Log files
└── pg_tblspc/             # Tablespace links
```

### Understanding Relfilenode

- Each table and index gets a unique `relfilenode` number
- This number becomes the filename in the filesystem
- You can find a table's relfilenode with: `SELECT relfilenode FROM pg_class WHERE relname = 'your_table';`

### Page Structure Deep Dive

PostgreSQL stores data in **pages** (also called blocks), each exactly 8KB by default:

```
Page Structure (8KB):
┌─────────────────────────────────────────────┐
│ Page Header (24 bytes)                      │
├─────────────────────────────────────────────┤
│ Line Pointer Array                          │
│ (each pointer: 4 bytes)                     │
├─────────────────────────────────────────────┤
│                                             │
│         Free Space                          │
│                                             │
├─────────────────────────────────────────────┤
│ Tuple Data (growing upward)                 │
│ ┌─────────────────────────────────────────┐ │
│ │ Tuple Header + User Data                │ │
│ └─────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
```

### Page Header Contents

The page header contains critical metadata:

- **Page size**: Always 8KB (8192 bytes)
- **Checksum**: For data integrity verification
- **LSN (Log Sequence Number)**: For crash recovery
- **Free space pointers**: Track available space in the page
- **Special space**: Used by indexes

### Line Pointer Array

- Each tuple (row) has a corresponding line pointer
- Line pointers contain:
    - **Offset**: Where the tuple starts in the page
    - **Length**: How long the tuple is
    - **Flags**: Status information (live, dead, redirected)

### Tuple Structure

Each tuple contains:

```
Tuple Header (23 bytes minimum):
├── xmin (4 bytes)     # Transaction ID that created this tuple
├── xmax (4 bytes)     # Transaction ID that deleted/updated this tuple
├── ctid (6 bytes)     # Physical location (page, offset)
├── natts (2 bytes)    # Number of attributes/columns
├── hoff (1 byte)      # Header offset to user data
├── infomask (2 bytes) # Status flags
├── infomask2 (2 bytes)# More status flags
├── oid (4 bytes)      # Object ID (if table has OIDs)
└── User Data          # Actual column values
```

---

## 2. Complete Query Processing Flow

Let's trace a query from start to finish: `SELECT * FROM employees WHERE name LIKE 'John%';`

### Step 1: Connection and Authentication

1. Client connects to PostgreSQL server
2. Authentication occurs (password, certificates, etc.)
3. A backend process is assigned to handle this connection

### Step 2: SQL Parsing and Analysis

```
Raw SQL → Lexical Analysis → Syntax Analysis → Parse Tree
```

The parser:

- Breaks SQL into tokens (keywords, identifiers, operators)
- Builds an Abstract Syntax Tree (AST)
- Validates SQL syntax
- Resolves table and column names

### Step 3: Query Rewriting

- Applies view definitions
- Expands rules
- Performs macro substitutions
- Transforms complex queries into simpler forms

### Step 4: Query Planning and Optimization

The planner generates multiple execution strategies and chooses the best:

```
Possible Plans for our query:
1. Sequential Scan on employees
   Cost: 1000.00..2500.00 rows=100 width=32
   
2. Index Scan using employees_name_idx
   Cost: 0.42..45.50 rows=12 width=32
   
3. Bitmap Heap Scan
   Cost: 15.25..65.30 rows=12 width=32
```

The planner considers:

- **Table statistics**: Row counts, data distribution
- **Available indexes**: B-tree, hash, GIN, GiST indexes
- **Cost estimates**: CPU, I/O, memory usage
- **Join algorithms**: Nested loop, hash join, merge join

### Step 5: Query Execution

The executor implements the chosen plan:

#### For Sequential Scan (No Index):

1. **File Access**: Locate table file using relfilenode
2. **Buffer Manager**: Load pages into shared memory
3. **Page Iteration**: Read each page sequentially
4. **Tuple Processing**: For each tuple in each page:
    - Check visibility (MVCC rules)
    - Apply WHERE clause filter
    - Project required columns

#### Detailed Sequential Scan Process:

```
for each page in table:
    load_page_into_buffer(page)
    for each line_pointer in page:
        tuple = get_tuple_from_pointer(line_pointer)
        if tuple_is_visible(tuple, current_snapshot):
            if where_clause_matches(tuple):
                add_to_result_set(tuple)
```

---

## 3. Multi-Version Concurrency Control (MVCC) Explained

MVCC is PostgreSQL's method for handling concurrent transactions without locking readers.

### How MVCC Works

Every tuple has two critical fields:

- **xmin**: Transaction ID that created this tuple
- **xmax**: Transaction ID that deleted/updated this tuple (0 if still live)

### Transaction Snapshots

When a transaction starts, it gets a snapshot containing:

- **xmin**: Oldest transaction still running
- **xmax**: Next transaction ID to be assigned
- **Active XIDs**: List of transactions currently running

### Visibility Rules

A tuple is visible to a transaction if:

1. **Creation check**: `tuple.xmin` is committed and < snapshot.xmax
2. **Deletion check**: `tuple.xmax` is 0 OR uncommitted OR > snapshot.xmax
3. **Not in active list**: Neither xmin nor xmax is in the active transaction list

### MVCC Example

```
Time | Transaction | Action                    | Tuple State
-----|-------------|---------------------------|------------------
T1   | TXN 100     | INSERT (id=1, name='John')| xmin=100, xmax=0
T2   | TXN 101     | UPDATE SET name='Johnny'  | Old: xmin=100, xmax=101
     |             |                           | New: xmin=101, xmax=0
T3   | TXN 102     | SELECT * FROM table       | Sees based on snapshot
```

---

## 4. Buffer Management System

PostgreSQL doesn't access disk directly for every query. Instead, it uses a sophisticated buffer management system.

### Shared Buffers

- Fixed-size memory pool (configured by `shared_buffers`)
- Stores frequently accessed pages
- Shared among all database connections
- Default size: 128MB (often increased to 25% of RAM)

### Buffer Manager Operations

#### Page Request Process:

1. **Hash lookup**: Check if page is already in shared buffers
2. **Cache hit**: Return pointer to page in memory
3. **Cache miss**:
    - Find a free buffer slot
    - If no free slots, evict a page (LRU policy)
    - Read page from disk into buffer
    - Update buffer metadata

#### Buffer Replacement Policy:

PostgreSQL uses a **Clock Sweep** algorithm (variant of LRU):

- Each buffer has a usage counter
- On access, increment usage counter
- For replacement, scan buffers and decrement counters
- Choose buffer with counter = 0

### WAL (Write-Ahead Logging)

Before any data page is written to disk:

1. Changes are logged to WAL files
2. WAL record includes: transaction ID, page location, old/new values
3. WAL is flushed to disk before data pages (ensures durability)

---

## 5. Index Architecture and Usage

### B-Tree Index Structure (Most Common)

```
Root Page
├── Pointer to Internal Page 1 ('A-M')
│   ├── Pointer to Leaf Page 1 ('A-F')
│   └── Pointer to Leaf Page 2 ('G-M')
└── Pointer to Internal Page 2 ('N-Z')
    ├── Pointer to Leaf Page 3 ('N-S')
    └── Pointer to Leaf Page 4 ('T-Z')

Leaf Pages contain: Key → CTID pairs
```

### Index Lookup Process

For query: `SELECT * FROM employees WHERE employee_id = 12345;`

1. **Root page access**: Find which internal page contains range for 12345
2. **Internal page traversal**: Navigate down to appropriate leaf page
3. **Leaf page search**: Binary search for exact key value
4. **Heap access**: Use CTID to fetch actual tuple from table
5. **Visibility check**: Apply MVCC rules to determine if tuple is visible

### Index Types in PostgreSQL

#### B-Tree (Default)

- **Use case**: Equality and range queries (`=`, `<`, `>`, `BETWEEN`)
- **Example**: `CREATE INDEX idx_name ON table (column);`

#### Hash Index

- **Use case**: Equality queries only (`=`)
- **Advantage**: Faster for exact matches
- **Limitation**: Not crash-safe until PostgreSQL 10

#### GIN (Generalized Inverted Index)

- **Use case**: Full-text search, array operations, JSONB
- **Example**: `CREATE INDEX idx_tags ON articles USING gin(tags);`

#### GiST (Generalized Search Tree)

- **Use case**: Geometric data, full-text search
- **Example**: `CREATE INDEX idx_location ON stores USING gist(location);`

---

## 6. Update and Delete Operations Deep Dive

### Update Process (Tuple Versioning)

When you execute: `UPDATE employees SET salary = 75000 WHERE id = 123;`

#### Step-by-Step Process:

1. **Find target tuple**: Use index or sequential scan
2. **Visibility check**: Ensure tuple is visible to current transaction
3. **Lock tuple**: Prevent concurrent modifications
4. **Create new tuple version**:
    - New tuple gets current transaction ID as xmin
    - Old tuple gets current transaction ID as xmax
5. **Choose storage location**:
    - **Same page (HOT update)**: If space available and no indexed columns changed
    - **Different page**: If no space or indexed columns changed
6. **Update indexes**: If indexed columns changed or tuple moved pages

#### HOT (Heap-Only Tuple) Updates

Special optimization when:

- Only non-indexed columns are updated
- Sufficient space exists in the same page

Benefits:

- No index updates required
- Faster execution
- Less I/O overhead

Process:

```
Old Tuple: [xmin=100, xmax=200, data...]
           ↓ (HOT chain pointer)
New Tuple: [xmin=200, xmax=0, data...] (same page)
```

### Delete Process

For: `DELETE FROM employees WHERE id = 123;`

1. **Locate tuple**: Using index or scan
2. **Mark as deleted**: Set xmax to current transaction ID
3. **Tuple remains**: Physical tuple stays in page until VACUUM

#### Soft Delete Concept:

- DELETE doesn't immediately remove tuple
- Tuple marked as deleted with xmax
- Space reclaimed during VACUUM operation

---

## 7. VACUUM and Space Management

### Why VACUUM is Necessary

PostgreSQL's MVCC creates "dead tuples":

- Updated tuples leave old versions behind
- Deleted tuples remain marked but not removed
- Dead tuples consume space and slow scans

### VACUUM Operations

#### Regular VACUUM:

- Marks dead tuple space as reusable
- Updates statistics
- Doesn't return space to OS
- Can run concurrently with other operations

#### VACUUM FULL:

- Rewrites entire table with only live tuples
- Returns space to operating system
- Requires exclusive lock (blocks all operations)
- Much slower but more thorough

### Autovacuum Daemon

Automatically triggers VACUUM based on:

- **autovacuum_vacuum_threshold**: Minimum number of dead tuples
- **autovacuum_vacuum_scale_factor**: Percentage of table size
- **Formula**: threshold + (scale_factor * table_size)

---

## 8. Query Execution Plans and Performance

### Understanding EXPLAIN Output

```sql
EXPLAIN (ANALYZE, BUFFERS) 
SELECT * FROM employees WHERE department = 'Engineering';
```

Sample output:

```
Seq Scan on employees  (cost=0.00..1500.00 rows=50 width=32) 
                       (actual time=0.123..15.456 rows=47 loops=1)
  Filter: (department = 'Engineering'::text)
  Rows Removed by Filter: 9953
  Buffers: shared hit=150 read=50
Planning time: 0.234 ms
Execution time: 15.789 ms
```

#### Key Metrics:

- **cost=0.00..1500.00**: Estimated cost (startup..total)
- **rows=50**: Estimated rows returned
- **actual time**: Real execution time (startup..total)
- **loops=1**: How many times this node executed
- **Buffers hit=150**: Pages found in buffer cache
- **Buffers read=50**: Pages read from disk

---

## 9. PostgreSQL vs MySQL (InnoDB) Architecture Comparison

### Storage Architecture Differences

#### PostgreSQL (Heap Storage):

```
Table Data: [Row1][Row2][Row3][Row4]...
Index: Key1→CTID1, Key2→CTID2, Key3→CTID3...
```

- Data stored in heap files
- Indexes point directly to tuple location (CTID)
- Single lookup from index to data

#### MySQL InnoDB (Clustered Storage):

```
Primary Key B-Tree: Contains actual row data
Secondary Index: Key→Primary Key Value
```

- Data stored in primary key B-tree structure
- Secondary indexes point to primary key
- Double lookup: Secondary Index → Primary Key → Data

### Performance Implications

|Aspect|PostgreSQL|MySQL InnoDB|
|---|---|---|
|**Primary Key Lookups**|Index → Heap (2 I/Os)|Direct from clustered index (1 I/O)|
|**Secondary Index Lookups**|Index → Heap (2 I/Os)|Secondary → Primary → Data (3 I/Os)|
|**Range Scans**|May require random heap access|Sequential in primary key order|
|**Updates**|MVCC versioning|In-place with undo logs|
|**Space Usage**|Higher due to MVCC|Lower, more compact|
|**Concurrent Reads**|Never blocked by writes|May be blocked in some cases|

---

## 10. Advanced Topics and Optimizations

### Statistics and Query Planning

PostgreSQL maintains statistics about your data:

- **pg_stats**: Human-readable statistics view
- **Histogram bounds**: Data distribution information
- **Most common values**: Frequently occurring values
- **Correlation**: Physical vs logical ordering

Update statistics:

```sql
ANALYZE table_name;  -- Update statistics for specific table
ANALYZE;             -- Update all table statistics
```

### Connection Pooling and Process Architecture

PostgreSQL uses a **process-per-connection** model:

- Each client connection gets a dedicated backend process
- Processes don't share memory (except shared buffers)
- High connection overhead compared to thread-based systems

Use connection poolers like PgBouncer:

- Maintains pool of database connections
- Routes client requests to available connections
- Reduces connection overhead

### Monitoring and Troubleshooting

#### Key System Catalogs:

- **pg_stat_activity**: Current database activity
- **pg_stat_user_tables**: Table access statistics
- **pg_stat_user_indexes**: Index usage statistics
- **pg_locks**: Current locks in the system

#### Performance Monitoring Queries:

```sql
-- Find slow queries
SELECT query, mean_time, calls 
FROM pg_stat_statements 
ORDER BY mean_time DESC;

-- Check index usage
SELECT schemaname, tablename, indexname, idx_scan, idx_tup_read
FROM pg_stat_user_indexes
WHERE idx_scan = 0;  -- Unused indexes
```

---

## 11. Disaster Recovery and Data Corruption

### What Happens When Files Are Deleted

If someone accidentally deletes a table file from the filesystem:

1. **Immediate effect**: PostgreSQL doesn't detect the deletion
2. **On access**: Query returns error: `ERROR: could not read block X of relation Y`
3. **Recovery options**:
    - Restore from backup (preferred)
    - Use pg_resetwal (dangerous, may cause data loss)
    - Recreate table structure (data lost)

### Backup Strategies

#### Physical Backups:

- **pg_basebackup**: Full cluster backup
- **File system snapshots**: LVM, ZFS snapshots
- **Continuous archiving**: WAL-E, pgBackRest

#### Logical Backups:

- **pg_dump**: Single database backup
- **pg_dumpall**: All databases backup
- **Custom format**: Compressed, selective restore

---

## 12. When to Choose PostgreSQL vs MySQL

### Choose PostgreSQL when you need:

- **Complex queries**: Advanced SQL features, window functions, CTEs
- **Data integrity**: Strong consistency, foreign keys, constraints
- **Advanced data types**: JSON/JSONB, arrays, geometric types
- **Full-text search**: Built-in text search capabilities
- **Extensibility**: Custom functions, operators, data types
- **ACID compliance**: Strict transaction guarantees

### Choose MySQL (InnoDB) when you need:

- **High write throughput**: Better for insert-heavy workloads
- **Simple replication**: Master-slave setup is straightforward
- **Lower resource usage**: Less memory overhead
- **Wide ecosystem support**: More hosting providers, tools
- **Simpler administration**: Fewer configuration options

### Performance Characteristics

#### PostgreSQL strengths:

- Complex analytical queries
- Concurrent read-heavy workloads
- Applications requiring strong consistency
- Workloads with varied data types

#### MySQL InnoDB strengths:

- Simple OLTP workloads
- Applications with mostly primary key lookups
- High-frequency insert/update operations
- Web applications with simple queries

---

## Conclusion

PostgreSQL's architecture prioritizes data integrity, advanced SQL features, and concurrent access over raw performance. Understanding these internals helps you:

- **Optimize queries**: Know when indexes help and how MVCC affects performance
- **Troubleshoot issues**: Understand what's happening when queries are slow
- **Make architectural decisions**: Choose the right database for your use case
- **Configure properly**: Set appropriate memory, vacuum, and other parameters

The complexity of PostgreSQL's internals reflects its focus on being a feature-rich, reliable database system suitable for complex applications requiring strong consistency and advanced SQL capabilities.