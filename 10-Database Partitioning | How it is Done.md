# Data Partitioning

**Data partitioning** means dividing a large table into smaller, manageable parts inside the **SAME database server**.

```
+-------------------------------------------------------------+
|                      Database Server                        |
|                                                             |
|   +-----------------------------------------------------+   |
|   |             Large Table (e.g., 1B Rows)             |   |
|   +-----------------------------------------------------+   |
|                              │                              |
|                              ▼                              |
|   ┌──────────────┬──────────────────┬───────────────────┐   |
|   │ Partition 1  │   Partition 2    │   Partition 3     │   |
|   │ (e.g., 2024) │   (e.g., 2025)   │   (e.g., 2026)    │   |
|   └──────────────┴──────────────────┴───────────────────┘   |
+-------------------------------------------------------------+

```

### Purpose

* **Faster queries**
* **Better organization**
* **Reduced scanning**
* **Easier maintenance**

---

## Types of Data Partitioning

### 1. Horizontal Partitioning

* **Rows are divided.**
* **Columns remain the same.**

```
Original Table (Columns: A, B)
+-------+-------+
| Col A | Col B |
+-------+-------+
| Row 1 | Data  |
| Row 2 | Data  |
+-------+-------+
| Row 3 | Data  |
| Row 4 | Data  |
+-------+-------+
       │
       ▼
Partition 1 (Rows 1–2)      Partition 2 (Rows 3–4)
+-------+-------+           +-------+-------+
| Col A | Col B |           | Col A | Col B |
+-------+-------+           +-------+-------+
| Row 1 | Data  |           | Row 3 | Data  |
| Row 2 | Data  |           | Row 4 | Data  |
+-------+-------+           +-------+-------+

```

### 2. Vertical Partitioning

* **Columns are divided.**
* **Rows remain the same.**
* Frequently used columns are separated from heavy columns.

```
Original Table
+----+----------+---------------------+
| ID | Username | Heavy_Profile_Blob  |
+----+----------+---------------------+
| 1  | alice    | 0xDEADBEEF...       |
| 2  | bob      | 0xCAFEBABE...       |
+----+----------+---------------------+
       │
       ▼
Partition 1 (Frequent)       Partition 2 (Heavy)
+----+----------+            +----+---------------------+
| ID | Username |            | ID | Heavy_Profile_Blob  |
+----+----------+            +----+---------------------+
| 1  | alice    |            | 1  | 0xDEADBEEF...       |
| 2  | bob      |            | 2  | 0xCAFEBABE...       |
+----+----------+            +----+---------------------+

```

---

### Partitioning Strategies & SQL Examples

| Partitioning Strategy | Description | Key Concept / Routing Logic |
| --- | --- | --- |
| **Range Partitioning** | Data divided according to ranges. | `Value < Threshold` (e.g., Date/Year intervals) |
| **List Partitioning** | Partitioning based on predefined categories. | `Value IN ('A', 'B', 'C')` (e.g., Country, Region) |
| **Hash Partitioning** | Database applies a mathematical hash function. | `Hash(Key) % N` (e.g., User ID distribution) |

---

### SQL Syntax Examples

#### 1. Range Partitioning

```sql
CREATE TABLE Orders (
    OrderID INT,
    CustomerID INT,
    OrderDate DATE
)
PARTITION BY RANGE (YEAR(OrderDate)) (
    PARTITION p24 VALUES LESS THAN (2025),
    PARTITION p25 VALUES LESS THAN (2026),
    PARTITION p26 VALUES LESS THAN (2027)
);

```

#### 2. List Partitioning

```sql
CREATE TABLE NetflixUsers (
    UserID INT,
    Name VARCHAR(50),
    Country VARCHAR(30)
)
PARTITION BY LIST (Country) (
    PARTITION India VALUES IN ('India'),
    PARTITION USA VALUES IN ('USA'),
    PARTITION Japan VALUES IN ('Japan')
);

```

#### 3. Hash Partitioning

```sql
CREATE TABLE Users (
    UserID INT,
    Name VARCHAR(50),
    Email VARCHAR(100),
    City VARCHAR(50)
)
PARTITION BY HASH(UserID)
PARTITIONS 4;

```
