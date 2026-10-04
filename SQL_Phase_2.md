# SQL Phase 2 — String & Date Functions

## Goal

Learn the useful string and date functions needed for SQL practice, SQL 50, interviews, and real-world data cleaning.

---

# 1. String Functions

SQL functions take a value, process it, and return a result.

```sql
FUNCTION(value)
```

Functions can be nested:

```sql
LENGTH(TRIM(name))
```

Think from the inside out:

```text
name → TRIM() → LENGTH() → result
```

---

# 2. LENGTH()

Returns the number of characters.

```sql
SELECT name, LENGTH(name) AS name_length
FROM employees;
```

Example:

```text
Abdul → 5
Rahul → 5
```

Useful with filtering:

```sql
SELECT name
FROM employees
WHERE LENGTH(name) > 5;
```

When spaces may exist:

```sql
LENGTH(TRIM(name))
```

---

# 3. UPPER()

Converts text to uppercase.

```sql
SELECT name, UPPER(name) AS uppercase_name
FROM employees;
```

```text
Abdul → ABDUL
```

It does not permanently change the stored value when used in `SELECT`.

---

# 4. LOWER()

Converts text to lowercase.

```sql
SELECT name, LOWER(name) AS lowercase_name
FROM employees;
```

```text
Abdul → abdul
ABDUL → abdul
aBdUl → abdul
```

Useful for comparisons:

```sql
WHERE LOWER(name) = 'abdul'
```

---

# 5. TRIM()

Removes spaces from the beginning and end.

```sql
SELECT TRIM(name)
FROM employees;
```

```text
'   Abdul   ' → 'Abdul'
```

It does not remove spaces between words.

Very useful:

```sql
LENGTH(TRIM(name))
```

and:

```sql
TRIM(name) ILIKE 'a%'
```

---

# 6. REPLACE()

Replaces part of a string.

Syntax:

```sql
REPLACE(string, old_text, new_text)
```

Example:

```sql
SELECT
    email,
    REPLACE(email, 'example.com', 'company.com') AS new_email
FROM employees;
```

```text
abdul@example.com
        ↓
abdul@company.com
```

Another example:

```sql
SELECT REPLACE(name, 'a', '@')
FROM employees;
```

`REPLACE()` is case-sensitive.

Using it in `SELECT` does not permanently update the database.

---

# 7. CONCAT()

Combines strings.

```sql
SELECT CONCAT(name, ' - ', city) AS employee_info
FROM employees;
```

Multiple values:

```sql
SELECT CONCAT(
    TRIM(name),
    ' - ',
    city,
    ' - ',
    department
) AS employee_info
FROM employees;
```

Remember:

```text
CONCAT  → join strings
REPLACE → substitute text
```

---

# 8. SUBSTRING()

Extracts part of a string.

PostgreSQL syntax:

```sql
SUBSTRING(string FROM start FOR count)
```

Example:

```sql
SELECT SUBSTRING('Abdul Muqeet' FROM 1 FOR 5);
```

Result:

```text
Abdul
```

PostgreSQL string positions start at 1:

```text
A → 1
B → 2
C → 3
```

Example:

```sql
SELECT
    name,
    SUBSTRING(TRIM(name) FROM 1 FOR 3) AS first_three
FROM employees;
```

---

# 9. LEFT()

Gets characters from the beginning.

```sql
LEFT('Abdul Muqeet', 3)
```

Result:

```text
Abd
```

Equivalent idea:

```sql
SUBSTRING(name FROM 1 FOR 3)
```

---

# 10. RIGHT()

Gets characters from the end.

```sql
RIGHT('Abdul Muqeet', 3)
```

Result:

```text
eet
```

Example:

```sql
SELECT
    name,
    RIGHT(TRIM(name), 3) AS last_three
FROM employees;
```

---

# 11. LIKE

Used for pattern matching.

Two important wildcards:

```text
% → zero or more characters
_ → exactly one character
```

Starts with A:

```sql
WHERE name LIKE 'A%'
```

Ends with A:

```sql
WHERE name LIKE '%A'
```

Contains A:

```sql
WHERE name LIKE '%A%'
```

Second character is A:

```sql
WHERE name LIKE '_A%'
```

Exactly three characters:

```sql
WHERE name LIKE '___'
```

---

# 12. ILIKE

PostgreSQL `ILIKE` performs case-insensitive pattern matching.

```sql
WHERE name ILIKE 'a%'
```

Can match names such as:

```text
Abdul
Aisha
alex
```

Useful with messy data:

```sql
WHERE TRIM(name) ILIKE 'a%'
```

This:

1. removes outer spaces
2. ignores capitalization
3. checks that the name starts with A/a

---

# 13. High-Value Pattern Matching

```sql
'A%'    -- starts with A
'%A'    -- ends with A
'%A%'   -- contains A
'_A%'   -- second character is A
'___'   -- exactly 3 characters
```

For case-insensitive PostgreSQL matching:

```sql
ILIKE 'a%'
ILIKE '%a'
ILIKE '%a%'
```

---

# 14. Date and Time

Our employee table contains:

```text
joining_date
```

Example:

```text
2023-01-15
2022-06-20
2024-03-10
```

---

# 15. CURRENT_DATE

Returns the current date.

```sql
SELECT CURRENT_DATE;
```

---

# 16. CURRENT_TIMESTAMP

Returns current date and time.

```sql
SELECT CURRENT_TIMESTAMP;
```

Remember:

```text
CURRENT_DATE      → date
CURRENT_TIMESTAMP → date + time
```

---

# 17. EXTRACT()

Gets a part of a date.

Syntax:

```sql
EXTRACT(part FROM date)
```

Examples:

```sql
EXTRACT(YEAR FROM joining_date)
EXTRACT(MONTH FROM joining_date)
EXTRACT(DAY FROM joining_date)
```

Example:

```sql
SELECT
    name,
    EXTRACT(YEAR FROM joining_date) AS joining_year
FROM employees;
```

Other useful parts include:

```text
QUARTER
WEEK
DOW
```

---

# 18. Comparing Dates

Dates can be compared directly.

Before:

```sql
WHERE joining_date < '2023-01-01'
```

After:

```sql
WHERE joining_date > '2023-01-01'
```

On or before:

```sql
WHERE joining_date <= '2023-01-01'
```

On or after:

```sql
WHERE joining_date >= '2023-01-01'
```

Mental rule:

```text
BEFORE → <
AFTER  → >
```

---

# 19. Date Ranges

Find employees who joined during 2023:

```sql
SELECT name, joining_date
FROM employees
WHERE joining_date >= '2023-01-01'
  AND joining_date <= '2023-12-31';
```

Or:

```sql
WHERE joining_date BETWEEN '2023-01-01' AND '2023-12-31'
```

`BETWEEN` is inclusive at both ends.

---

# 20. INTERVAL

Represents a period of time.

Examples:

```sql
INTERVAL '1 year'
INTERVAL '6 months'
INTERVAL '30 days'
```

Add time:

```sql
joining_date + INTERVAL '1 year'
```

Subtract time:

```sql
joining_date - INTERVAL '30 days'
```

Example:

```sql
SELECT
    name,
    joining_date + INTERVAL '1 year' AS one_year_anniversary
FROM employees;
```

---

# 21. Date Difference

Subtracting two `DATE` values gives the number of days between them.

```sql
SELECT
    name,
    CURRENT_DATE - joining_date AS days_since_joining
FROM employees;
```

---

# 22. High-Value Phase 2 Patterns

Clean then measure:

```sql
LENGTH(TRIM(name))
```

Clean then match:

```sql
TRIM(name) ILIKE 'a%'
```

Clean then extract:

```sql
SUBSTRING(TRIM(name) FROM 1 FOR 3)
```

Transform:

```sql
UPPER(name)
LOWER(name)
```

Replace:

```sql
REPLACE(email, 'example.com', 'company.com')
```

Combine:

```sql
CONCAT(name, ' - ', city)
```

Date filtering:

```sql
WHERE joining_date < '2023-01-01'
```

Date extraction:

```sql
EXTRACT(YEAR FROM joining_date)
```

Date arithmetic:

```sql
joining_date + INTERVAL '1 year'
```

---

# 23. Common Mistakes

### Forgetting TRIM

Messy data:

```text
'   Abdul'
```

Better:

```sql
WHERE TRIM(name) ILIKE 'a%'
```

### Confusing `%` and `_`

```text
% → zero or more characters
_ → exactly one character
```

### Thinking LIKE is case-insensitive

In PostgreSQL:

```sql
LIKE
```

is case-sensitive.

Use:

```sql
ILIKE
```

for case-insensitive matching.

### Thinking SELECT REPLACE permanently changes data

This:

```sql
SELECT REPLACE(email, 'example.com', 'company.com')
FROM employees;
```

only changes the query result.

### Date direction

```text
BEFORE → <
AFTER  → >
```

---

# 24. Phase 2 Interview / SQL 50 Practice

1. Find employees whose cleaned name starts with `A`, ignoring case.

2. Find employees whose email ends with `@example.com`.

3. Find employees whose cleaned name contains `a`, ignoring case.

4. Display:
   ```text
   name | cleaned_name | name_length
   ```
   where spaces are removed before calculating length.

5. Create:
   ```text
   name | employee_info
   ```
   where:
   ```text
   Abdul Muqeet - Bangalore - IT
   ```

6. Find employees who joined before January 1, 2023.

7. Find employees who joined in 2023.

8. Display the employee name and joining year.

9. Display the employee name and one-year anniversary.

10. Find active employees who joined after January 1, 2023 and whose cleaned name starts with A, ignoring case.

---

# 25. Phase 2 Cheat Sheet

```text
LENGTH()       → count characters
UPPER()        → uppercase
LOWER()        → lowercase
TRIM()         → remove outer spaces
REPLACE()      → replace text
CONCAT()       → join strings
SUBSTRING()    → extract a section
LEFT()         → take from left
RIGHT()        → take from right

LIKE           → pattern matching
ILIKE          → case-insensitive pattern matching
%              → any number of characters
_              → exactly one character

CURRENT_DATE   → current date
CURRENT_TIMESTAMP → current date + time
EXTRACT()      → get date part
< / >          → compare dates
BETWEEN        → inclusive range
INTERVAL       → add/subtract time
date - date    → number of days between dates
```

---

# 26. Phase 2 Completion Checklist

- [x] LENGTH()
- [x] UPPER()
- [x] LOWER()
- [x] TRIM()
- [x] REPLACE()
- [x] CONCAT()
- [x] SUBSTRING()
- [x] LEFT()
- [x] RIGHT()
- [x] LIKE
- [x] ILIKE
- [x] `%`
- [x] `_`
- [x] CURRENT_DATE
- [x] CURRENT_TIMESTAMP
- [x] EXTRACT()
- [x] Date comparison
- [x] Date ranges
- [x] INTERVAL
- [x] Date arithmetic
- [x] Date difference

---

# 27. Phase 3 — Next

Now move to:

```text
Aggregate Functions
        ↓
COUNT()
SUM()
AVG()
MIN()
MAX()
        ↓
GROUP BY
        ↓
HAVING
```

Phase 3 is more important for SQL 50, analytics, interviews, and real-world SQL than spending a huge amount of time memorizing every Phase 2 function.
