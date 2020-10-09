# SQL-Cheat-Sheet

**QUERIES**
```sql
SELECT first_name AS name 
FROM people 
WHERE name = 'Daniel' AND age = 34;
```

SELECT DISTINCT first_name FROM people;
//= equal, <> not equal to

SELECT first_name AS F_name 
FROM people 
WHERE f_name LIKE "Fort%AL"; //this tells PG to search anything with these first four letters and ending with those 2. 
//% is a wild card. AS is _ underscore.
WHERE f_name LIKE "____, KS"; //will return Hays, KS for example.
or WHERE f_name LIKE "____, %" //would return all four letter names regardless of states (KS etc).

WHERE cancellation_code IS NULL;
WHERE cancellation_code IS NOT NULL; // ie, where there are values

//OPERATORS
AND
OR
BETWEEN
WHERE age BETWEEN 19 and 35;

WHERE first_name IN ("Jimmy", "Jane", "etc") //instead of multiple OR statements
//opposite of in is NOT IN, ie dont include these names.

//AND, OR etc have a hierachy, AND first. TO avoid confusion at runtime, use parentheses!
//Use parentheses when writing complex expressions.

**JOINS**
Three types of joins using keys.
Primary keys and foreign keys

*INNER* 
Fields exsist in both tables.

SELECT customers.first_name,
		customers.last_name,
		orders.order_date,
		orders.order_amount
FROM customers
INNER JOIN orders
ON customers.customer_id = orders.customer_id
WHERE customers.last_name = "Dodd";

//INNER JOIN has two shortcuts!

FROM customers
JOIN orders //default is inner
ON customers.customer_id = orders.customer_id

FROM customers,
orders
WHERE ustomers.customer_id = orders.customer_id

