# PostgreSQL Table Partitioning Example

This guide demonstrates how to implement table partitioning in PostgreSQL to improve query performance. We will use **range partitioning** based on a date column to partition a `sales` table.

## Table of Contents

- [Overview](#overview)
- [Step 1: Create the Main Table](#step-1-create-the-main-table)
- [Step 2: Create Partitions](#step-2-create-partitions)
- [Step 3: Insert Data](#step-3-insert-data)
- [Step 4: Querying the Partitioned Table](#step-4-querying-the-partitioned-table)
- [Step 5: Add Indexes (Optional)](#step-5-add-indexes-optional)
- [Conclusion](#conclusion)

---

## Overview

Partitioning in PostgreSQL splits large tables into smaller, manageable pieces (partitions), leading to faster query performance. This example partitions the `sales` table by `sale_date` using **range partitioning**.

---

## Step 1: Create the Main Table

We first create a partitioned `sales` table.

```sql
CREATE TABLE sales (
    id SERIAL PRIMARY KEY,
    sale_date DATE NOT NULL,
    customer_id INT NOT NULL,
    amount NUMERIC
) PARTITION BY RANGE (sale_date);
```

This defines the `sales` table, partitioned by the `sale_date` column.

---

## Step 2: Create Partitions

Next, we create partitions for specific date ranges (e.g., years).

```sql
CREATE TABLE sales_2022 PARTITION OF sales
FOR VALUES FROM ('2022-01-01') TO ('2023-01-01');

CREATE TABLE sales_2023 PARTITION OF sales
FOR VALUES FROM ('2023-01-01') TO ('2024-01-01');
```

Two partitions are created: one for sales in 2022 and another for 2023.

---

## Step 3: Insert Data

Inserting data into the main `sales` table will automatically place it into the correct partition.

```sql
INSERT INTO sales (sale_date, customer_id, amount)
VALUES ('2023-06-15', 101, 500);

INSERT INTO sales (sale_date, customer_id, amount)
VALUES ('2022-11-12', 102, 1000);
```

PostgreSQL automatically inserts the data into the corresponding partition based on `sale_date`.

---

## Step 4: Querying the Partitioned Table

When querying the partitioned table, PostgreSQL scans only the relevant partitions.

```sql
SELECT * FROM sales
WHERE sale_date BETWEEN '2023-01-01' AND '2023-12-31';
```

This query scans only the `sales_2023` partition, improving query performance.

---

## Step 5: Add Indexes (Optional)

You can add indexes to individual partitions to optimize queries further.

```sql
CREATE INDEX idx_sales_2023_customer_id ON sales_2023 (customer_id);
```

This index will speed up queries filtering by `customer_id` for sales in 2023.

---

## Conclusion

Partitioning in PostgreSQL enhances query performance by reducing the data scanned. It's especially useful for time-based data like logs, sales, or events. By using partitioning, you can manage large tables more efficiently.

---
## References
- https://www.postgresql.org/docs/current/ddl-partitioning.html
