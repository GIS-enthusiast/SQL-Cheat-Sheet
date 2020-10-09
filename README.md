# SQL-Cheat-Sheet

**Querie**
```sql
SELECT first_name AS name 
FROM people 
WHERE name = 'Daniel' AND age = 34;

SELECT DISTINCT first_name 
FROM people;
WHERE name = 'Daniel' AND age = 34;
```
//= equal, <> not equal to

```sql
SELECT first_name AS F_name 
FROM people 
WHERE f_name LIKE "Fort%AL"; 
```
this tells Postgre to search anything with these first four letters and ending with those two letters, AL. 

% is a wild card. As is _, underscore.
```sql
WHERE f_name LIKE "____, KS";
```
will return Hays, KS for example.
```sql
WHERE f_name LIKE "____, %"
```
would return all four letter names regardless of states (KS etc).

Null values are not zero in SQL, they represent missing information. IS NULL and IS NOT NULL can therefore be used to query missing or availbale information.
```sql
WHERE cancellation_code IS NULL;
WHERE cancellation_code IS NOT NULL; 
```

**Operators**
AND
OR
BETWEEN
WHERE age BETWEEN 19 and 35;

```sql
WHERE first_name IN ("Jimmy", "Jane", "etc"); 
```
IN is used instead of multiple OR statements
```sql
WHERE first_name NOT IN ("Jimmy", "Jane", "etc"); 
```
Opposite of in is NOT IN, ie dont include these names.

AND, OR etc have a hierachy, AND is higher than OR. TO avoid confusion at runtime, use parentheses!
Use parentheses when writing complex expressions.

**Joins**
Three types of joins.
Primary keys and foreign keys. Primary key is the first column in a table. Foreign keys are columns from other tables.

*Inner Join* 
Fields must exsist in both tables.
```sql
SELECT customers.first_name,
		customers.last_name,
		orders.order_date,
		orders.order_amount
FROM customers
INNER JOIN orders
ON customers.customer_id = orders.customer_id
WHERE customers.last_name = "Dodd";
```
INNER JOIN has two shortcuts!
```sql
FROM customers
JOIN orders //default is inner
ON customers.customer_id = orders.customer_id;

FROM customers,
orders
WHERE ustomers.customer_id = orders.customer_id;
```
