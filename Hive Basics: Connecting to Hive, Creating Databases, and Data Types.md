# Hive Basics: Connecting to Hive, Creating Databases, and Data Types

## Introduction

Before working with data in Hive, we need to understand the basic operations that form the foundation of every Hive project. These include:

* Connecting to Hive
* Creating and using databases
* Understanding Hive data types
* Preparing for table creation

These concepts are equivalent to learning how to create a database and define columns before storing data in any relational database system.

---

# Connecting to Hive

## What is Beeline?

Beeline is a **command-line client** used to interact with Hive.

It allows users to:

* Connect to Hive Server
* Execute Hive queries
* Create databases and tables
* Load and analyze data

Think of Beeline as Hive's version of SQL command-line tools.

### Why Do We Need Beeline?

Hive runs as a service in the Hadoop ecosystem. To communicate with Hive, users need an interface that can send queries and display results.

Beeline acts as that communication bridge.

```text
User
  ↓
Beeline
  ↓
Hive Server
  ↓
Hadoop
```

Without Beeline, users would have no direct way to execute Hive commands from the terminal.

---

## Starting Beeline

The transcript demonstrates connecting to Hive through a terminal.

General command:

```sql
beeline
```

In enterprise environments, the connection often includes the Hive server URL:

```sql
beeline -u jdbc:hive2://hostname:10000
```

### What Happens Internally?

When Beeline starts:

1. It establishes a connection with HiveServer2.
2. HiveServer2 authenticates the user.
3. A session is created.
4. The user can execute HQL commands.

---

# Hive Database

## What is a Database in Hive?

A database in Hive is a logical container used to organize tables.

It helps separate data belonging to different projects, departments, or applications.

Example:

```text
Sales Database
    ├── customers
    ├── orders
    └── products

HR Database
    ├── employees
    ├── payroll
    └── attendance
```

A database does not store actual data itself.

Instead, it organizes related tables.

---

# Creating a Database

## Syntax

```sql
CREATE DATABASE database_name;
```

### Example

```sql
CREATE DATABASE demo_01;
```

### What This Command Does

When executed:

1. Hive creates metadata for the database.
2. A directory is created in HDFS.
3. The database becomes available for storing tables.

Example location:

```text
/user/hive/warehouse/demo_01.db
```

---

## Why Create Databases?

Databases help:

* Organize tables
* Improve manageability
* Separate business domains
* Avoid naming conflicts

Without databases, thousands of tables would exist in a single location.

---

# Using a Database

After creating a database, we usually switch into it.

## Syntax

```sql
USE database_name;
```

### Example

```sql
USE demo_01;
```

### Purpose

Once selected, all future tables will be created inside that database until another database is chosen.

Example:

```sql
USE demo_01;

CREATE TABLE employee(...);
```

The table employee will belong to demo_01.

---

# IF NOT EXISTS Clause

## Problem

Suppose a database already exists.

Running:

```sql
CREATE DATABASE demo_01;
```

again may produce an error.

To avoid this, Hive provides a safety mechanism.

---

## Syntax

```sql
CREATE DATABASE IF NOT EXISTS database_name;
```

### Example

```sql
CREATE DATABASE IF NOT EXISTS demo_01;
```

### Purpose

Hive checks:

```text
Does database exist?
        │
   ┌────┴────┐
   │         │
 Yes       No
   │         │
Ignore    Create
```

This prevents duplicate database creation errors.

---

## Why Is It Useful?

Very useful in:

* Production deployments
* Automated scripts
* Data pipelines
* Repeated executions

A script can run multiple times without failing.

---

# Data Types in Hive

## Why Data Types Matter

Whenever data is stored in a table, Hive needs to know what kind of information is being stored.

Examples:

| Data       | Data Type |
| ---------- | --------- |
| 100        | INT       |
| 3.14       | DOUBLE    |
| John       | STRING    |
| TRUE       | BOOLEAN   |
| 2025-06-18 | DATE      |

Data types help Hive:

* Store data efficiently
* Validate values
* Optimize processing
* Reduce storage requirements

---

# Categories of Hive Data Types

Hive data types are broadly divided into two groups:

```text
Hive Data Types
      │
 ┌────┴─────┐
 │          │
Primitive  Complex
```

The transcript currently discusses **Primitive Data Types**.

---

# Primitive Data Types

Primitive data types store single values.

Examples:

```text
10
25.6
Hello
TRUE
2025-06-18
```

These are similar to the basic data types available in relational databases.

Hive groups primitive types into several categories.

---

# 1. Numeric Data Types

Numeric types store numbers.

### Common Numeric Types

| Type     | Description             |
| -------- | ----------------------- |
| TINYINT  | Very small integers     |
| SMALLINT | Small integers          |
| INT      | Standard integers       |
| BIGINT   | Large integers          |
| FLOAT    | Decimal values          |
| DOUBLE   | High-precision decimals |

### Examples

```sql
age INT
```

```sql
salary DOUBLE
```

```sql
marks FLOAT
```

### Usage

Used for:

* IDs
* Counts
* Measurements
* Financial values

---

# 2. Date and Time Data Types

These store date-related information.

### Common Types

| Type      | Purpose              |
| --------- | -------------------- |
| DATE      | Stores date          |
| TIMESTAMP | Stores date and time |

### Examples

```sql
joining_date DATE
```

```sql
login_time TIMESTAMP
```

### Usage

Useful for:

* Transaction records
* Attendance systems
* Log analysis
* Event tracking

---

# 3. String Data Types

String types store textual information.

### Types

| Type    | Description                     |
| ------- | ------------------------------- |
| STRING  | Variable-length text            |
| CHAR    | Fixed-length text               |
| VARCHAR | Variable-length text with limit |

### Examples

```sql
name STRING
```

```sql
gender CHAR(1)
```

```sql
email VARCHAR(100)
```

### Usage

Used for:

* Names
* Addresses
* Product descriptions
* Comments

---

# 4. Miscellaneous Data Types

These handle special-purpose values.

### BOOLEAN

Stores:

```text
TRUE
FALSE
```

Example:

```sql
is_active BOOLEAN
```

---

### BINARY

Stores raw binary data.

Example:

```sql
photo BINARY
```

Used for:

* Images
* Files
* Encoded data

---

# Why Choosing Correct Data Types Is Important

Selecting the correct data type:

### Improves Performance

Smaller types require less memory.

### Reduces Storage

Efficient storage lowers disk usage.

### Increases Data Quality

Prevents invalid data from being stored.

### Helps Query Optimization

Hive can process data more efficiently.

---

# Interview Questions

### What is Beeline?

Beeline is a command-line client used to connect and interact with Hive.

---

### Why do we use CREATE DATABASE?

To create a logical container for organizing Hive tables.

---

### What is the purpose of IF NOT EXISTS?

It prevents errors when a database already exists.

---

### What are Primitive Data Types?

Basic data types that store single values such as integers, strings, dates, and booleans.

---

### Difference between STRING and VARCHAR?

STRING has no practical length restriction, whereas VARCHAR has a defined maximum length.

---

# Quick Revision

* Beeline is Hive's command-line interface.
* Hive databases organize tables logically.
* `CREATE DATABASE` creates a new database.
* `USE database_name` selects a database.
* `IF NOT EXISTS` prevents duplicate creation errors.
* Hive data types are divided into Primitive and Complex types.
* Primitive types include Numeric, Date-Time, String, and Miscellaneous categories.
* Choosing the correct data type improves performance and storage efficiency.

---

# One-Line Recall

**Hive uses Beeline for interaction, databases for organizing tables, and data types to define how information is stored and processed within the Hadoop ecosystem.**
