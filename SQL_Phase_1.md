# SQL Phase 1 — Foundations

> **Goal:** Build a strong foundation for PostgreSQL/SQL before moving into JOINs, subqueries, CTEs, and window functions.
>
> Use this file whenever you forget the basics. The important part is not memorizing syntax — understand what each clause is doing and in what order.

---

## 1. What is SQL?

**SQL (Structured Query Language)** is used to communicate with relational databases.

A database contains tables.

Example:

```text
Database
├── employees
├── departments
├── customers
└── orders
```

A table contains:

- **Columns** → describe the type of information
- **Rows** → individual records

Example:

```text
employee_id | name  | city      | salary
------------+-------+-----------+-------
1           | Abdul | Bangalore | 70000
2           | Rahul | Mumbai    | 60000
3           | Aisha | Delhi     | 50000
```

Think:

```text
Column = what information?
Row    = one record
Table  = collection of records
```

---

# 2. Our Practice Table

Use this table while revising Phase 1.

```sql
CREATE TABLE employees (
    employee_id INT,
    name VARCHAR(50),
    city VARCHAR(50),
    department VARCHAR(30),
    salary NUMERIC(10,2),
    is_active BOOLEAN
);

INSERT INTO employees VALUES
(1, 'Abdul', 'Bangalore', 'IT', 70000, TRUE),
(2, 'Rahul', 'Mumbai', 'IT', 60000, TRUE),
(3, 'Aisha', 'Delhi', 'HR', 50000, TRUE),
(4, 'John', 'Bangalore', 'HR', 80000, FALSE),
(5, 'Sara', 'Mumbai', 'Sales', 45000, TRUE),
(6, 'David', 'Chennai', 'Sales', 55000, TRUE),
(7, 'Alex', 'Bangalore', 'IT', 90000, TRUE),
(8, 'Emma', 'Delhi', 'HR', 65000, TRUE),
(9, 'Rohan', 'Mumbai', 'Sales', 75000, FALSE),
(10, 'Priya', 'Chennai', 'IT', 85000, TRUE);
```

Check it:

```sql
SELECT *
FROM employees;
```

---

# 3. SELECT

`SELECT` answers:

> **What columns do I want to see?**

Basic structure:

```sql
SELECT column
FROM table;
```

Example:

```sql
SELECT name
FROM employees;
```

Multiple columns:

```sql
SELECT name, salary
FROM employees;
```

All columns:

```sql
SELECT *
FROM employees;
```

Mental model:

```text
SELECT → What do I want?
FROM   → Where is it?
```

---

# 4. WHERE

`WHERE` filters **rows**.

Example:

```sql
SELECT name, salary
FROM employees
WHERE salary > 60000;
```

Think:

```text
All rows
   ↓
Check condition for each row
   ↓
TRUE  → keep
FALSE → remove
```

Example:

```text
Abdul  70000 → 70000 > 60000 → KEEP
Rahul  60000 → 60000 > 60000 → REMOVE
Aisha  50000 → 50000 > 60000 → REMOVE
```

### Key idea

> `WHERE` filters individual rows.

---

# 5. Comparison Operators

Common operators:

| Operator | Meaning |
|---|---|
| `=` | equal |
| `!=` | not equal |
| `<>` | not equal |
| `>` | greater than |
| `<` | less than |
| `>=` | greater than or equal |
| `<=` | less than or equal |

Examples:

```sql
WHERE salary = 70000;
```

```sql
WHERE salary > 60000;
```

```sql
WHERE salary >= 60000;
```

```sql
WHERE city != 'Delhi';
```

---

# 6. Text/String Values

Text values use **single quotes**:

```sql
WHERE city = 'Bangalore';
```

Do not use double quotes for normal text values:

```sql
-- Wrong for a text value
WHERE city = "Bangalore";
```

In PostgreSQL, double quotes are used for identifiers such as column/table names when needed.

Remember:

```text
'Bangalore' → text value
"Bangalore" → identifier
```

---

# 7. AND

`AND` means **both conditions must be true**.

```sql
SELECT name, salary
FROM employees
WHERE salary > 60000
  AND city = 'Bangalore';
```

Mental model:

```text
salary > 60000
      AND
city = Bangalore

TRUE + TRUE → KEEP
TRUE + FALSE → REMOVE
FALSE + TRUE → REMOVE
FALSE + FALSE → REMOVE
```

Use `AND` when all conditions must be satisfied.

---

# 8. OR

`OR` means **at least one condition must be true**.

```sql
SELECT name, city
FROM employees
WHERE city = 'Bangalore'
   OR city = 'Mumbai';
```

Mental model:

```text
Bangalore OR Mumbai

Bangalore → TRUE
Mumbai    → TRUE
Delhi     → FALSE
```

---

# 9. AND + OR + Parentheses

This is an important interview area.

Suppose the requirement is:

> Active employees who are from Bangalore OR Mumbai.

Write:

```sql
SELECT name, city
FROM employees
WHERE is_active = TRUE
  AND (city = 'Bangalore' OR city = 'Mumbai');
```

The parentheses make the intended logic clear:

```text
ACTIVE
  AND
(BANGALORE OR MUMBAI)
```

Without parentheses:

```sql
WHERE is_active = TRUE
  AND city = 'Bangalore'
  OR city = 'Mumbai';
```

the logic can be interpreted according to operator precedence as:

```text
(active AND Bangalore) OR Mumbai
```

That could include inactive Mumbai employees.

### Rule

When mixing `AND` and `OR`, use parentheses when they clarify the intended grouping.

---

# 10. IN

`IN` is useful when checking one column against several possible values.

Instead of:

```sql
WHERE city = 'Bangalore'
   OR city = 'Mumbai';
```

you can write:

```sql
WHERE city IN ('Bangalore', 'Mumbai');
```

Think:

```text
Is city one of these values?
```

Example:

```sql
SELECT name
FROM employees
WHERE city IN ('Bangalore', 'Mumbai');
```

---

# 11. NOT IN

`NOT IN` means the value should not be any of the listed values.

```sql
SELECT name, salary
FROM employees
WHERE city NOT IN ('Delhi');
```

For multiple values:

```sql
WHERE city NOT IN ('Delhi', 'Mumbai');
```

Think:

```text
IN      → keep listed values
NOT IN  → remove listed values
```

### Important NULL warning

`NOT IN` has tricky behavior when the expression/list contains `NULL`. This becomes important later when dealing with subqueries. Don't blindly replace every `NOT IN` with another form without considering NULLs.

---

# 12. NULL

`NULL` means the value is **missing/unknown**, not zero and not an empty string.

You cannot test it with normal equality:

```sql
-- Wrong
WHERE manager = NULL;
```

Use:

```sql
WHERE manager IS NULL;
```

To find values that exist:

```sql
WHERE manager IS NOT NULL;
```

### Remember

```text
NULL
 ↓
special value/state
 ↓
use IS / IS NOT
```

Therefore:

```sql
IS NULL
IS NOT NULL
```

not:

```sql
= NULL
!= NULL
```

This is a very common SQL interview trap.

---

# 13. BOOLEAN

PostgreSQL supports Boolean values:

```text
TRUE
FALSE
NULL
```

Example:

```sql
SELECT name
FROM employees
WHERE is_active = TRUE;
```

You can also write:

```sql
SELECT name
FROM employees
WHERE is_active IS TRUE;
```

For a Boolean column, these are useful forms:

```sql
WHERE is_active = TRUE
WHERE is_active IS TRUE
```

`IS TRUE` is particularly useful when you want explicit Boolean semantics.

---

# 14. DISTINCT

`DISTINCT` removes duplicate rows from the **query result**.

Example:

```sql
SELECT DISTINCT city
FROM employees;
```

Without `DISTINCT`, cities may repeat:

```text
Bangalore
Mumbai
Delhi
Bangalore
Mumbai
Chennai
...
```

With `DISTINCT`:

```text
Bangalore
Mumbai
Delhi
Chennai
```

### Important

`DISTINCT` does **not delete data from the table**.

It only removes duplicate result rows.

---

# 15. ORDER BY

`ORDER BY` sorts the result.

Ascending:

```sql
SELECT name, salary
FROM employees
ORDER BY salary ASC;
```

Descending:

```sql
SELECT name, salary
FROM employees
ORDER BY salary DESC;
```

Usually:

```text
ASC  → smallest → largest
DESC → largest → smallest
```

For salaries:

```text
DESC
90000
85000
80000
...
```

```text
ASC
45000
50000
55000
...
```

---

# 16. LIMIT

`LIMIT` controls how many rows are returned.

Example:

```sql
SELECT name, salary
FROM employees
ORDER BY salary DESC
LIMIT 3;
```

Mental model:

```text
FROM employees
      ↓
ORDER BY salary DESC
      ↓
highest salary first
      ↓
LIMIT 3
      ↓
return first 3 rows
```

### Important

`LIMIT 3` does NOT mean salary <= 3.

It means:

> Return at most 3 result rows.

---

# 17. The Important Query Pattern

A very common Phase 1 pattern:

```sql
SELECT columns
FROM table
WHERE condition
ORDER BY column DESC
LIMIT 3;
```

Example:

```sql
SELECT name, salary
FROM employees
WHERE is_active = TRUE
ORDER BY salary DESC
LIMIT 3;
```

Meaning:

```text
1. Get employees
2. Keep active employees
3. Sort them by salary, highest first
4. Return top 3
```

---

# 18. Logical Thinking / Dry Run

Don't think SQL is executed as random lines.

For the simple query:

```sql
SELECT name, salary
FROM employees
WHERE salary > 60000
ORDER BY salary DESC
LIMIT 3;
```

Think conceptually:

```text
employees
   ↓
filter salary > 60000
   ↓
remaining rows
   ↓
sort salary DESC
   ↓
take first 3
   ↓
display name + salary
```

A useful simplified mental order for Phase 1 is:

```text
FROM
 ↓
WHERE
 ↓
SELECT
 ↓
ORDER BY
 ↓
LIMIT
```

This is a learning model, not a complete description of PostgreSQL's internal execution engine.

Later, when we learn `GROUP BY`, `HAVING`, and joins, the logical query processing model becomes more important.

---

# 19. SELECT Aliases

You can give a calculated or selected column a temporary output name.

```sql
SELECT name,
       salary AS monthly_salary
FROM employees;
```

The table is not changed.

`AS` creates an alias for the query result.

Example:

```sql
SELECT salary + 5000 AS salary_with_bonus
FROM employees;
```

The result column is called:

```text
salary_with_bonus
```

---

# 20. Expressions in SELECT

SQL can calculate values.

```sql
SELECT name,
       salary + 5000 AS salary_with_bonus
FROM employees;
```

You can use arithmetic such as:

```text
+
-
*
/
```

Example:

```sql
SELECT name,
       salary * 12 AS yearly_salary
FROM employees;
```

This doesn't modify the stored salary.

It calculates a value for the result.

---

# 21. Common Mistakes

## Mistake 1 — Using `==`

SQL normally uses:

```sql
=
```

not:

```sql
==
```

Correct:

```sql
WHERE city = 'Bangalore';
```

---

## Mistake 2 — Comparing NULL with `=`

Wrong:

```sql
WHERE manager = NULL;
```

Correct:

```sql
WHERE manager IS NULL;
```

---

## Mistake 3 — Wrong OR syntax

Wrong:

```sql
WHERE city = 'Bangalore' OR 'Mumbai';
```

Correct:

```sql
WHERE city = 'Bangalore'
   OR city = 'Mumbai';
```

Or:

```sql
WHERE city IN ('Bangalore', 'Mumbai');
```

---

## Mistake 4 — Double quotes for strings

Wrong:

```sql
WHERE city = "Bangalore";
```

Correct:

```sql
WHERE city = 'Bangalore';
```

---

## Mistake 5 — Confusing WHERE and ORDER BY

`WHERE` filters.

`ORDER BY` sorts.

```text
WHERE → Which rows?
ORDER BY → In what order?
```

---

## Mistake 6 — Thinking LIMIT filters based on a value

```sql
LIMIT 3
```

doesn't mean:

```text
salary <= 3
```

It means:

```text
return at most 3 rows
```

---

# 22. Phase 1 Cheat Sheet

```sql
-- Select columns
SELECT name
FROM employees;

-- Select multiple columns
SELECT name, salary
FROM employees;

-- Everything
SELECT *
FROM employees;

-- Filter
SELECT name
FROM employees
WHERE salary > 60000;

-- AND
WHERE salary > 60000
  AND city = 'Bangalore';

-- OR
WHERE city = 'Bangalore'
   OR city = 'Mumbai';

-- IN
WHERE city IN ('Bangalore', 'Mumbai');

-- NOT IN
WHERE city NOT IN ('Delhi', 'Mumbai');

-- NULL
WHERE manager IS NULL;

-- NOT NULL
WHERE manager IS NOT NULL;

-- DISTINCT
SELECT DISTINCT city
FROM employees;

-- Sort
ORDER BY salary DESC;

-- Top N
ORDER BY salary DESC
LIMIT 3;

-- Alias
SELECT salary AS monthly_salary
FROM employees;

-- Calculation
SELECT salary * 12 AS yearly_salary
FROM employees;
```

---

# 23. Interview-Level Questions

Don't just solve these by memorizing syntax. Explain your reasoning.

## Q1 — Top active employees

Find the top 3 highest-paid active employees.

Return:

```text
name | salary
```

Expected tools:

```text
WHERE
ORDER BY
LIMIT
```

---

## Q2 — Multiple cities

Find active employees from Bangalore, Mumbai, or Chennai.

Return:

```text
name | city
```

Try both:

```text
IN
```

and:

```text
OR
```

---

## Q3 — Excluding cities

Find employees who are not from Delhi or Mumbai.

Return:

```text
name | city
```

---

## Q4 — Salary range

Find employees whose salary is between 60,000 and 85,000 inclusive.

Return:

```text
name | salary
```

Hint:

```text
>=
<=
AND
```

---

## Q5 — Logic trap

Find active employees who are from Bangalore or Mumbai.

Write the query so that **inactive Mumbai employees are NOT included**.

Think carefully about:

```text
AND
OR
()
```

---

## Q6 — NULL trap

Suppose `manager` exists in the table.

Find employees who don't have a manager.

Explain why:

```sql
manager = NULL
```

doesn't work.

---

## Q7 — Top N after filtering

Find the 2 highest-paid employees from Bangalore who are active.

Correct conceptual flow:

```text
employees
 ↓
Bangalore
 ↓
active
 ↓
sort salary DESC
 ↓
LIMIT 2
```

---

## Q8 — Distinct values

Find the distinct departments of active employees.

Important:

`DISTINCT` should apply to the result you actually want.

---

## Q9 — Business-style question

A company wants to contact high-paid active employees.

Find the top 3 active employees whose salary is greater than 60,000, ordered from highest salary to lowest.

---

## Q10 — Harder logic

Find employees who satisfy:

```text
active
AND
(
    Bangalore
    OR
    Mumbai
)
AND
salary >= 70000
```

Return:

```text
name | city | salary
```

---

# 24. Interview Thinking Checklist

Before writing SQL, translate the English requirement.

Example:

> Find the top 3 active employees from Bangalore earning more than 60k.

Break it down:

```text
"employees"
        ↓
FROM employees

"active"
        ↓
WHERE is_active = TRUE

"from Bangalore"
        ↓
AND city = 'Bangalore'

"earning more than 60k"
        ↓
AND salary > 60000

"top 3"
        ↓
LIMIT 3

"highest salary first"
        ↓
ORDER BY salary DESC
```

Then combine:

```sql
SELECT name, salary
FROM employees
WHERE is_active = TRUE
  AND city = 'Bangalore'
  AND salary > 60000
ORDER BY salary DESC
LIMIT 3;
```

This translation skill is more valuable than memorizing individual queries.

---

# 25. SQL 50 — What You Should Remember From SELECT Problems

When doing SQL 50 SELECT problems, pay special attention to these patterns:

### Filtering

```sql
WHERE condition
```

### Multiple conditions

```sql
WHERE condition1
  AND condition2
```

### Alternative conditions

```sql
WHERE condition1
   OR condition2
```

### Multiple values

```sql
WHERE column IN (...)
```

### Excluding values

```sql
WHERE column NOT IN (...)
```

### Missing values

```sql
WHERE column IS NULL
```

### Existing values

```sql
WHERE column IS NOT NULL
```

### Unique values

```sql
SELECT DISTINCT column
```

### Sorting

```sql
ORDER BY column DESC
```

### Top N

```sql
ORDER BY column DESC
LIMIT N
```

### Output naming

```sql
column AS alias
```

### Calculations

```sql
salary * 12 AS yearly_salary
```

---

# 26. The Most Important Mental Model

Whenever you receive a SQL problem, ask:

```text
1. Which TABLE?
       ↓
2. Which ROWS?
       ↓
3. Which COLUMNS?
       ↓
4. Do I need to SORT?
       ↓
5. Do I need only N rows?
```

For Phase 1:

```text
TABLE
  ↓
FROM

ROWS
  ↓
WHERE

COLUMNS
  ↓
SELECT

SORT
  ↓
ORDER BY

NUMBER OF RESULTS
  ↓
LIMIT
```

Once this becomes automatic, SQL questions become much easier.

---

# 27. Phase 1 Completion Checklist

Before moving forward, you should be comfortable with:

- [ ] What a database is
- [ ] What a table is
- [ ] Rows vs columns
- [ ] `SELECT`
- [ ] `FROM`
- [ ] `SELECT *`
- [ ] `WHERE`
- [ ] `=`
- [ ] `!=`
- [ ] `>`
- [ ] `<`
- [ ] `>=`
- [ ] `<=`
- [ ] `AND`
- [ ] `OR`
- [ ] Parentheses with conditions
- [ ] `IN`
- [ ] `NOT IN`
- [ ] `NULL`
- [ ] `IS NULL`
- [ ] `IS NOT NULL`
- [ ] Boolean values
- [ ] `DISTINCT`
- [ ] `ORDER BY`
- [ ] `ASC`
- [ ] `DESC`
- [ ] `LIMIT`
- [ ] Column aliases
- [ ] Basic arithmetic expressions
- [ ] Translating English requirements into SQL
- [ ] Dry-running a query

---

# 28. What Comes Next

After Phase 1, the next progression is:

```text
PHASE 1
SELECT + filtering
        ↓
PHASE 2
String / Date functions
        ↓
PHASE 3
Aggregate functions
COUNT / SUM / AVG / MIN / MAX
        ↓
PHASE 4
GROUP BY
        ↓
PHASE 5
HAVING
        ↓
PHASE 6
JOINS
        ↓
PHASE 7
Subqueries
        ↓
PHASE 8
CTEs
        ↓
PHASE 9
CASE
        ↓
PHASE 10
Window Functions
        ↓
PHASE 11
PostgreSQL + Database Design
        ↓
PHASE 12
Indexes + EXPLAIN + Query Optimization
```

**Don't rush Phase 2.** The goal of Phase 1 is that `SELECT → FROM → WHERE → ORDER BY → LIMIT` becomes automatic.

---

# One-line memory

```text
SELECT = what
FROM   = where
WHERE  = which rows
DISTINCT = remove duplicate results
ORDER BY = sorting
LIMIT = how many rows
NULL = missing/unknown → IS NULL / IS NOT NULL
```
