{:toc}

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
WHERE is designed to filter rows. HAVING is designed to filter groups or aggregates. HAVING must always directly follow the GROUP BY clause.
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

#PostGIS
Use this resource below!
http://postgis.net/workshops/postgis-intro/index.html

## Setup
1. Download PostGIS by installing OpenGeoSuite, see link. http://postgis.net/workshops/postgis-intro/installation.html test.
2. PostGIS has a number of administrative front ends. The primary is the psql command line tool. Free and open source PgAdmin is another popular tool.
3. After creating a new database with postgres as the owner add the PostGIS spatial extention as follows:
```sql
CREATE EXTENSION postgis;
```
Confirm Postgis is installed with:
```sql
SELECT postgis_full_version();
```
## Functions 

Length of strings:
```sql
SELECT char_length(name)
FROM nyc_neighborhoods
WHERE boroname = 'Brooklyn';
  ```
Aggregates:
```sql
SELECT boroname, avg(char_length(name)), stddev(char_length(name))
FROM nyc_neighborhoods
GROUP BY boroname;
  
SELECT
boroname,
100 * Sum(popn_white)/Sum(popn_total) AS white_pct
FROM nyc_census_blocks
GROUP BY boroname;
```
## Geometries

Postgis uses a geometry column of type geometry, in practice often called geom.
```sql
CREATE TABLE geometries (name varchar, geom geometry);

INSERT INTO geometries VALUES
  ('Point', 'POINT(0 0)'),
  ('Linestring', 'LINESTRING(0 0, 1 1, 2 1, 2 2)'),
  ('Polygon', 'POLYGON((0 0, 1 0, 1 1, 0 1, 0 0))'),
  ('PolygonWithHole', 'POLYGON((0 0, 10 0, 10 10, 0 10, 0 0),(1 1, 1 2, 2 2, 2 1, 1 1))'),
  ('Collection', 'GEOMETRYCOLLECTION(POINT(2 0),POLYGON((0 0, 1 0, 1 1, 0 1, 0 0)))');

SELECT name, ST_AsText(geom) FROM geometries;
```
There are two metadata tables in Postgis
1. spatial_ref_sys : this lists all spatial reference systems known to the database
2. geometry_columns : 

### Representing Real World Objects
```sql
SELECT name, ST_GeometryType(geom), ST_NDims(geom), ST_SRID(geom)
  FROM geometries;
```
#### Points
Read coordinates from a point:
```sql
SELECT ST_X(geom), ST_Y(geom)
  FROM geometries
  WHERE name = 'Point';
 
 -- The following would return the first 10 records from the geometry column:
 
 SELECT name, ST_AsText(geom)
  FROM nyc_subway_stations
  LIMIT 10;
  ```
#### Lines

Some specific spatial functions for linestrings:

 1. ST_Length(geometry) returns the length of the linestring
 2. ST_StartPoint(geometry) returns the first coordinate as a point
 3. ST_EndPoint(geometry) returns the last coordinate as a point
 4. ST_NPoints(geometry) returns the number of coordinates in the linestring

```sql
SELECT ST_AsText(geom)
  FROM geometries
  WHERE name = 'Linestring';
  
  -- Length as follows:
  
SELECT ST_Length(geom)
  FROM geometries
  WHERE name = 'Linestring';
```
#### Polygons

Some specific spatial functions for Polygons:
1. ST_Area(geometry) returns the area of the polygons
2. ST_NRings(geometry) returns the number of rings (usually 1, more of there are holes)
3. ST_ExteriorRing(geometry) returns the outer ring as a linestring
4. ST_InteriorRingN(geometry,n) returns a specified interior ring as a linestring
5. ST_Perimeter(geometry) returns the length of all the rings

In SQL the % symbol is used along with LIKE to do globbing. 
```sql
SELECT ST_AsText(geom)
  FROM geometries
  WHERE name LIKE 'Polygon%';

-- To find the area:

SELECT name, ST_Area(geom)
  FROM geometries
  WHERE name LIKE 'Polygon%';
```

#### Collections: 

Refer to the Postgis Totorial Site for a list of geometry functions and documentation:
http://postgis.net/workshops/postgis-intro/geometries.html

### Geometry Input and Output: 

Postgis supports many formats for other applications, WKT, WKB, GML, KML, GeoJSON and SVG.
Here’s an example that consumes GML and output JSON:

```sql
SELECT ST_AsGeoJSON(ST_GeomFromGML('<gml:Point><gml:coordinates>1,1</gml:coordinates></gml:Point>'));
```
#### Geometry Exercises: 
```sql
--“How many census blocks in New York City have a hole in them?”
SELECT Count(*)
  FROM nyc_census_blocks
  WHERE ST_NumInteriorRings(ST_GeometryN(geom,1)) > 0;
  
--“What is the total length of streets (in kilometers) in New York City?” 
SELECT Sum(ST_Length(geom)) / 1000
  FROM nyc_streets;

--“What is the JSON representation of the boundary of the ‘West Village’?”
SELECT ST_AsGeoJSON(geom)
  FROM nyc_neighborhoods
  WHERE name = 'West Village';
  
--“What is the length of streets in New York City, summarized by type?”
SELECT type, Sum(ST_Length(geom)) AS length
FROM nyc_streets
GROUP BY type
ORDER BY length DESC;
```
## Schemas: 
Schemas allow two things:
1. For production purposes, they improve management.
2. Keeps different users from treading on each other as users can have different accessibilities. 

Manipulating the search_path is a nice way to provide access to tables in multiple schemas without lots of extra typing.
```sql
ALTER USER postgres SET search_path = census, public;
```
By default, every role in Oracle is given a personal schema. This is a nice practice to use for PostgreSQL users too, and is easy to replicate using PostgreSQL roles, schemas, and search paths.

```sql
CREATE USER myuser WITH ROLE postgis_writer;
CREATE SCHEMA myuser AUTHORIZATION myuser;
```
## Spatial Relationships: 
ST_Equals(geometry A, geometry B) tests the spatial equality of two geometries.
```sql
SELECT name, geom, ST_AsText(geom)
FROM nyc_subway_stations
WHERE name = 'Broad St';
--Then, plug the geometry representation back into an ST_Equals test:
SELECT name
FROM nyc_subway_stations
WHERE ST_Equals(geom, '0101000020266900000EEBD4CF27CF2141BC17D69516315141');
```
ST_Intersects, ST_Crosses, and ST_Overlaps test whether the interiors of the geometries intersect, and ST_Disjoint checks that they do not intersect, although checking "not intersects" is better because they can be spatially indexed.
```sql
SELECT name, ST_AsText(geom)
FROM nyc_subway_stations
WHERE name = 'Broad St';

SELECT name, boroname
FROM nyc_neighborhoods
WHERE ST_Intersects(geom, ST_GeomFromText('POINT(583571 4506714)',26918));
```
ST_Touches
ST_Within and ST_Contains
