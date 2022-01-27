# Hospital

## Tables

**Groups**

* **gr_id** — groups ID;
* **gr_name** — groups name;
* **gr_temp** — groups storage temperature.

> CREATE TABLE groups (
> gr_id SERIAL PRIMARY KEY, 
> gr_name VARCHAR(100), 
> gr_temp INT
> );

**Analysis**

* **an_id** — analysis ID;
* **an_name** — analysis name;
* **an_cost** — analysis cost;
* **an_price** — analysis price;
* **an_group** — analysis group.

> CREATE TABLE analysis (
> an_id SERIAL PRIMARY KEY, 
> an_name VARCHAR(100), 
> an_cost INT, 
> an_price INT, 
> an_group INT REFERENCES groups (gr_id)
> );

**Orders**

* **ord_id** — orders ID;
* **ord_datetime** — orders date;
* **ord_an** — analysis ID.

> CREATE TABLE orders (
> ord_id SERIAL PRIMARY KEY, 
> ord_datetime DATE, 
> ord_an INT REFERENCES analysis (an_id)
> );

## Data

**Groups**

> INSERT INTO groups ("gr_name", "gr_temp") 
> VALUES ('Blood', 6), ('Urine', 8), ('Feces', 10), ('Gastric Juice', 8), ('Other' , 10);

**Analysis**

> INSERT INTO analysis ("an_name", "an_cost", "an_price", "an_group") 
> VALUES ('Coronavirus COVID-19', 1000, 1580, 1), 
> ('Taking a smear (from the pharynx, nose, ear, eyes, wound surface, etc.)', 30, 100, 5), 
> ('Blood group + Rh factor', 200, 430, 1), 
> ('Protein C', 500, 1100, 4), 
> ('Protein C Global', 200, 700, 4), 
> ('Protein S', 800, 1300, 4), 
> ('Alkaline phosphatase', 20, 120, 2), 
> ('Acid phosphatase', 30, 130, 3), 
> ('Uric acid', 30, 130, 2);

**Orders**

> INSERT INTO orders ("ord_datetime", "ord_an")
> VALUES ('2021-12-25', 1), 
> ('2021-12-25', 6), 
> ('2021-12-26', 2), 
> ('2021-12-26', 3), 
> ('2021-12-27', 7), 
> ('2021-12-27', 1), 
> ('2021-12-27', 2), 
> ('2021-12-28', 2), 
> ('2021-12-28', 1), 
> ('2021-12-28', 1), 
> ('2021-12-28', 1), 
> ('2021-12-29', 1), 
> ('2021-12-29', 4), 
> ('2021-12-30', 4), 
> ('2021-12-30', 2), 
> ('2021-12-30', 8);

## Tasks

 1. Display the name and price for all analyzes that were sold on December 22, 2021 and before the end of the week.
 2. Cumulatively calculate how the number of tests sold increased each month of each year, broken down by group.
 
## Answers

 1. > SELECT groups.gr_name, 
    > EXTRACT(year from orders.ord_datetime) AS "year", 
    > EXTRACT(month from orders.ord_datetime) AS "month", 
    > COUNT(analysis.an_price) AS "countw",
    > COALESCE(SUM(count(analysis.an_price)) OVER (PARTITION BY EXTRACT(year from orders.ord_datetime) ORDER BY EXTRACT(year from orders.ord_datetime), EXTRACT(month from orders.ord_datetime)
    > rows between unbounded preceding and current row), 0) AS total 
    > FROM orders 
    > JOIN analysis ON orders.ord_an = analysis.an_id 
    > JOIN groups ON groups.gr_id = analysis.an_group 
    > GROUP BY EXTRACT(year from orders.ord_datetime), EXTRACT(month from orders.ord_datetime), groups.gr_name 
    > ORDER BY EXTRACT(year from orders.ord_datetime), EXTRACT(month from orders.ord_datetime)
    
 2. > SELECT analysis.an_name AS "Анализ", orders.ord_datetime AS "Дата", analysis.an_price AS "Цена"
    > FROM orders 
    > JOIN analysis on orders.ord_an = analysis.an_id 
    > JOIN groups on groups.gr_id = analysis.an_group 
    > WHERE orders.ord_datetime BETWEEN  make_date(2021, 12, 22) AND make_date(2021, 12, 22) + CAST(7 - EXTRACT(isodow from make_date(2021, 12, 22)) AS INT);
