# OLTP vs OLAP Notes

## Table of Contents
1. [What is OLTP?](#-what-is-oltp)
2. [How to Design an OLTP System](#-how-to-design-an-oltp-system)
3. [What is OLAP?](#-what-is-olap)
4. [OLTP vs OLAP - Key Differences](#️-oltp-vs-olap-key-differences)
5. [How to Design an OLAP System](#-how-to-design-an-olap-system)
6. [OLTP vs OLAP Design](#️-oltp-vs-olap-design-core-difference)
7. [Real Data Engineering Flow](#-real-data-engineering-flow-very-important)

---

# 📊 OLTP vs OLAP

## ⚡ What is OLTP?

### 📌 Definition

**OLTP (Online Transaction Processing)** is used for handling **day-to-day transactions**.

👉 **In simple terms:**
Systems where data is continuously **inserted, updated, deleted**.

---

### 🧱 Example

- Banking transactions
- Online shopping orders
- ATM withdrawals

---

### 🔍 Example Table

| Order_ID | Customer | Amount |
|----------|----------|--------|
| 101      | Rahul    | 500    |

👉 Every time a user:
- places order
- updates details

➡️ database changes immediately

---

### ✅ Characteristics of OLTP

- Many small transactions
- Fast inserts/updates
- Highly normalized (3NF)
- Ensures data consistency (ACID)
- Used by applications/users

---

# 🧠 How to DESIGN an OLTP System

👉 **Goal:**
> Avoid redundancy + ensure accuracy + fast writes

---

## 🧱 Step-by-Step OLTP Design

### 1️⃣ Identify Entities (Tables)

**Example:**
- Customers
- Orders
- Products

---

### 2️⃣ Normalize the Data (Very Important)

Use:
- **1NF, 2NF, 3NF**

👉 **Why?**
- Removes redundancy
- Avoids inconsistency

---

### 3️⃣ Define Keys

- **Primary Key** → unique row (Customer_ID)
- **Foreign Key** → relationships

---

### 4️⃣ Split Data into Multiple Tables

#### ❌ Bad Design (Not OLTP)

| Order_ID | Customer_Name | Product | Price |
|----------|---------------|---------|-------|

👉 **Problem:**
- Customer repeated
- Product repeated

#### ✅ Good OLTP Design

**Customers Table**

| Customer_ID | Name  |
|-------------|-------|
| 1           | Rahul |

**Orders Table**

| Order_ID | Customer_ID |
|----------|-------------|
| 101      | 1           |

**Products Table**

| Product_ID | Product_Name |
|------------|--------------|

---

### 5️⃣ Ensure Data Integrity

- Constraints (NOT NULL, UNIQUE)
- Foreign keys

---

## 🎯 Final OLTP Design Idea

✔ Many small tables
✔ Highly normalized
✔ Fast inserts/updates
✔ No duplication

---

# 📊 What is OLAP?

## 📌 Definition

**OLAP (Online Analytical Processing)** is used for **analyzing large amounts of data**.

👉 **In simple terms:**
Systems where we **read and analyze** data, not update it frequently.

---

## 🧱 Example

- Sales reports
- Business dashboards
- Trend analysis

---

## 🔍 Example

👉 **Questions:**
- *"Total sales per month?"*
- *"Top customers?"*

👉 These require:
- Aggregations
- Large data scans

---

## ✅ Characteristics of OLAP

- Mostly read operations
- Complex queries (JOIN, GROUP BY)
- Denormalized (Star Schema)
- Optimized for fast querying
- Used by analysts / business users

---

# ⚖️ OLTP vs OLAP (Key Differences)

| Feature       | OLTP 🟢                    | OLAP 🔵                     |
|---------------|----------------------------|------------------------------|
| **Purpose**   | Transactions               | Analysis                     |
| **Operations** | INSERT, UPDATE, DELETE    | SELECT (read-heavy)          |
| **Data Size** | Small per transaction      | Large historical data        |
| **Design**    | Normalized (3NF)           | Denormalized (Star Schema)   |
| **Users**     | End users (apps)           | Analysts, BI tools           |
| **Speed Focus** | Fast writes              | Fast reads                   |

---

## 🔥 Why This Matters (Very Important)

👉 **In real-world systems:**

- **OLTP** → Source systems
  - e.g., app database (MySQL, PostgreSQL)
- **OLAP** → Data warehouse
  - e.g., Databricks, Snowflake, Redshift

---

# 🧠 How to DESIGN an OLAP System

👉 **Goal:**
> Fast querying + easy analysis

---

## 🧱 Step-by-Step OLAP Design

### 1️⃣ Identify Business Metrics (Facts)

👉 **What do you want to analyze?**

**Examples:**
- Sales amount
- Quantity
- Revenue

---

### 2️⃣ Identify Dimensions

👉 *"By what do you analyze?"*

**Examples:**
- Customer
- Product
- Time
- Location

---

### 3️⃣ Create Fact Table

**📊 Fact Table (Sales)**

| Sale_ID | Customer_ID | Product_ID | Amount |
|---------|-------------|------------|--------|

---

### 4️⃣ Create Dimension Tables

**👤 Customer Dimension**

| Customer_ID | Name | City |
|-------------|------|------|

**📦 Product Dimension**

| Product_ID | Name | Category |
|------------|------|----------|

**📅 Time Dimension**

| Date_ID | Date | Month | Year |
|---------|------|-------|------|

---

### 5️⃣ Use Star Schema ⭐

👉 Fact table in center
👉 Dimensions around it

```
          Customer Dim
                |
Product Dim —— FACT —— Time Dim
                |
          Location Dim
```

---

## 🎯 Final OLAP Design Idea

✔ Few large tables
✔ Denormalized (some repetition OK)
✔ Optimized for SELECT queries
✔ Easy joins

---

# ⚖️ OLTP vs OLAP Design (Core Difference)

| Aspect        | OLTP Design 🟢              | OLAP Design 🔵              |
|---------------|-----------------------------|------------------------------|
| **Goal**      | Accuracy + transactions     | Fast analytics               |
| **Design**    | Normalized (3NF)            | Denormalized (Star Schema)   |
| **Tables**    | Many small tables           | Few large tables             |
| **Data**      | Current/live                | Historical                   |
| **Optimization** | Write (INSERT/UPDATE)    | Read (SELECT)                |

---

# 🔥 Real Data Engineering Flow (Very Important)

👉 **In your Databricks/Azure world:**

1. **OLTP** → App DB (MySQL/Postgres)
2. **Data Ingestion** → ADF / Kafka
3. **Storage** → Data Lake (S3/ADLS)
4. **OLAP** → Delta Tables / Warehouse
5. **Reporting** → Power BI

```
OLTP (App DB) → Ingestion (ADF/Kafka) → Data Lake (S3/ADLS) → OLAP (Delta/Warehouse) → Power BI
```

---

## 💡 Simple Analogy

- **OLTP** → Like writing notes in a notebook (fast updates)
- **OLAP** → Like reading summary notes for exam (fast understanding)

---

## 🎯 Final One-Line Summary

> - **OLTP Design** = Normalize for correctness
> - **OLAP Design** = Denormalize for speed
