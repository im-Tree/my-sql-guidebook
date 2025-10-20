# My Personal SQL Guidebook (dvdrental Edition)

This guide serves as a personal reference for advanced SQL queries, built using the `dvdrental` sample database in PostgreSQL. The questions are designed to cover common business scenarios and technical concepts required for data engineer and data scientist interviews.

## 1\. Database Setup

This guide uses the pre-existing `dvdrental` database. However, to demonstrate Data Definition Language (DDL) and Data Manipulation Language (DML) commands, I will create, insert into, and update a new, separate table called `customer_feedback`.

### Q1: Create, Insert, and Update a New Table

**Query 1.1: `CREATE TABLE`**

```sql
CREATE TABLE customer_feedback (
    feedback_id SERIAL PRIMARY KEY,
    customer_id INT NOT NULL,
    feedback_date DATE NOT NULL,
    comment TEXT,
    FOREIGN KEY (customer_id) REFERENCES customer (customer_id)
);
```

**Query 1.2: `INSERT INTO`**
```sql
INSERT INTO customer_feedback (customer_id, feedback_date, comment)
VALUES (1, '2025-10-18', 'Great selection of movies!');
```
  
**Query 1.3: `UPDATE`**

```sql
UPDATE customer_feedback
SET comment = 'Great selection of movies! Really enjoyed the new arrivals.'
WHERE feedback_id = 1;
```

-----
## 2\. Common and Essential Keywords and Functions
### Part 1: SQL Commands (Keywords by Type)

These are the primary "verbs" you use to tell the database what to do.

#### DQL (Data Query Language)
  
| Keyword | Meaning & Usage |
| :--- | :--- |
| `SELECT` | The main command to retrieve data from one or more tables. |
| `DISTINCT`| Used with `SELECT` to return only unique (different) values. |

#### DML (Data Manipulation Language)
Used to modify data within tables.

| Keyword | Meaning & Usage |
| :--- | :--- |
| `INSERT INTO` | Adds new rows of data to a table. |
| `UPDATE` | Modifies existing data in rows within a table. |
| `DELETE` | Removes one or more rows from a table (often used with `WHERE`). |

#### DDL (Data Definition Language)
Used to define or change the database structure.

| Keyword | Meaning & Usage |
| :--- | :--- |
| `CREATE TABLE` | Creates a new table in the database. |
| `ALTER TABLE` | Modifies an existing table's structure (e.g., add/drop column, change datatype). |
| `DROP TABLE` | Completely deletes a table, its structure, and all its data. |
| `TRUNCATE TABLE`| Deletes all data *inside* a table, but the table structure remains. It's much faster than `DELETE FROM table;` (no `WHERE` clause). |
| `CREATE VIEW` | Creates a virtual table based on a `SELECT` query. |
| `CREATE INDEX`| Creates an index on a column to speed up search operations. |

#### TCL (Transaction Control Language)
Used to manage transactions (groups of SQL statements).

| Keyword | Meaning & Usage |
| :--- | :--- |
| `BEGIN` | Starts a new transaction. |
| `COMMIT` | Saves all changes made during the current transaction. |
| `ROLLBACK` | Undoes all changes made during the current transaction. |

### Part 2: SQL Clauses & Operators
These are the "connectors" and "filters" that build the query.

#### Core Clauses
These build the structure of a `SELECT` statement.

| Clause | Meaning & Usage |
| :--- | :--- |
| `FROM` | Specifies which table(s) to query. |
| `WHERE` | **Filters rows *before* any grouping.** This is where you put your main filters. |
| `GROUP BY` | Groups rows that have the same values in specified columns. Used with aggregate functions. |
| `HAVING` | **Filters *groups* after aggregation.** Used *after* `GROUP BY`. |
| `ORDER BY` | Sorts the final result set in ascending (`ASC`) or descending (`DESC`) order. |
| `LIMIT` / `FETCH` | Restricts the number of rows returned by the query. |

#### JOIN Clauses
Used to combine rows from two or more tables based on a related column.

| Clause | Meaning & Usage |
| :--- | :--- |
| `INNER JOIN` | Returns only the rows that have matching values in *both* tables. |
| `LEFT JOIN` | Returns *all* rows from the left table, and the matched rows from the right table. (If no match, right side columns are `NULL`). |
| `RIGHT JOIN`| Returns *all* rows from the right table, and the matched rows from the left table. (If no match, left side columns are `NULL`). |
| `FULL OUTER JOIN`| Returns all rows when there is a match in *either* the left or right table. Combines `LEFT` and `RIGHT` join. |
| `ON` | Specifies the join condition (e.g., `ON a.id = b.id`). |
| `USING` | A shorthand for `ON` when the join columns have the exact same name (e.g., `USING (customer_id)`). |

#### Set Operators
Used to combine the results of two `SELECT` statements.

| Operator | Meaning & Usage |
| :--- | :--- |
| `UNION` | Combines two result sets and removes duplicate rows. |
| `UNION ALL` | Combines two result sets and *keeps* all duplicate rows. Much faster than `UNION`. |
| `INTERSECT` | Returns only the rows that appear in *both* result sets. |
| `EXCEPT` | Returns rows from the first result set that do *not* appear in the second. |

#### Common Operators
Used primarily in `WHERE` and `HAVING` clauses.

| Operator | Meaning & Usage |
| :--- | :--- |
| `AND`, `OR`, `NOT` | Logical operators to combine multiple conditions. |
| `IN (...)` | Checks if a value matches any value in a list. |
| `BETWEEN ... AND ...`| Checks if a value is within a range (inclusive). |
| `LIKE` | Searches for a pattern in a string (e.g., `LIKE 'A%'` finds strings starting with 'A'). |
| `IS NULL` / `IS NOT NULL`| Checks if a value is `NULL`. You cannot use `= NULL`. |
| `AS` | Creates an "alias" (a temporary, new name) for a column or table. |
| `WITH ... AS ()` | Defines a **Common Table Expression (CTE)**. A temporary, named result set that you can reference within your main query. Helps readability. |


### Part 3: SQL Functions (The Tools)
Functions take an input (like a column) and return a value.

#### Aggregate Functions
Used with `GROUP BY` to perform calculations on a set of rows.

| Function | Meaning & Usage |
| :--- | :--- |
| `COUNT()` | Counts the number of rows. `COUNT(*)` counts all rows; `COUNT(column)` counts **non-NULL** rows. |
| `COUNT(DISTINCT ...)`| Counts the number of *unique* values in a column. |
| `SUM()` | Calculates the sum of a numeric column. |
| `AVG()` | Calculates the average of a numeric column. |
| `MAX()` | Finds the maximum value in a set. |
| `MIN()` | Finds the minimum value in a set. |

#### Window Functions
Perform calculations across a "window" (a set of related rows) without collapsing them like `GROUP BY`.

| Function | Meaning & Usage |
| :--- | :--- |
| `OVER()` | The clause that defines the window. `OVER (PARTITION BY ... ORDER BY ...)` |
| `PARTITION BY` | Divides the rows into partitions (groups). This is the "GROUP BY" for window functions. |
| `ROW_NUMBER()` | Assigns a unique, sequential integer to each row in its partition (e.g., 1, 2, 3, 4). |
| `RANK()` | Assigns a rank to each row. Skips ranks after ties (e.g., 1, 2, 2, 4). |
| `DENSE_RANK()` | Assigns a rank to each row. Does *not* skip ranks after ties (e.g., 1, 2, 2, 3). |
| `LEAD(col, N)` | Accesses data from the `N`-th row *after* the current row in the partition. |
| `LAG(col, N)` | Accesses data from the `N`-th row *before* the current row in the partition. |
| `SUM() OVER (...)` | Creates a running total or cumulative sum. |

#### Conditional Functions
Used for `if/then/else` logic within a query.

| Function | Meaning & Usage |
| :--- | :--- |
| `CASE WHEN ... THEN ... END`| The main `if/then/else` logic block. `CASE WHEN condition1 THEN result1 ELSE result2 END` |
| `COALESCE(val1, val2, ...)` | **Returns the first non-NULL value** in the list. Perfect for replacing `NULL` with a default value (e.g., `COALESCE(discount, 0)`). |
| `NULLIF(val1, val2)` | Returns `NULL` if `val1` equals `val2`. Otherwise, returns `val1`. Used to prevent divide-by-zero errors (e.g., `amount / NULLIF(quantity, 0)`). |

#### String Functions
Used to manipulate text data.

| Function | Meaning & Usage |
| :--- | :--- |
| `CONCAT(str1, str2, ...)` | Joins two or more strings together. (PostgreSQL, MySQL). |
| `CONCAT_WS(sep, str1, str2, ...)`| Concatenate With Separator. Joins strings with a separator (e.g., `' '`) and skips `NULL` values. |
| `||` | (PostgreSQL/Oracle) The operator to join strings. `first_name || ' ' || last_name` |
| `SUBSTRING(str FROM pos FOR len)`| Extracts a part of a string. |
| `LENGTH()` / `CHAR_LENGTH()`| Returns the number of characters in a string. |
| `LOWER()` / `UPPER()` | Converts a string to lowercase or uppercase. |
| `TRIM()` / `LTRIM()` / `RTRIM()`| Removes leading, trailing, or all whitespace from a string. |
| `REPLACE(str, old, new)` | Replaces all occurrences of a substring with a new one. |

#### Date/Time Functions
(Syntax can vary, these are common in PostgreSQL)

| Function | Meaning & Usage |
| :--- | :--- |
| `NOW()` / `CURRENT_TIMESTAMP` | Returns the current timestamp (date and time). |
| `CURRENT_DATE` | Returns the current date only. |
| `EXTRACT(part FROM date)` | Gets a specific part from a date (e.g., `EXTRACT(MONTH FROM rental_date)`). |
| `DATE_TRUNC(part, date)` | Truncates a timestamp to a specific part (e.g., `'month'` truncates `2025-10-19 18:30:00` to `2025-10-01 00:00:00`). Great for `GROUP BY` month. |
| `INTERVAL` | A datatype used to add or subtract time from timestamps (e.g., `NOW() - INTERVAL '5 days'`). |

#### Conversion Functions
Used to change data from one type to another.

| Function | Meaning & Usage |
| :--- | :--- |
| `CAST(expression AS datatype)`| The standard SQL way to convert a datatype (e.g., `CAST(payment_id AS TEXT)`). |
| `::` | (PostgreSQL shortcut) A non-standard, fast way to cast (e.g., `payment_id::text`). |
| `TO_CHAR(date/num, format)` | (PostgreSQL/Oracle) Converts a date or number to a formatted string. |
| `TO_DATE(text, format)` | (PostgreSQL/Oracle) Converts a string to a date. |
-----

## 3\. Example Questions  

### Q2: Find the title and length of top 5 longest 'G' rated films. 

```sql
SELECT 
	*
FROM 
	film
WHERE 
	rating = 'G'
ORDER BY 
	length DESC
LIMIT 5;
```

**Output**:

```
| title               | length |
|---------------------|--------|
| Muscle Bright       | 185    |
| CONTROL ANTHEM      | 185    |
| Darn Forrester      | 185    |
| Moonwalker Fool     | 184    |
| Catch Amistad       | 183    |
```

### Q3: Find out how many films exist for each rating and only show ratings with more than 200 films.

```sql
SELECT 
	rating,
	COUNT(*) AS count
FROM 
	film
GROUP BY
	rating
Having 
	COUNT(*) > 200;
```

**Output**:

```
| rating | film_count |
|--------|------------|
| NC-17  | 210        |
| PG-13  | 223        |
```

### Q4: Get a list of all actors who starred in a specific film (e.g. Muscle Bright).
```sql
SELECT
	actor.first_name,
	actor.last_name
FROM 
	actor 
JOIN 
	film_actor
ON 
	actor.actor_id = film_actor.actor_id
JOIN 
	film
ON 
	film_actor.film_id = film.film_id
WHERE
	film.title LIKE 'Muscle Bright'
```

**Output**:

```
| first_name | last_name   |
|------------|-------------|
| Sean       | Williams    |
| Matthew    | Leigh       |
| Groucho    | Williams    |
| Jeff       | Silverstone |
| Matthew    | Carrey      |
```

### Q5: Get a list of *all* customers and the total number of rentals they've made. Include customers who have never rented anything.
```sql
SELECT 
	customer.first_name,
	customer.last_name,
	COUNT(rental.rental_id) AS total_rentals
FROM
	customer
LEFT JOIN rental ON customer.customer_id = rental.customer_id
GROUP BY customer.customer_id
ORDER BY total_rentals DESC;
```

**Output**:

```
| first_name | last_name | total_rentals |
|------------|-----------|---------------|
| Eleanor    | Hunt      | 46            |
| Karl       | Seal      | 45            |
| Clara      | Shaw      | 42            |
...
| Brain      | Wyman     | 12            |
```

### Q6: Identify the top 5 customers who have spent the most money.
    
```sql
SELECT 
	customer.first_name,
	customer.last_name,
	SUM(payment.amount) AS total_spending
FROM
	customer
LEFT JOIN payment ON customer.customer_id = payment.customer_id
GROUP BY customer.customer_id
ORDER BY total_spending DESC
LIMIT 5;
```

**Output**:

```
| first_name | last_name | total_spent |
|------------|-----------|-------------|
| ELEANOR    | HUNT      | 211.55      |
| KARL       | SEAL      | 208.58      |
| MARION     | SNYDER    | 194.61      |
| RHONDA     | KENNEDY   | 191.62      |
| CLARA      | SHAW      | 189.60      |
```

### Q7: Classify all customers into 'Gold', 'Silver', or 'Bronze' tiers based on their total spending. Specically: (1)spending > 150: Gold; (2)100 < spending <= 150: Silver; (3)spending <= 100: Bronze. Display the full names of customers in the 'name' column.

```sql
WITH CustomerSpending AS (
    SELECT
        c.customer_id,
        c.first_name,
        c.last_name,
        COALESCE(SUM(p.amount), 0) AS total_spent
    FROM
        customer AS c
    LEFT JOIN
        payment AS p ON c.customer_id = p.customer_id
    GROUP BY
        c.customer_id
)
SELECT
    first_name,
    last_name,
    total_spent,
    CASE
        WHEN total_spent > 150 THEN 'Gold'
        WHEN total_spent > 100 THEN 'Silver'
        ELSE 'Bronze'
    END AS customer_tier
FROM
    CustomerSpending
ORDER BY
    total_spent DESC;
```

**Output**:

```
|     name     | total_spent | customer_tier |
|--------------|-------------|---------------|
| Eleanor Hunt | 211.55      | Gold          |
| Karl Seal    | 208.58      | Gold          |
| Marion Snyder| 194.61      | Gold          |
...
| Brian Wyman  | 27.93       | Bronze        |
```

### Q8: For every customer, find the title and date of their single most recent rental. Display the full names of customers in the 'name' column.

```sql
WITH RankedRentals AS(
	SELECT
		CONCAT(customer.first_name, ' ', customer.last_name) AS name,
		ROW_NUMBER() OVER(
			PARTITION BY customer.customer_id
			ORDER BY rental.rental_date DESC
		) AS rental_num,
		rental.rental_date,
		film.title
	FROM customer 
	LEFT JOIN rental ON customer.customer_id = rental.customer_id
	LEFT JOIN inventory ON rental.inventory_id = inventory.inventory_id
	LEFT JOIN film ON inventory.film_id = film.film_id
)
SELECT 
	name,
	rental_date,
	title
FROM RankedRentals
WHERE rental_num = 1
	
```

**Output**:

```
|       name       |         title        |     rental_date     |
|------------------|----------------------|---------------------|
| Mary Smith       | Bikini Borrowers     | 2005-08-22 20:03:46 |
| Patricia Johnson | Open African         | 2005-08-23 17:39:35 |
| Linda Williams   | Lucky Flying         | 2005-08-23 07:10:14 |
...
| Austin Cintron   | Blues Instinct       | 2005-08-23 11:25:00 |
```

### Q9: Find out which film category generates the most revenue and rank them.
```sql
SELECT
	category.name AS category_name,
	SUM(payment.amount) AS category_revenue,
	RANK() OVER(
		ORDER BY SUM(payment.amount) DESC
		)
FROM category
JOIN film_category ON category.category_id = film_category.category_id
JOIN inventory ON film_category.film_id = inventory.film_id
JOIN rental ON inventory.inventory_id = rental.inventory_id
JOIN payment ON rental.rental_id = payment.rental_id
GROUP BY category.category_id
```

**Output**:

```
| category_name | total_revenue | revenue_rank |
|---------------|---------------|--------------|
| Sports        | 4892.19       | 1            |
| Sci-Fi        | 4336.01       | 2            |
| Animation     | 4245.31       | 3            |
...
| Travel        | 3227.36       | 15           |
| Music         | 3071.52       | 16           |
```

### Q10: For customer ID 1 and 2, calculate the time elapsed between each of her rentals, display their name.

```sql
WITH CustomerRental AS (
	SELECT 
		customer.customer_id,
		CONCAT(customer.first_name, ' ', customer.last_name) AS name,
		rental.rental_date,
		LAG(rental.rental_date, 1) OVER(
			PARTITION BY customer.customer_id
			ORDER BY rental.rental_date
		) AS pre_rental_date
	FROM customer
	JOIN rental ON customer.customer_id = rental.customer_id
	WHERE customer.customer_id IN (1, 2)
)
SELECT
	customer_id,
	name,
	rental_date, 
	pre_rental_date,
	rental_date - pre_rental_date AS time_gap
FROM CustomerRental
WHERE pre_rental_date is NOT NULL
ORDER BY customer_id, rental_date;
```

**Output**:

```
| customer_id |        name         | rental_date         | prev_rental_date    |       time_gap       |
|-------------|---------------------|---------------------|---------------------|----------------------|
| 1           | Mary Smith          | 2005-05-28 10:35:23 |2005-05-25 11:30:37  | 2 days 23:04:46      |
| 1           | Mary Smith          | 2005-06-15 00:54:12 |2005-05-28 10:35:23  | 17 days 14:18:49     |
...
| 2           | Patricia Johnson    | 2005-08-23 17:39:35 |2005-08-22 13:53:04  | 1 day 03:46:31     |
```

### Q11: Find all actors who have appeared in *both* 'Comedy' films and 'Horror' films.
```sql
SELECT
    a.actor_id,
    a.first_name,
    a.last_name
FROM
    actor AS a
JOIN film_actor AS fa ON a.actor_id = fa.actor_id
JOIN film_category AS fc ON fa.film_id = fc.film_id
JOIN category AS c ON fc.category_id = c.category_id
WHERE
    c.name = 'Comedy'

INTERSECT

SELECT
    a.actor_id,
    a.first_name,
    a.last_name
FROM
    actor AS a
JOIN film_actor AS fa ON a.actor_id = fa.actor_id
JOIN film_category AS fc ON fa.film_id = fc.film_id
JOIN category AS c ON fc.category_id = c.category_id
WHERE
    c.name = 'Horror'
ORDER BY
    last_name;
```

**Output**:

```
| actor_id | first_name | last_name |
|----------|------------|-----------|
| 182      | Debbie     | Akroyd    |
| 58       | Christian  | Akroyd    |
| 194      | Meryl      | Allen     |
| 118      | Cuba       | Allen     |
...
```


