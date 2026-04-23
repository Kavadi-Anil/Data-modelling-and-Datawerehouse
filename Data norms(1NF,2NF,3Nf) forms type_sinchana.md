# Data Normalization Notes (Extended: 1NF → 2NF → 3NF)

## Table of Contents
1. [What is Data Normalization?](#-what-is-data-normalization)
2. [Why Do We Use Normalization?](#-why-do-we-use-it)
3. [Starting Example (Unnormalized Table)](#-example-starting-table)
4. [First Normal Form (1NF)](#-1️⃣-first-normal-form-1nf)
5. [Partial Dependency Explained](#️-what-is-wrong-in-1nf)
6. [Second Normal Form (2NF)](#-2️⃣-second-normal-form-2nf)
7. [Transitive Dependency Explained](#-transitive-dependency)
8. [Third Normal Form (3NF)](#-3️⃣-third-normal-form-3nf)
9. [Final Summary](#-final-summary)

---

# 📌 What is Data Normalization?

## ✅ Definition

**Data normalization** is the process of organizing data into multiple related tables to:

✔ reduce data redundancy
✔ avoid data inconsistency
✔ improve data integrity

---

## 🧠 Simple Understanding

👉 Instead of storing everything in one big messy table, we split data into **smaller, well-structured tables**.

---

## 📊 Example

### ❌ Before Normalization (Bad Design)

| Student_ID | Name    | Course_ID | Course_Name | Instructor | Dept_ID | Dept_Name |
|------------|---------|-----------|-------------|------------|---------|-----------|
| 101        | Rahul   | C1        | SQL         | Amit       | D1      | Data      |
| 101        | Rahul   | C2        | Python      | Neha       | D2      | AI        |
| 102        | Anjali  | C1        | SQL         | Amit       | D1      | Data      |

### ❗ Problems

- Same data repeated (Rahul, SQL, Dept info) → **Redundancy**
- Updating course/department details is difficult → **Update issue**
- Can lead to wrong data → **Inconsistency**

---

## ✅ After Normalization (Good Design)

**Students Table**

| Student_ID | Name    |
|------------|---------|
| 101        | Rahul   |
| 102        | Anjali  |

**Courses Table**

| Course_ID | Course_Name | Instructor | Dept_ID |
|-----------|-------------|------------|---------|
| C1        | SQL         | Amit       | D1      |
| C2        | Python      | Neha       | D2      |

**Departments Table**

| Dept_ID | Dept_Name |
|---------|-----------|
| D1      | Data      |
| D2      | AI        |

**Enrollment Table**

| Student_ID | Course_ID |
|------------|-----------|
| 101        | C1        |
| 101        | C2        |
| 102        | C1        |

---

### ✔ What We Achieved

- No duplicate data
- Easy updates
- Better structure

---

## 🧱 Levels of Normalization

- **1NF** → Remove multiple values in a cell
- **2NF** → Remove partial dependency
- **3NF** → Remove transitive dependency

---

## 🎯 Why Do We Use It?

- Prevent duplication
- Maintain consistency
- Reduce storage waste
- Improve data quality

---

## 💡 One-Line Summary

> **Normalization** = splitting data into proper tables to avoid duplication and errors

---

# DATA NORMALIZATION NOTES (1NF → 2NF → 3NF)

## 📌 Example (Starting Table)

### ❌ Unnormalized Table

| Student_ID | Student_Name | Course_ID | Course_Name   | Instructor | Dept_ID | Dept_Name |
|------------|--------------|-----------|---------------|------------|---------|-----------|
| 101        | Rahul        | C1, C2    | SQL, Python   | Amit       | D1      | Data      |
| 102        | Anjali       | C1        | SQL           | Amit       | D1      | Data      |

### 🔑 Dependencies

- `Student_ID → Student_Name`
- `Course_ID → Course_Name`
- `Course_ID → Instructor`
- `Course_ID → Dept_ID`
- `Dept_ID → Dept_Name`

---

# 📘 1️⃣ FIRST NORMAL FORM (1NF)

## ✅ Definition

A table is in **1NF** if:
- Each column contains **atomic values**
- No multiple values in a single cell

---

## ✅ Convert to 1NF

| Student_ID | Student_Name | Course_ID | Course_Name | Instructor | Dept_ID | Dept_Name |
|------------|--------------|-----------|-------------|------------|---------|-----------|
| 101        | Rahul        | C1        | SQL         | Amit       | D1      | Data      |
| 101        | Rahul        | C2        | Python      | Neha       | D2      | AI        |
| 102        | Anjali       | C1        | SQL         | Amit       | D1      | Data      |

### 🔑 Keys & Columns

- **Primary Key (PK):** `(Student_ID, Course_ID)`

### 📌 Key vs Non-Key

| Type    | Columns                                                       |
|---------|---------------------------------------------------------------|
| Key     | Student_ID, Course_ID                                         |
| Non-Key | Student_Name, Course_Name, Instructor, Dept_ID, Dept_Name     |

### 🔗 Dependencies

- `Student_ID → Student_Name`
- `Course_ID → Course_Name, Instructor, Dept_ID`
- `Dept_ID → Dept_Name`

---

## ⚠️ WHAT IS WRONG IN 1NF

### 🔴 Partial Dependency

#### ✅ Definition

A non-key column depends on **part of composite key**, not full key.

#### ❌ Identify Partial Dependency

- `Student_ID → Student_Name` ❌
- `Course_ID → Course_Name` ❌
- `Course_ID → Instructor` ❌
- `Course_ID → Dept_ID` ❌

---

## ❌ Problems (Explained Clearly)

### 1. Data Redundancy

- Rahul is stored multiple times
- Course details (SQL, Amit, D1) repeated

👉 Wastes storage and increases duplication

### 2. Update Anomaly

- If Instructor of SQL changes (Amit → Raj)

👉 Need to update multiple rows
👉 If one row is missed → **inconsistent data**

### 3. Insert Anomaly

- Cannot add a new course unless a student exists

👉 Because Student_ID is part of PK

### 4. Delete Anomaly

- If we delete: `(102, C1) → Anjali record`

👉 If it was last record of C1
👉 Course details (SQL, Amit, D1) may be lost

---

## 🎯 WHY MOVE TO 2NF

👉 **Remove partial dependency**

---

# 📘 2️⃣ SECOND NORMAL FORM (2NF)

## 📌 INPUT (1NF TABLE)

| Student_ID | Student_Name | Course_ID | Course_Name | Instructor | Dept_ID | Dept_Name |
|------------|--------------|-----------|-------------|------------|---------|-----------|
| 101        | Rahul        | C1        | SQL         | Amit       | D1      | Data      |
| 101        | Rahul        | C2        | Python      | Neha       | D2      | AI        |
| 102        | Anjali       | C1        | SQL         | Amit       | D1      | Data      |

---

## ✅ Definition

A table is in **2NF** if:
- It is in **1NF**
- **No partial dependency** exists

---

## ✅ Convert to 2NF

### 📂 Students

| Student_ID (PK) | Student_Name |
|-----------------|--------------|
| 101             | Rahul        |
| 102             | Anjali       |

### 📂 Courses

| Course_ID (PK) | Course_Name | Instructor | Dept_ID | Dept_Name |
|----------------|-------------|------------|---------|-----------|
| C1             | SQL         | Amit       | D1      | Data      |
| C2             | Python      | Neha       | D2      | AI        |

### 📂 Enrollment

| Student_ID (PK, FK) | Course_ID (PK, FK) |
|---------------------|--------------------|
| 101                 | C1                 |
| 101                 | C2                 |
| 102                 | C1                 |

---

## 🎯 What We Fixed

- Student details separated ✅
- Course details separated ✅
- No partial dependency ✅

---

## ⚠️ NOW THE IMPORTANT PART

👉 **Transitive Dependency** is visible in Courses table

---

### 🔴 Transitive Dependency

#### ✅ Definition

A non-key column depends on **another non-key column** instead of directly on primary key.

👉 **A → B → C**

---

### 📌 Courses Table (2NF)

| Course_ID (PK) | Course_Name | Instructor | Dept_ID | Dept_Name |
|----------------|-------------|------------|---------|-----------|
| C1             | SQL         | Amit       | D1      | Data      |
| C2             | Python      | Neha       | D2      | AI        |

### 🔗 Dependencies

- `Course_ID → Dept_ID`
- `Dept_ID → Dept_Name`

---

### ❌ Problem (Explained Clearly)

👉 `Dept_Name` is stored in Courses table

But:
- `Dept_Name` depends on `Dept_ID`
- `Dept_ID` depends on `Course_ID`

👉 So `Dept_Name` is **indirectly dependent**

**Problems caused:**

#### 1. Data Redundancy

Dept_Name repeated for every course in same department

#### 2. Update Anomaly

If Dept_Name changes (Data → Data Science)

👉 Must update all rows
👉 Missing one → inconsistency

#### 3. Insert Anomaly

Cannot add new department without course

#### 4. Delete Anomaly

Deleting last course of a department removes department info

---

## 🎯 WHY MOVE TO 3NF

👉 **Remove transitive dependency**

---

# 📘 3️⃣ THIRD NORMAL FORM (3NF)

## 📌 INPUT (2NF Courses Table)

| Course_ID (PK) | Course_Name | Instructor | Dept_ID | Dept_Name |
|----------------|-------------|------------|---------|-----------|
| C1             | SQL         | Amit       | D1      | Data      |
| C2             | Python      | Neha       | D2      | AI        |

---

## ✅ Definition

A table is in **3NF** if:
- It is in **2NF**
- **No transitive dependency** exists

👉 Non-key columns depend only on **primary key**

---

## ✅ Convert to 3NF

### 📂 Courses

| Course_ID (PK) | Course_Name | Instructor | Dept_ID (FK) |
|----------------|-------------|------------|--------------|
| C1             | SQL         | Amit       | D1           |
| C2             | Python      | Neha       | D2           |

### 📂 Departments

| Dept_ID (PK) | Dept_Name |
|--------------|-----------|
| D1           | Data      |
| D2           | AI        |

---

## 🎯 What We Fixed in 3NF

- Removed indirect dependency ✅
- Dept_Name moved to separate table ✅
- No redundancy ✅
- Better data integrity ✅

---

# 🧠 FINAL SUMMARY

| Normal Form | Fixes                    |
|-------------|--------------------------|
| **1NF**     | Multi-values             |
| **2NF**     | Partial dependency       |
| **3NF**     | Transitive dependency    |

---

## 🔥 ONE-LINE MEMORY

> - **1NF** → One value per cell
> - **2NF** → Full key dependency
> - **3NF** → No indirect dependency
