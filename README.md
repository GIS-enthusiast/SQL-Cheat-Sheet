# SQL-Cheat-Sheet

## Queries
```sql
SELECT first_name AS name 
FROM people 
WHERE name = 'Daniel' AND age = 34;

SELECT DISTINCT first_name 
FROM people;
WHERE name = 'Daniel' AND age = 34 AND age <> 40;
```
Note: = equal, <> not equal to

```sql
SELECT first_name AS F_name 
FROM people 
WHERE f_name LIKE 'Fort%AL'; 
```
This tells Postgres to search anything with these first four letters and ending with those two letters, AL. 

% is a wild card. As is _, underscore.
```sql
WHERE f_name LIKE '____, KS';
```
Will return Hays, KS for example.
```sql
WHERE f_name LIKE '____, %'
```
Would return all four letter names regardless of states (KS etc).

Null values are not zero in SQL, they represent missing information. IS NULL and IS NOT NULL can therefore be used to query missing or availbale information.
```sql
WHERE cancellation_code IS NULL;
WHERE cancellation_code IS NOT NULL; 
```

## Operators
AND
OR
BETWEEN
```sql
WHERE age BETWEEN 19 and 35;
```

```sql
WHERE first_name IN ('Jimmy', 'Jane', 'etc'); 
```
IN is used instead of multiple OR statements
```sql
WHERE first_name NOT IN ('Jimmy', 'Jane', 'etc'); 
```
Opposite of IN is NOT IN, ie dont include these names.

AND, OR etc have a hierachy, AND is higher than OR. TO avoid confusion at runtime, use parentheses!
Use parentheses when writing complex expressions.

## Table Aliases
Saves time by writing less. Three conventions:
1. First Letter, ie. c for customers.
2. Shortcut, ie. cust for customers.
3. ABC sequential, ie. a for customers, b for orders.

```sql
SELECT c.first_name,
		c.last_name,
		o.first_name,
		o.last_name
FROM customers AS c
INNER JOIN orders AS o
ON c.customer_id = o.customer_id
WHERE c.last_name = 'Dodd';
```
Shortcut is to simply drop the AS

```sql
FROM customers c
INNER JOIN orders o
ON c.customer_id = o.customer_id
```
## Joins
Three types of joins.
Primary keys and foreign keys. Primary key is the first column in a table. Foreign keys are columns from other tables.

### Inner Join
Fields must exsist in both tables.
```sql
SELECT customers.first_name,
		customers.last_name,
		orders.order_date,
		orders.order_amount
FROM customers
INNER JOIN orders
ON customers.customer_id = orders.customer_id
WHERE customers.last_name = 'Dodd';
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
## Outer Joins
Left or Right outer join determines which table we want to have precedence. From example for a left outer join it is all rows from the left table and those that match from the right table. Left joins are far more prevelent and encouraged as they are typically easier to read.
```sql
SELECT c.first_name,
		c.last_name,
		o.first_name,
		o.last_name
FROM customers c
LEFT OUTER JOIN orders o
ON c.customer_id = o.customer_id
WHERE c.last_name = 'Dodd';
```
ON specifies fields to join.
Shortcut is to ommit OUTER
```sql
FROM customers c
LEFT JOIN orders o
ON c.customer_id = o.customer_id

FROM customers c
RIGHT JOIN orders o
ON c.customer_id = o.customer_id
```
## Full Outer Join (Full Join)
All rows from left and right
```sql
SELECT c.first_name,
		c.last_name,
		o.first_name,
		o.last_name
FROM customers c
FULL OUTER JOIN orders o
ON c.customer_id = o.customer_id;
```
## Look Up Tables
Commoning used in practice to join abbreviations/codes to full names.
```sql
SELECT p.flight_date,
		p.carrier,
		cc.carrier_desc AS airline,
		p.cancellation_code,
		ca.cancel_desc
FROM performance p
INNER JOIN codes.carrier cc
ON p.carrier = cc.carrier_code
LEFT JOIN codes_cancellation ca
ON p.cancellation_code = ca.cancellation_code;
```
## Sorting 
By default results are returned in the order in which they are stored. We can change this using ORDER BY, SORT BY ASC and SORT BY DESC.
```sql
SELECT name, state
FROM residency
ORDER BY name;

SELECT name, age
FROM people
SORT BY DESC age;
```
You can ORDER BY on more than one column.
```sql
SELECT name, state
FROM residency
ORDER BY state, name;

SELECT name, state
FROM residency
ORDER BY state DESC, name ASC;
```
Short cut
```sql
SELECT name, state
FROM residency
ORDER BY 2 DESC, 1 ASC;
```
## Aggregate Functions
Count, Sum, Average, Min, Max
```sql
SELECT AVG(age) AS avg_age
FROM person;
```
The following would return the average departure delay for different airlines.
```sql
SELECT p.mkt_carrier,
	cc.carrier_desc,
	AVG(p.dep_delay_new) AS departure_delay,
	AVG(p.arr_delay_new) AS arrival delay
FROM performance p
INNER JOIN code_carrier cc
ON p.mkt_carrier = cc.carrier_code
GROUP BY p.mkt_carrier,
		cc.carrier_desc
ORDER BY AVG(p.dep_delay_new) DESC;
```
Note above: Non aggregate fields must appear in the GROUP BY statement.
### Having (Filtering Aggregate Results)
WHERE is designed to filter rows. HAVING is designed to filter groups or aggregates.
```sql
SELECT grade_lvl,
	AVG(age) AS avg_age
FROM person
GROUP BY grade_lvl
HAVING AVG(age) < 19;

SELECT p.mkt_carrier,
	cc.carrier_desc,
	AVG(p.dep_delay_new) AS departure_delay,
	AVG(p.arr_delay_new) AS arrival delay
FROM performance p
INNER JOIN code_carrier cc
ON p.mkt_carrier = cc.carrier_code
GROUP BY p.mkt_carrier,
		cc.carrier_desc
HAVING AVG(p.dep_delay_new) < 15
AND AVG(p.arr_delay_new) < 15
ORDER BY AVG(p.dep_delay_new) DESC;
```
The above with only show average arrival and depatures delays less than 15 minutes for airlines in descending order for departure delays.


