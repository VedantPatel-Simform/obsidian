# Database Interview Questions and Answers

## Basic Level Questions

### 1. What is a database?

**Answer:** A database is an organized collection of structured data stored electronically in a computer system. It allows for efficient storage, retrieval, modification, and management of data through a Database Management System (DBMS).

### 2. What is DBMS?

**Answer:** Database Management System (DBMS) is software that provides an interface between users and databases. It manages data storage, retrieval, security, backup, and ensures data integrity. Examples include MySQL, PostgreSQL, Oracle, SQL Server.

### 3. What are the types of databases?

**Answer:**

- **Relational Databases:** Use tables with rows and columns (MySQL, PostgreSQL, Oracle)
- **NoSQL Databases:** Non-relational, flexible schema (MongoDB, Cassandra, Redis)
- **Object-oriented Databases:** Store data as objects
- **Graph Databases:** Use nodes and edges (Neo4j)
- **Column-family:** Store data in column families (HBase)

### 4. What is SQL?

**Answer:** Structured Query Language (SQL) is a standard programming language designed for managing and manipulating relational databases. It's used for querying, inserting, updating, and deleting data.

### 5. What are the different types of SQL statements?

**Answer:**

- **DDL (Data Definition Language):** CREATE, ALTER, DROP, TRUNCATE
- **DML (Data Manipulation Language):** SELECT, INSERT, UPDATE, DELETE
- **DCL (Data Control Language):** GRANT, REVOKE
- **TCL (Transaction Control Language):** COMMIT, ROLLBACK, SAVEPOINT

## Intermediate Level Questions

### 6. What is normalization? Explain different normal forms.

**Answer:** Normalization is the process of organizing data to minimize redundancy and dependency.

**Normal Forms:**

- **1NF:** Each column contains atomic values, no repeating groups
- **2NF:** 1NF + no partial dependencies on composite primary keys
- **3NF:** 2NF + no transitive dependencies
- **BCNF:** 3NF + every determinant is a candidate key
- **4NF:** BCNF + no multi-valued dependencies
- **5NF:** 4NF + no join dependencies

### 7. What is denormalization and when is it used?

**Answer:** Denormalization is the process of adding redundant data to improve read performance by reducing the number of joins required. It's used in:

- Data warehousing
- Read-heavy applications
- When query performance is more critical than storage space
- OLAP systems

### 8. Explain ACID properties.

**Answer:**

- **Atomicity:** All operations in a transaction succeed or fail together
- **Consistency:** Database remains in a valid state before and after transaction
- **Isolation:** Concurrent transactions don't interfere with each other
- **Durability:** Committed changes are permanently saved

### 9. What are database indexes and their types?

**Answer:** Indexes are data structures that improve query performance by creating shortcuts to data.

**Types:**

- **Clustered Index:** Physically reorders table data
- **Non-clustered Index:** Separate structure pointing to data rows
- **Unique Index:** Ensures uniqueness and improves performance
- **Composite Index:** Built on multiple columns
- **Partial Index:** Built on subset of data meeting certain conditions

### 10. What is the difference between DELETE, TRUNCATE, and DROP?

**Answer:**

- **DELETE:** Removes specific rows, can use WHERE clause, slower, can be rolled back
- **TRUNCATE:** Removes all rows, faster, minimal logging, can be rolled back in some DBMS
- **DROP:** Removes entire table structure and data, cannot be rolled back

## Advanced Level Questions

### 11. Explain different types of joins with examples.

**Answer:**

```sql
-- INNER JOIN: Returns matching records from both tables
SELECT e.name, d.department_name 
FROM employees e 
INNER JOIN departments d ON e.dept_id = d.id;

-- LEFT JOIN: All records from left table + matching from right
SELECT e.name, d.department_name 
FROM employees e 
LEFT JOIN departments d ON e.dept_id = d.id;

-- RIGHT JOIN: All records from right table + matching from left
SELECT e.name, d.department_name 
FROM employees e 
RIGHT JOIN departments d ON e.dept_id = d.id;

-- FULL OUTER JOIN: All records from both tables
SELECT e.name, d.department_name 
FROM employees e 
FULL OUTER JOIN departments d ON e.dept_id = d.id;

-- CROSS JOIN: Cartesian product of both tables
SELECT e.name, d.department_name 
FROM employees e 
CROSS JOIN departments d;
```

### 12. What are database locks and their types?

**Answer:** Locks prevent concurrent access conflicts.

**Types:**

- **Shared Lock:** Multiple transactions can read simultaneously
- **Exclusive Lock:** Only one transaction can modify
- **Row-level Lock:** Locks specific rows
- **Table-level Lock:** Locks entire table
- **Intent Lock:** Indicates intention to acquire locks at lower level

### 13. Explain database isolation levels.

**Answer:**

- **READ UNCOMMITTED:** Lowest isolation, allows dirty reads
- **READ COMMITTED:** Prevents dirty reads, allows non-repeatable reads
- **REPEATABLE READ:** Prevents dirty and non-repeatable reads, allows phantom reads
- **SERIALIZABLE:** Highest isolation, prevents all anomalies

### 14. What is database sharding?

**Answer:** Sharding is horizontal partitioning where data is distributed across multiple database instances. Each shard contains a subset of data, improving performance and scalability.

**Types:**

- **Range-based:** Data divided by value ranges
- **Hash-based:** Data distributed using hash function
- **Directory-based:** Lookup service determines shard location

### 15. Explain CAP theorem and its implications.

**Answer:** CAP theorem states that a distributed database can guarantee only two of three properties:

- **Consistency:** All nodes see the same data simultaneously
- **Availability:** System remains operational
- **Partition tolerance:** System continues despite network failures

Most distributed systems choose between CP (consistency + partition tolerance) or AP (availability + partition tolerance).

## Query Optimization Questions

### 16. How do you optimize a slow-running query?

**Answer:**

1. **Analyze execution plan** to identify bottlenecks
2. **Add appropriate indexes** on frequently queried columns
3. **Rewrite queries** to avoid unnecessary operations
4. **Use LIMIT/TOP** to reduce result set size
5. **Optimize JOIN conditions** and order
6. **Avoid SELECT *** and specify needed columns
7. **Use EXISTS instead of IN** for subqueries
8. **Consider query hints** when appropriate

### 17. What is query execution plan?

**Answer:** Query execution plan shows how the database engine executes a query, including:

- Order of operations
- Join algorithms used
- Index usage
- Estimated costs and row counts
- Identifies performance bottlenecks

### 18. Explain the difference between UNION and UNION ALL.

**Answer:**

- **UNION:** Combines results and removes duplicates, slower
- **UNION ALL:** Combines results without removing duplicates, faster

```sql
-- UNION removes duplicates
SELECT name FROM employees WHERE dept = 'IT'
UNION
SELECT name FROM employees WHERE salary > 50000;

-- UNION ALL keeps duplicates
SELECT name FROM employees WHERE dept = 'IT'
UNION ALL
SELECT name FROM employees WHERE salary > 50000;
```

## NoSQL and Modern Database Questions

### 19. When would you choose NoSQL over SQL databases?

**Answer:** Choose NoSQL when:

- **Flexible schema** requirements
- **Horizontal scaling** needs
- **Big data** and high velocity applications
- **Document or graph** data models fit better
- **High availability** over strict consistency
- **Rapid development** with changing requirements

### 20. Explain different types of NoSQL databases.

**Answer:**

- **Document:** MongoDB, CouchDB - store data as documents
- **Key-Value:** Redis, DynamoDB - simple key-value pairs
- **Column-family:** Cassandra, HBase - column-oriented storage
- **Graph:** Neo4j, Amazon Neptune - nodes and relationships

## Performance and Monitoring

### 21. What are database performance metrics to monitor?

**Answer:**

- **Query response time** and throughput
- **CPU and memory utilization**
- **Disk I/O** and storage usage
- **Connection counts** and pool usage
- **Lock waits** and deadlocks
- **Index usage** and fragmentation
- **Replication lag** (if applicable)

### 22. How do you handle database deadlocks?

**Answer:**

1. **Detect deadlocks** through monitoring
2. **Design transactions** to access resources in consistent order
3. **Keep transactions short** and fast
4. **Use appropriate isolation levels**
5. **Implement retry logic** with exponential backoff
6. **Consider deadlock timeouts**

## Practical Scenario Questions

### 23. Design a database schema for an e-commerce application.

**Answer:**

```sql
-- Users table
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Products table
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    stock_quantity INT DEFAULT 0,
    category_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Orders table
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    total_amount DECIMAL(10,2) NOT NULL,
    status ENUM('pending', 'confirmed', 'shipped', 'delivered') DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- Order items table
CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

### 24. How would you handle a database migration with zero downtime?

**Answer:**

1. **Blue-green deployment** with database replication
2. **Rolling updates** with backward-compatible schema changes
3. **Feature flags** to control new functionality
4. **Database versioning** and migration scripts
5. **Comprehensive testing** in staging environment
6. **Rollback plan** in case of issues
7. **Monitor application** during migration

## Tips for Database Interviews

1. **Understand the basics thoroughly** - ACID, normalization, indexes
2. **Practice writing SQL queries** for complex scenarios
3. **Know your specific database system** (MySQL, PostgreSQL, etc.)
4. **Understand performance optimization** techniques
5. **Be familiar with both SQL and NoSQL** concepts
6. **Prepare for system design** questions involving databases
7. **Know about backup, recovery,** and high availability
8. **Understand transaction management** and concurrency control