# STEP 15 — First Normal Form (1NF)

## 1. Input Table (Unnormalized)

| Student_ID | Name   | Phone       | Course_ID  | Course_Name       | Instructor      | Dept_ID  | Dept_Name    |
|------------|--------|-------------|------------|-------------------|-----------------|----------|--------------|
| 101        | Rahul  | 9999900000  | C1, C2, C3 | SQL, Python, Spark | Amit, Neha, Amit | D1, D2, D1 | Data, AI, Data |
| 102        | Anjali | 8888800000  | C1, C2     | SQL, Python        | Amit, Neha       | D1, D2     | Data, AI       |
| 103        | Kiran  | 7777700000  | C1         | SQL                | Amit             | D1         | Data           |

## 2. What's the Problem?

### Data Redundancy

Not visible yet in terms of rows, but the multi-valued cells are hiding massive duplication — "SQL, Amit, D1, Data" is packed inside BOTH Rahul's row and Anjali's row as part of their comma lists.

### Data Integrity

- The database sees "C1, C2, C3" as ONE text string, not three separate values.
- You CANNOT run `WHERE Course_ID = 'C1'` — it won't match because the cell contains "C1, C2, C3".
- You cannot enforce a foreign key on "C1, C2, C3" — it's not a valid reference to any single course.
- You cannot sort by Course_ID, filter by it, or join on it — SQL is blind to individual values inside a comma string.
- Data has no integrity because the database can't even understand what's inside the cells.

### Insert Anomaly

A new course Java (C4) is introduced, taught by Priya. Where do you insert it? There's no way to add it without attaching it to a student's comma list. You can't add it as an independent fact.

### Update Anomaly

Amit's name changes to "Amit Sharma". He appears inside the comma strings of ALL three rows. You'd have to parse every comma list, find "Amit," replace it — across every row he appears in. Miss one comma list → his name is "Amit" in some rows, "Amit Sharma" in others → inconsistency.

### Delete Anomaly

Kiran (103) drops out. You delete his row. His row was the simplest record of "C1 = SQL, Amit, D1, Data". If Rahul and Anjali also drop their SQL enrollment by removing C1 from their comma lists, all knowledge of the SQL course is gone.

## 3. What's the Rule?

**1NF** — every cell must contain one and only one value (atomic). No lists, no comma-separated values, no multiple entries in a single cell.

> One cell = one value. Period.

## 4. How to Fix It

Break every multi-valued cell into separate rows.

- Rahul has "C1, C2, C3" → becomes 3 rows, one per course.
- Anjali has "C1, C2" → becomes 2 rows.
- Kiran has "C1" → stays 1 row.

Since Student_ID is no longer unique (101 appears 3 times), we need a **composite primary key**: `(Student_ID, Course_ID)`.

## 5. Output Table (1NF)

| Student_ID (PK) | Course_ID (PK) | Name   | Phone       | Course_Name | Instructor | Dept_ID | Dept_Name |
|------------------|----------------|--------|-------------|-------------|------------|---------|-----------|
| 101              | C1             | Rahul  | 9999900000  | SQL         | Amit       | D1      | Data      |
| 101              | C2             | Rahul  | 9999900000  | Python      | Neha       | D2      | AI        |
| 101              | C3             | Rahul  | 9999900000  | Spark       | Amit       | D1      | Data      |
| 102              | C1             | Anjali | 8888800000  | SQL         | Amit       | D1      | Data      |
| 102              | C2             | Anjali | 8888800000  | Python      | Neha       | D2      | AI        |
| 103              | C1             | Kiran  | 7777700000  | SQL         | Amit       | D1      | Data      |

Every cell has exactly one value. SQL can now search, filter, join, sort on any column. **1NF achieved.**

### Keys and Dependencies

| Type    | Columns                                                       |
|---------|---------------------------------------------------------------|
| Key     | Student_ID, Course_ID (composite PK)                          |
| Non-Key | Name, Phone, Course_Name, Instructor, Dept_ID, Dept_Name      |

## 6. What's STILL Wrong? (Why We Need 2NF)

1NF fixed the cell problem. But look at the table now:

### Data Redundancy

- "Rahul, 9999900000" → repeated 3 times (rows 1, 2, 3)
- "Anjali, 8888800000" → repeated 2 times (rows 4, 5)
- "SQL, Amit, D1, Data" → repeated 3 times (rows 1, 4, 6)
- "Python, Neha, D2, AI" → repeated 2 times (rows 2, 5)

### Data Inconsistency Risk

If Rahul changes his phone to 5555500000 and admin updates only row 1 but misses rows 2 and 3 → same student, different phones → broken data.

### Insert Anomaly

New course Java (C4) taught by Priya — still can't add it without a Student_ID because Student_ID is part of the composite PK. No student enrolled in Java yet → can't insert the course.

### Update Anomaly

Amit changes phone → he appears in rows 1, 3, 4, 6 → update 4 rows → miss one → inconsistency.

### Delete Anomaly

Kiran (103) drops out → delete row 6 → if he was the only student in a particular course, that course info disappears.

### The Root Cause

The PK is `(Student_ID, Course_ID)`. But:

- **Name, Phone** depend on `Student_ID` alone — they don't need Course_ID.
- **Course_Name, Instructor, Dept_ID, Dept_Name** depend on `Course_ID` alone — they don't need Student_ID.

Non-key columns depend on *part* of the composite key, not the whole key. This is called **partial dependency** — and it's exactly what 2NF fixes.

---

# STEP 16 — Second Normal Form (2NF)

## 1. Input Table (1NF)

| Student_ID (PK) | Course_ID (PK) | Name   | Phone       | Course_Name | Instructor | Dept_ID | Dept_Name |
|------------------|----------------|--------|-------------|-------------|------------|---------|-----------|
| 101              | C1             | Rahul  | 9999900000  | SQL         | Amit       | D1      | Data      |
| 101              | C2             | Rahul  | 9999900000  | Python      | Neha       | D2      | AI        |
| 101              | C3             | Rahul  | 9999900000  | Spark       | Amit       | D1      | Data      |
| 102              | C1             | Anjali | 8888800000  | SQL         | Amit       | D1      | Data      |
| 102              | C2             | Anjali | 8888800000  | Python      | Neha       | D2      | AI        |
| 103              | C1             | Kiran  | 7777700000  | SQL         | Amit       | D1      | Data      |

Composite PK: `(Student_ID, Course_ID)`

## 2. Quick Recap — What's Wrong (from end of 1NF)

The PK has two parts: Student_ID and Course_ID. But non-key columns don't need BOTH parts:

| Functional Dependency         | Depends on       | Full PK is                  | Problem                        |
|-------------------------------|------------------|-----------------------------|--------------------------------|
| Student_ID → Name             | Student_ID only  | (Student_ID, Course_ID)     | Partial — ignores Course_ID    |
| Student_ID → Phone            | Student_ID only  | (Student_ID, Course_ID)     | Partial — ignores Course_ID    |
| Course_ID → Course_Name       | Course_ID only   | (Student_ID, Course_ID)     | Partial — ignores Student_ID   |
| Course_ID → Instructor        | Course_ID only   | (Student_ID, Course_ID)     | Partial — ignores Student_ID   |
| Course_ID → Dept_ID           | Course_ID only   | (Student_ID, Course_ID)     | Partial — ignores Student_ID   |
| Course_ID → Dept_Name         | Course_ID only   | (Student_ID, Course_ID)     | Partial — ignores Student_ID   |

Every single non-key column depends on only PART of the key. This is **partial dependency** — the root cause of everything below.

## 3. What's the Problem?

### Data Redundancy

- "Rahul, 9999900000" → 3 times (rows 1, 2, 3) — because he takes 3 courses, his info is copied 3 times
- "Anjali, 8888800000" → 2 times (rows 4, 5)
- "SQL, Amit, D1, Data" → 3 times (rows 1, 4, 6) — because 3 students take SQL
- "Python, Neha, D2, AI" → 2 times (rows 2, 5)

### Data Inconsistency

Rahul changes phone to 5555500000. Admin updates row 1, forgets rows 2 and 3. Result: Student_ID 101 has phone 5555500000 in row 1, phone 9999900000 in rows 2 and 3. Same student, two different phone numbers — data is lying.

### Insert Anomaly

New course Java (C4) introduced, taught by Priya, Dept D3 (Backend), Fee 6000. You try to insert: `(???, C4, ???, ???, Java, Priya, D3, Backend)`. Student_ID is part of the PK — it's required, can't be null. No student has enrolled in Java yet → you literally cannot add this course to the database. The database refuses to acknowledge Java exists until a student signs up.

### Update Anomaly

Instructor for SQL changes from Amit to Raj. Amit appears as SQL instructor in rows 1, 4, 6 → 3 rows to update. Miss row 6 → SQL shows Raj for Rahul and Anjali, but Amit for Kiran. Same course, two different instructors — broken.

### Delete Anomaly

Kiran (103) drops out → delete row 6. Row 6 is: `(103, C1, Kiran, 7777700000, SQL, Amit, D1, Data)`. If Kiran was the ONLY student in a unique course (say "AWS"), deleting his row would also destroy the fact that AWS exists, its instructor, its department. You wanted to remove a student. You accidentally murdered a course.

### Root Cause

**Partial dependency.** Name and Phone are attached to the Courses key (Course_ID) even though they have NOTHING to do with courses. Course_Name and Instructor are attached to the Student key (Student_ID) even though they have NOTHING to do with students. Everything is chained to the wrong key, so everything gets duplicated.

## 4. What's the Rule?

**2NF** — a table is in 2NF when it is already in 1NF, and every non-key column depends on the **entire** composite primary key, not just a part of it.

> In simple terms: if a column only needs HALF the key, it doesn't belong in this table. Move it to a table where that half IS the full key.

## 5. How to Fix It

The principle: for each partial dependency, move the dependent columns to a new table where their actual determinant becomes the PK.

| Column      | Actually depends on | Move to         | Why                              |
|-------------|---------------------|-----------------|----------------------------------|
| Name        | Student_ID          | Students table  | Student_ID becomes PK there      |
| Phone       | Student_ID          | Students table  | Same reason                      |
| Course_Name | Course_ID           | Courses table   | Course_ID becomes PK there       |
| Instructor  | Course_ID           | Courses table   | Same reason                      |
| Dept_ID     | Course_ID           | Courses table   | Same reason                      |
| Dept_Name   | Course_ID           | Courses table   | Same reason                      |

What remains in the original table? Only the two key columns — Student_ID and Course_ID. That becomes the **Enrollment table**.

## 6. Output Tables (2NF)

### Students Table (PK: Student_ID)

| Student_ID (PK) | Name   | Phone       |
|------------------|--------|-------------|
| 101              | Rahul  | 9999900000  |
| 102              | Anjali | 8888800000  |
| 103              | Kiran  | 7777700000  |

- Name depends on Student_ID (full PK) → clean
- Phone depends on Student_ID (full PK) → clean
- Rahul appears once (was 3 times)

### Courses Table (PK: Course_ID)

| Course_ID (PK) | Course_Name | Instructor | Dept_ID | Dept_Name |
|-----------------|-------------|------------|---------|-----------|
| C1              | SQL         | Amit       | D1      | Data      |
| C2              | Python      | Neha       | D2      | AI        |
| C3              | Spark       | Amit       | D1      | Data      |

- Course_Name depends on Course_ID (full PK) → clean
- Instructor depends on Course_ID (full PK) → clean
- SQL info appears once (was 3 times)

### Enrollment Table (PK: Student_ID, Course_ID)

| Student_ID (PK, FK) | Course_ID (PK, FK) |
|----------------------|--------------------|
| 101                  | C1                 |
| 101                  | C2                 |
| 101                  | C3                 |
| 102                  | C1                 |
| 102                  | C2                 |
| 103                  | C1                 |

- Only key columns, zero non-key columns → nothing CAN be partially dependent
- Tracks "who enrolled in what" without carrying any student or course details

### Anomalies Check After 2NF

- **Insert Anomaly → FIXED.** New course Java? Just add a row to Courses table: `(C4, Java, Priya, D3, Backend)`. No student needed.
- **Update Anomaly → MOSTLY FIXED.** Rahul changes phone? Update one row in Students table. Done. SQL changes instructor? Update one row in Courses table. Done.
- **Delete Anomaly → FIXED.** Kiran drops out? Delete from Students and Enrollment. Courses table untouched. SQL still exists.

## 7. What's STILL Wrong? (Why We Need 3NF)

The Students table and Enrollment table are clean. But zoom into the Courses table:

| Course_ID (PK) | Course_Name | Instructor | Dept_ID | Dept_Name |
|-----------------|-------------|------------|---------|-----------|
| C1              | SQL         | Amit       | D1      | Data      |
| C2              | Python      | Neha       | D2      | AI        |
| C3              | Spark       | Amit       | D1      | Data      |

Look at the dependencies:

```
Course_ID → Dept_ID → Dept_Name
```

Dept_Name doesn't depend on Course_ID directly. It depends on Dept_ID, which happens to depend on Course_ID. That's a middleman. A chain. A **transitive dependency**.

And it's causing problems right now:

### Data Redundancy

Dept_Name "Data" appears twice (rows C1 and C3) — because both courses belong to D1. If 50 courses belong to Data department → "Data" repeated 50 times.

### Data Inconsistency

Department D1 renames from "Data" to "Data Science". Update row C1, forget row C3 → same Dept_ID, two different names → broken.

### Insert Anomaly

New department D3 (Cloud) is created, but no course is assigned to it yet. Can't add D3 to the Courses table without a Course_ID → can't record the department's existence.

### Update Anomaly

"Data" → "Data Science" requires updating every row where Dept_ID = D1. Miss one → inconsistency.

### Delete Anomaly

Delete course C2 (Python) → row (C2, Python, Neha, D2, AI) is gone. If C2 was the only course in D2 → the fact that department "AI" exists is now gone. You deleted a course. You accidentally killed a department.

### Root Cause

**Transitive dependency.** Dept_Name depends on Dept_ID (a non-key column), not directly on Course_ID (the PK). The chain: `Course_ID → Dept_ID → Dept_Name`. The middleman (Dept_ID) is the problem.

---

# STEP 17 — Third Normal Form (3NF)

## 1. Input Tables (from 2NF)

We produced three tables in 2NF. Students and Enrollment are clean. The problem lives in Courses.

### Students Table ✅ (no issues)

| Student_ID (PK) | Name   | Phone       |
|------------------|--------|-------------|
| 101              | Rahul  | 9999900000  |
| 102              | Anjali | 8888800000  |
| 103              | Kiran  | 7777700000  |

### Enrollment Table ✅ (no issues)

| Student_ID (PK, FK) | Course_ID (PK, FK) |
|----------------------|--------------------|
| 101                  | C1                 |
| 101                  | C2                 |
| 101                  | C3                 |
| 102                  | C1                 |
| 102                  | C2                 |
| 103                  | C1                 |

### Courses Table ⚠️ (problem here)

| Course_ID (PK) | Course_Name | Instructor | Dept_ID | Dept_Name |
|-----------------|-------------|------------|---------|-----------|
| C1              | SQL         | Amit       | D1      | Data      |
| C2              | Python      | Neha       | D2      | AI        |
| C3              | Spark       | Amit       | D1      | Data      |

## 2. Quick Recap — What We Spotted at End of 2NF

We fixed partial dependency. Every non-key column now depends on the full PK (Course_ID). No column depends on just part of a key. 2NF passed.

But we noticed a chain in the Courses table:

```
Course_ID → Dept_ID → Dept_Name
  (PK)      (non-key)   (non-key)
```

Dept_Name doesn't depend on Course_ID directly. It goes through Dept_ID — a middleman. And "Data" appears twice because of it. That's a new type of problem.

## 3. What is Transitive Dependency?

**Transitive Dependency** — when a non-key column depends on another non-key column, which in turn depends on the primary key. Instead of depending on the PK directly, it reaches the PK through a middleman.

The pattern: **PK → Non-Key A → Non-Key B**

- PK determines Non-Key A → that's normal, fine
- Non-Key A determines Non-Key B → that's the problem — a non-key column is determining another non-key column

Applied to our Courses table:

| Step in the chain | FD                    | What's happening                              |
|-------------------|-----------------------|-----------------------------------------------|
| Step 1            | Course_ID → Dept_ID   | PK determines Dept_ID — normal, fine          |
| Step 2            | Dept_ID → Dept_Name   | Non-key determines non-key — THIS is the problem |

Ask yourself: does Dept_Name REALLY depend on Course_ID?

No. Dept_Name depends on Dept_ID. It doesn't care what course you're looking at. Department "D1" is called "Data" whether the course is SQL, Spark, or anything else. The connection to Course_ID is indirect — it passes through Dept_ID like a middleman.

> A simple way to spot it: if two non-key columns have a determinant relationship between them (one decides the other), you have a transitive dependency.

## 4. What's the Problem?

### Data Redundancy

- Dept_Name "Data" → appears 2 times (rows C1 and C3) — both courses belong to D1
- Dept_Name "AI" → appears 1 time (only C2 belongs to D2) — safe for now, but if 10 courses join AI department, "AI" appears 10 times
- The redundancy grows with every course added to a department

### Data Inconsistency

Department D1 renames from "Data" to "Data Science". Admin updates row C1, forgets row C3. Result: Dept_ID D1 is called "Data Science" in C1 but "Data" in C3. Same department, two different names — data is lying.

### Insert Anomaly

New department D3 (Cloud) is created. No course is assigned to it yet. Can't add D3 to the Courses table — Course_ID is the PK, it's required. The database refuses to acknowledge a department exists until a course is created in it. Department is a real-world entity that should exist independently of courses.

### Update Anomaly

"Data" → "Data Engineering" for Dept_ID D1. D1 appears in rows C1 and C3 → 2 rows to update. With 50 courses in the Data department → 50 rows to update. Miss one → Dept_ID D1 has two different names → inconsistency.

### Delete Anomaly

Course C2 (Python) is discontinued → delete its row. Row C2 was: `(C2, Python, Neha, D2, AI)`. C2 was the only course in department D2. Deleting C2 also destroys the fact that department "AI" (D2) exists. You deleted a course. You accidentally killed a department.

### Root Cause

**Transitive dependency.** Dept_Name depends on Dept_ID (a non-key column), not directly on Course_ID (the PK). The middleman relationship forces Dept_Name to repeat every time its Dept_ID appears in a new course row.

## 5. What's the Rule?

**3NF** — a table is in 3NF when it is already in 2NF, and no non-key column depends on another non-key column. Every non-key column must depend directly on the primary key — no middlemen, no chains.

The famous phrase that covers 1NF through 3NF:

> "Every non-key column must depend on **the key**, **the whole key**, and **nothing but the key**."

- **The key** → 1NF (data is atomic, identifiable by a key)
- **The whole key** → 2NF (no partial dependency)
- **Nothing but the key** → 3NF (no transitive dependency — depend on the PK directly, not through another non-key)

## 6. How to Fix It

The principle: find the transitive chain, break it by pulling the middleman and its dependent into a separate table where the middleman becomes the PK.

Transitive chain to break:

```
Course_ID → Dept_ID → Dept_Name
               ↑           ↑
           middleman    depends on middleman

BREAK HERE: pull Dept_ID and Dept_Name into their own table
```

| Column    | Currently in | Actually depends on | Move to            | Why                                              |
|-----------|--------------|---------------------|--------------------|--------------------------------------------------|
| Dept_Name | Courses      | Dept_ID (non-key)   | Departments table  | Dept_ID becomes PK there                         |
| Dept_ID   | Courses      | Course_ID (PK)      | Stays in Courses as FK | Still a valid direct dependency on PK         |

So:

- Dept_Name leaves the Courses table → goes to a new **Departments** table
- Dept_ID stays in Courses but becomes a **foreign key** pointing to the Departments table
- The transitive chain is broken — Dept_Name now depends directly on Dept_ID which is the PK of its own table

## 7. Output Tables (3NF)

### Courses Table (PK: Course_ID)

| Course_ID (PK) | Course_Name | Instructor | Dept_ID (FK) |
|-----------------|-------------|------------|--------------|
| C1              | SQL         | Amit       | D1           |
| C2              | Python      | Neha       | D2           |
| C3              | Spark       | Amit       | D1           |

- Course_Name depends on Course_ID (PK) → direct → clean
- Instructor depends on Course_ID (PK) → direct → clean
- Dept_ID depends on Course_ID (PK) → direct → clean
- No non-key depends on another non-key → **3NF passed**

### Departments Table (PK: Dept_ID) — NEW

| Dept_ID (PK) | Dept_Name |
|---------------|-----------|
| D1            | Data      |
| D2            | AI        |

- Dept_Name depends on Dept_ID (PK) → direct → clean
- "Data" appears once (was 2 times)
- "AI" appears once

### Students Table (unchanged)

| Student_ID (PK) | Name   | Phone       |
|------------------|--------|-------------|
| 101              | Rahul  | 9999900000  |
| 102              | Anjali | 8888800000  |
| 103              | Kiran  | 7777700000  |

### Enrollment Table (unchanged)

| Student_ID (PK, FK) | Course_ID (PK, FK) |
|----------------------|--------------------|
| 101                  | C1                 |
| 101                  | C2                 |
| 101                  | C3                 |
| 102                  | C1                 |
| 102                  | C2                 |
| 103                  | C1                 |

### Anomalies Check After 3NF

- **Insert Anomaly → FIXED.** New department D3 (Cloud) with no courses yet? Just add `(D3, Cloud)` to Departments table. No course needed.
- **Update Anomaly → FIXED.** "Data" → "Data Science"? Update one row in Departments table. Every course referencing D1 automatically sees the new name through the FK.
- **Delete Anomaly → FIXED.** Delete course C2 (Python)? Departments table untouched. D2 (AI) still exists.
- **Redundancy → FIXED.** "Data" stored once. "AI" stored once. No duplication.
- **Inconsistency → IMPOSSIBLE.** Each department name exists in one place. Can't have two different values.
