# Auto

## Tables

**City**

* **code** — city code;
* **name** — city name;
* **population** — city population;

> CREATE TABLE city (
> code SERIAL PRIMARY KEY,
> name VARCHAR(50),
> population INT
> );

**Customer**

* **phone_num** — customer phone number;
* **firstname** — customer firstname;
* **lastname** — customer lastname;
* **city_code** — customer city code;
* **age** — customer age;

> CREATE TABLE customer (
> phone_num VARCHAR(15) PRIMARY KEY ,
> firstname VARCHAR(50),
> lastname VARCHAR(50),  
> city_code INT REFERENCES city (code),
> age INT	 
> );

**Auto**

* **reg_num** — auto registration number;
* **mark** — auto mark;
* **color** — auto color;
* **release_dt** — auto release date;
* **phone_num** — customer phone number;

> CREATE TABLE auto (
> reg_num VARCHAR(10) PRIMARY KEY,
> mark VARCHAR(10),
> color VARCHAR(15),
> release_dt DATE,
> cus_phone_num  VARCHAR(15) REFERENCES customer (phone_num)
> );

## Data

**City**

> INSERT INTO city ("code", "name", "population")
> VALUES (1,'Moskva', 10000000),
> (2, 'Vladimir', 500000),
> (3, 'Orel', 300000),
> (4, 'Kursk', 200000),
> (5, 'Kazan', 2000000),
> (7, 'Kotlas', 110000),
> (8, 'Murmansk', 400000),
> (9, 'Yaroslavl', 500000);

**Customer**

> INSERT INTO customer ("phone_num", "firstname", "lastname", "city_code", "age")
> VALUES ('9152222221','Andrey','Nikolaev', 1, 22),
> ('9152222222', 'Maxim', 'Moskitov', 1, 31),
> ('9153333333', 'Oleg', 'Denisov', 3, 34),
> ('9173333334', 'Alice', 'Nikina', 4, 31),
> ('9173333335', 'Tanya', 'Ivanova', 4, 31),
> ('9213333336', 'Aleksey', 'Ivanov', 7, 25),
> ('9213333331', 'Andrey', 'Nekrasov', 2, 27),
> ('9213333332', 'Misha', 'Rogozin', 2, 21),
> ('9214444444', 'Aleksey', 'Galkin', 1, 38);

**Auto**

> INSERT INTO auto ("reg_num", "mark", "color", "release_dt", "cus_phone_num") 
> VALUES (111114,'LADA', 'RED', '2008-01-01', '9152222221'),
> (111115, 'VOLVO', 'RED', '2013-01-01', '9173333334'),
> (111116, 'BMW', 'BLUE', '2015-01-01', '9173333334'),
> (111121, 'AUDI', 'BLUE', '2009-01-01', '9213333332'),
> (111122, 'AUDI', 'BLUE', '2011-01-01', '9213333336'),
> (111113, 'BMW', 'GREEN', '2007-01-01', '9214444444'),
> (111126, 'LADA', 'GREEN', '2005-01-01', null),
> (111117, 'BMW', 'BLUE', '2005-01-01', null),
> (111119, 'LADA', 'BLUE', '2017-01-01', '9213333331');

## Tasks

 1. Find the number of g letters in word Chargoggagoggmanchauggagoggchaubunagungamaugg*
 2. Select the city code, city name and population, where the city code is greater than the general city code by 2.
 3. Display the brand and color of the car - the firstname and lastname of the customer, for all customers who live in a city with a population of more than 1 million people.
 4. Display people who live in cities with a population greater than 1000000 if there are no people with the same name in the "customer" table.
 5. Display cities if there is no city in the table that starts with the same letter.
 6. Count the number of BMWs in the "auto" table.
 7. Display the mark of car and the number of cars not of this mark.
 8. Display how many cars of each color for BMW and LADA cars.
 9. Display the car brand - as well as how many cars of this brand are in the table, display records where the number of cars of this brand is more than 2, records should not be duplicated.

> Chargoggagoggmanchauggagoggchaubunagungamaugg is the full original name of American Webster Lake in Massachusetts.
 
## Answers

 1. > SELECT LENGTH('Chargoggagoggmanchauggagoggchaubunagungamaugg') - LENGTH(REPLACE('Chargoggagoggmanchauggagoggchaubunagungamaugg', 'g', '')) AS g

 2. > SELECT c.* FROM city
    > JOIN (SELECT * FROM city) AS c ON c.code = city.code + 2

 3. > SELECT auto.mark as auto_mark, auto.color as auto_color, cus.firstname, cus.lastname FROM auto
    > JOIN (
    > SELECT customer.firstname, customer.lastname, customer.phone_num FROM customer
    > JOIN (SELECT * FROM city) AS city ON city.code = customer.city_code AND city.population > 1000000
    > ) AS cus ON cus.phone_num = auto.cus_phone_num
 
 4. > SELECT customer.* FROM customer
    > JOIN (SELECT customer.firstname, COUNT(customer.firstname) FROM customer
    > JOIN (SELECT * FROM customer) AS cus ON cus.firstname = customer.firstname
    > GROUP BY customer.firstname
    > ) AS cus ON cus.firstname = customer.firstname AND cus.count = 1
    > JOIN (SELECT * FROM city) AS city ON city.code = customer.city_code AND city.population > 1000000
    
 5. > SELECT city.* FROM city
    > JOIN (
    > SELECT city.name, COUNT(city.name) FROM city
    > JOIN (SELECT * FROM city) AS c ON SUBSTRING(c.name, 1, 1) = SUBSTRING(city.name, 1, 1)
    > GROUP BY city.name
    > ) AS c ON c.name = city.name AND count = 1
 
 6. > SELECT COUNT(auto.mark) FROM auto
    > WHERE auto.mark = 'BMW'
    > GROUP BY auto.mark
    
 7. > SELECT auto.mark, (SELECT COUNT(*) as count FROM auto) - COUNT(auto.mark) AS count  FROM auto
    > GROUP BY auto.mark
    
 8. > SELECT auto.mark, auto.color, COUNT(*) FROM auto
    > WHERE auto.mark = 'BMW' OR auto.mark = 'LADA'
    > GROUP BY auto.mark, auto.color
    > ORDER BY auto.mark, auto.color
    
 9. > SELECT auto.mark, COUNT(*) FROM auto
    > GROUP BY auto.mark
    > HAVING COUNT(*) > 2
