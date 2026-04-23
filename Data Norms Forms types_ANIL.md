# Data Normalization Notes (1NF, 2NF, 3NF)

## Table of Contents
1. [What is Data Normalization?](#-what-is-data-normalization)
2. [Why Use Normalization?](#-why-do-we-use-it)
3. [Normal Forms Overview (1NF, 2NF, 3NF)](#-what-are-1nf-2nf-3nf)
4. [Step-by-Step Example](#-lets-take-one-example-and-fix-it-step-by-step)
5. [Deep Dive: 1NF](#-1️⃣-first-normal-form-1nf)
6. [Deep Dive: 2NF](#-2️⃣-second-normal-form-2nf)
7. [Deep Dive: 3NF](#-3️⃣-third-normal-form-3nf)
8. [Final Summary](#-final-understanding)

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

| Student_ID | Name   | Course  | Instructor |
|------------|--------|---------|------------|
| 101        | Rahul  | SQL     | Amit       |
| 101        | Rahul  | Python  | Neha       |

### ❗ Problems

- Same data repeated (Rahul) → **Redundancy**
- Updating name is difficult → **Update issue**
- Can lead to wrong data → **Inconsistency**

---

### ✅ After Normalization (Good Design)

**Students Table**

| Student_ID | Name  |
|------------|-------|
| 101        | Rahul |

**Courses Table**

| Course  | Instructor |
|---------|------------|
| SQL     | Amit       |
| Python  | Neha       |

**Enrollment Table**

| Student_ID | Course  |
|------------|---------|
| 101        | SQL     |
| 101        | Python  |

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

# 📌 What are 1NF, 2NF, 3NF?

These are **Normal Forms** used in Relational Data Modeling to:

- ✅ Remove redundancy
- ✅ Avoid inconsistency
- ✅ Improve data structure

---

## 🧠 Simple Idea

Normalization happens in steps:

- **1NF** → Clean structure
- **2NF** → Remove partial dependency
- **3NF** → Remove indirect dependency

---

# 📊 Let's Take ONE Example and Fix It Step by Step

### ❌ Unnormalized Table (Bad Design)

| Student_ID | Name    | Courses       | Instructor |
|------------|---------|---------------|------------|
| 101        | Rahul   | SQL, Python   | Amit       |
| 102        | Anjali  | SQL           | Amit       |

### 🚫 Problems Here

- Multiple values in one column (SQL, Python) ❌
- Data repetition ❌
- Hard to query ❌

---

## ✅ Step 1: First Normal Form (1NF)

### 📌 Rule

- Each column should have **atomic (single)** values
- No multiple values in a single cell

### ✅ Convert to 1NF

| Student_ID | Name    | Course  | Instructor |
|------------|---------|---------|------------|
| 101        | Rahul   | SQL     | Amit       |
| 101        | Rahul   | Python  | Amit       |
| 102        | Anjali  | SQL     | Amit       |

### ✔ What We Fixed

- Removed multi-values ✅
- Each cell = single value ✅

### ❗ Still Problem

- Rahul name repeated multiple times
- Instructor repeated
- 👉 **Redundancy still exists**

---

## ✅ Step 2: Second Normal Form (2NF)

### 📌 Rule

- Must be in **1NF**
- Remove **partial dependency**

### 🤔 What is Partial Dependency?

When a column depends on only **part of a composite key**.

👉 Here, primary key = **(Student_ID + Course)**

But:
- Name depends only on Student_ID
- Not on full key ❌

### ✅ Convert to 2NF

**Students Table**

| Student_ID | Name    |
|------------|---------|
| 101        | Rahul   |
| 102        | Anjali  |

**Enrollment Table**

| Student_ID | Course  | Instructor |
|------------|---------|------------|
| 101        | SQL     | Amit       |
| 101        | Python  | Amit       |
| 102        | SQL     | Amit       |

### ✔ What We Fixed

- Removed partial dependency ✅
- Student info separated ✅

### ❗ Still Problem

- Instructor depends on Course, not Student
- 👉 This is **transitive dependency**

---

## ✅ Step 3: Third Normal Form (3NF)

### 📌 Rule

- Must be in **2NF**
- Remove **transitive dependency**

### 🤔 What is Transitive Dependency?

When:
- **A → B → C**
- Then A indirectly determines C ❌

👉 **In our case:**
- Course → Instructor
- But stored in same table ❌

### ✅ Convert to 3NF

**Students Table**

| Student_ID | Name    |
|------------|---------|
| 101        | Rahul   |
| 102        | Anjali  |

**Courses Table**

| Course  | Instructor |
|---------|------------|
| SQL     | Amit       |
| Python  | Neha       |

**Enrollment Table**

| Student_ID | Course  |
|------------|---------|
| 101        | SQL     |
| 101        | Python  |
| 102        | SQL     |

### ✔ What We Fixed

- Removed indirect dependency ✅
- Clean separation of data ✅
- No redundancy ✅

---

## ⚖️ Final Summary (Interview Ready)

| Normal Form | Rule                        | Goal                              |
|-------------|-----------------------------|-----------------------------------|
| **1NF**     | Atomic values               | Remove repeating groups           |
| **2NF**     | No partial dependency       | Separate data properly            |
| **3NF**     | No transitive dependency    | Remove indirect relationships     |

---

## 💡 Easy Way to Remember

- **1NF** → One value per cell
- **2NF** → Full key dependency
- **3NF** → No indirect dependency

---

## 🎯 Why Are They Used?

- Prevent data duplication
- Avoid update errors
- Improve data integrity
- Make database scalable

---

# 📘 1️⃣ FIRST NORMAL FORM (1NF)

## ✅ Definition

A table is in **First Normal Form (1NF)** if:
- Each column contains only **one value (atomic)**
- No multiple values in a single column
- Each row is uniquely identified (Primary Key exists)

---

## ❌ Example (Not in 1NF)

| Student_ID | Name   | Courses         |
|------------|--------|-----------------|
| 101        | John   | Math, Science   |
| 102        | Alice  | English         |

### 🔑 Keys and Columns

- **Primary Key:** Student_ID
- **Non-key columns:** Name, Courses

### 🔗 Dependencies

- Student_ID → Name
- Student_ID → Courses

### ❌ Problem

- Column `Courses` contains multiple values ("Math, Science")
- **Not atomic**

---

## ⚠️ WHY this is a problem

### 1. ❌ Cannot search properly

If you want:
👉 *"Find students taking Math"*

Database cannot easily search inside "Math, Science".

### 2. ❌ Insert problem

If a student has 3 courses:
- You must store all in one cell → messy

### 3. ❌ Update problem

If you want to change "Science" to "Physics":
- You must edit inside a string → error-prone

---

## ✅ Convert to 1NF

| Student_ID | Name   | Course   |
|------------|--------|----------|
| 101        | John   | Math     |
| 101        | John   | Science  |
| 102        | Alice  | English  |

### 🔑 Keys and Dependencies

- **Primary Key:** (Student_ID, Course)
- **Non-key column:** Name

**Dependency:**
- Student_ID → Name

### ⚠️ Still a problem (leads to 2NF)

- Name depends only on Student_ID (part of key)
- → **Partial dependency**

---

# 📘 2️⃣ SECOND NORMAL FORM (2NF)

## ✅ Definition

A table is in **Second Normal Form (2NF)** if:
- It is already in **1NF**
- **No partial dependency**

---

## 🔴 What is Partial Dependency?

When a **non-key column** depends on **part of a composite key**.

---

## ❌ Example (Not in 2NF)

| Student_ID | Course   | Name |
|------------|----------|------|
| 101        | Math     | John |
| 101        | Science  | John |

### 🔑 Keys and Columns

- **Primary Key:** (Student_ID, Course)
- **Non-key column:** Name

### 🔗 Dependencies

- Student_ID → Name
- (Student_ID, Course) is full key

👉 But:
- **Name depends only on Student_ID** (not full key)

### ❌ Problem

- Same Name is repeated for each course

---

## ⚠️ WHY this is a problem

### 1. ❌ Data Redundancy

John is repeated multiple times:
- John, John, John...

### 2. ❌ Update Anomaly

If John changes name to "Johnny":
- You must update multiple rows
- If one row is missed → inconsistent data

### 3. ❌ Insert Problem

If a student has no course yet:
- You cannot insert student info (Course required in PK)

### 4. ❌ Delete Problem

If you delete last course of John:
- You lose student info completely

---

## ✅ Convert to 2NF

### Table 1: Students

| Student_ID | Name   |
|------------|--------|
| 101        | John   |
| 102        | Alice  |

- **Primary Key:** Student_ID
- **Dependency:** Student_ID → Name

### Table 2: Student_Courses

| Student_ID | Course   |
|------------|----------|
| 101        | Math     |
| 101        | Science  |

- **Primary Key:** (Student_ID, Course)
- No non-key columns

### ✅ Now:

- No partial dependency
- No redundancy of Name

---

# 📘 3️⃣ THIRD NORMAL FORM (3NF)

## ✅ Definition

A table is in **Third Normal Form (3NF)** if:
- It is already in **2NF**
- **No transitive dependency**

---

## 🔴 What is Transitive Dependency?

When:
- One non-key column depends on another non-key column

**Form:**
- **A → B → C**

---

## ❌ Example (Not in 3NF)

| Student_ID | Name  | Department_ID | Department_Name   |
|------------|-------|---------------|-------------------|
| 101        | John  | D1            | Computer Science  |
| 102        | Alice | D2            | Mechanical        |

### 🔑 Keys and Columns

- **Primary Key:** Student_ID
- **Non-key columns:** Name, Department_ID, Department_Name

### 🔗 Dependencies

- Student_ID → Name
- Student_ID → Department_ID
- Department_ID → Department_Name

### 🔴 Transitive Dependency

- Student_ID → Department_ID
- Department_ID → Department_Name

👉 So:
- **Student_ID → Department_Name (indirect)**

### ❌ Problem

- Department_Name depends on Department_ID (not directly on primary key)

---

## ⚠️ WHY this is a problem

### 1. ❌ Data Redundancy

"Computer Science" repeated for every student in that department.

### 2. ❌ Update Anomaly

If department name changes:
- Must update many rows
- Risk of inconsistency

### 3. ❌ Insert Problem

You cannot add a new department unless a student exists.

### 4. ❌ Delete Problem

If last student of a department is deleted:
- Department info is lost

---

## ✅ Convert to 3NF

### Table 1: Students

| Student_ID | Name  | Department_ID |
|------------|-------|---------------|
| 101        | John  | D1            |
| 102        | Alice | D2            |

- **Primary Key:** Student_ID

### Table 2: Departments

| Department_ID | Department_Name   |
|---------------|-------------------|
| D1            | Computer Science  |
| D2            | Mechanical        |

- **Primary Key:** Department_ID

### 🔗 Final Dependencies

- Student_ID → Name, Department_ID
- Department_ID → Department_Name

✅ No non-key → non-key dependency in same table
✔️ No transitive dependency

---

# 🧠 FINAL UNDERSTANDING

| Normal Form | Removes                | Main Problem                              |
|-------------|------------------------|-------------------------------------------|
| **1NF**     | Multiple values        | Hard to query & update                    |
| **2NF**     | Partial dependency     | Redundant repeated data                   |
| **3NF**     | Transitive dependency  | Indirect dependency causing redundancy    |

---

## 🔥 ONE LINE MEMORY

> - **1NF:** One column → one value
> - **2NF:** Full key → full dependency
> - **3NF:** Only key → decides everything
