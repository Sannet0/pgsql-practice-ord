# Company

## Tables

**We have departments table**

* **id** - departments ID
* **name** - name of department

> CREATE TABLE departments (
> id serial primary key, 
> name varchar(100)
> );

**We have employees table**

* **id** - employees ID
* **name** - employee name
* **surname** - employee surname
* **payment** - employee payment
* **departments_id** - department ID in which employee work
* **chief_id** - employee chief ID

> CREATE TABLE employees (
> id serial primary key, 
> name varchar(80), 
> surname varchar(80), 
> payment int, 
> departments_id int references departments (id), 
> chief_id int
> );

## Data

> INSERT INTO employees ("name", "surname", "payment", "departments_id", "chief_id") 
> VALUES ('Demin', 'Victor', 40615, 1, NULL), 
> ('Shcherbakova', 'Pauline', 50862, 2, NULL), 
> ('Rozhkova', 'Valeria', 29562, 2, 2), 
> ('Nikolaeva', 'Julia', 58641, 3, NULL),
> ('Danilova', 'Alena', 25141, 3, 4),
> ('Uspensky', 'Leon', 26390, 3, 4),
> ('Khokhlov', 'Dmitry', 52674, 4, NULL),
> ('Ilyin', 'Dmitry', 29461, 4, 7),
> ('Kudryashova', 'Alicia', 26486, 4, 7),
> ('Litvinova', 'Olivia', 25952, 4, 7),
> ('Soloviev', 'Maria', 40749, 5, NULL),
> ('Volkov', 'Artyom', 23785, 5, 11),
> ('Abramova', 'Polina', 28032, 5, 11),
> ('Ostrovskaya', 'Karina', 23514, 5, 11),
> ('Morozov', 'Lion', 24581, 5, 11),
> ('Eliseev', 'Artem', 52615, 6, NULL),
> ('Ivanova', 'Sofya', 29732, 6, 16),
> ('Pavlova', 'Xenia', 29252, 6, 16),
> ('Alexandrova', 'Alexandra', 24191, 6, 16),
> ('Golikov', 'Maxim', 25790, 6, 16),
> ('Zakharov', 'David', 28182, 6, 16),
> ('Gracheva', 'Polina', 45898, 7, NULL),
> ('Saveliev', 'Alexander', 26203, 7, 22),
> ('Nikolaev', 'Emil', 25310, 7, 22),
> ('Anokhina', 'Victoria', 20342, 7, 22),
> ('Bondarev', 'Maxim', 21056, 7, 22),
> ('Malyshev', 'Maxim', 21258, 7, 22),
> ('Nazarova', 'Arina', 22270, 7, 22),
> ('Nazarov', 'Vladimir', 51218, 8, NULL),
> ('Golubeva', 'Varvara', 25097, 8, 29),
> ('Borisova', 'Elizaveta', 23483, 8, 29),
> ('Mikhailova', 'Sofia', 20864, 8, 29),
> ('Morozova', 'Taisiya', 26770, 8, 29),
> ('Shcheglov', 'Arsen', 23124, 8, 29),
> ('Kruglov', 'Yaroslav', 24857, 8, 29),
> ('Zakharova', 'Anna', 23492, 8, 29),
> ('Sokolov', 'Mark', 47721, 9, NULL),
> ('Popova', 'Diana', 29082, 9, 37),
> ('Tretyakov', 'Alexandra', 29882, 9, 37),
> ('Degtyareva', 'Sofia', 28679, 9, 37),
> ('Samoilov', 'Maxim', 21535, 9, 37),
> ('Morozov', 'Artem', 22445, 9, 37),
> ('Morozov', 'Semyon', 26822, 9, 37),
> ('Semina', 'Anna', 25184, 9, 37),
> ('Aleksandrov', 'Luka', 24153, 9, 37),
> ('Makarova', 'Emilia', 55106, 10, NULL),
> ('Makeeva', 'Hope', 24912, 10, 46),
> ('Romanov', 'Lev', 25811, 10, 46),
> ('Isaeva', 'Emilia', 28381, 10, 46),
> ('Paramonov', 'Sergey', 21224, 10, 46),
> ('Semenova', 'Polina', 28655, 10, 46),
> ('Volkova', 'Yesenia', 24912, 10, 46),
> ('Kozlov', 'Andrey', 25478, 10, 46),
> ('Nesterova', 'Miroslava', 27883, 10, 46),
> ('Kuznetsov', 'Demid', 23574, 10, 46);

> INSERT INTO departments ("name") 
> VALUES ('first department'), 
> ('second department'), 
> ('third department'), 
> ('fourth department'), 
> ('fifth department'), 
> ('sixth department'), 
> ('seventh department'), 
> ('eighth department'), 
> ('ninth department'), 
> ('tenth department');

## Tasks

1. Display a list of employees earning the highest salary in their department
2. Display a list of employees earning the highest salary in their department
3. Display a list of department IDs, the number of employees in which does not exceed 3 people
4. Display a list of employees who do not have an assigned manager working in the same department
5. Display a list of employees who do not have a manager in the department
6. Find a list of department IDs with the maximum total salary of employees
7. Compose an SQL query that calculates the product of real values contained in some column of the table
8. Product of all salaries in each department

## Answers

1. > SELECT employees.id, employees.name, employees.surname 
   > FROM employees 
   > JOIN (select * from employees) AS head ON head.id = employees.chief_id AND head.payment < employees.payment

2. > SELECT employees.id, employees.name, employees.surname, employees.payment 
   > FROM employees 
   > JOIN (select departments.name, max(employees.payment) as payment 
   > FROM departments 
   > JOIN employees ON employees.departments_id = departments.id GROUP BY departments.name) AS head ON  head.payment = employees.payment

3. > SELECT employees.departments_id, COUNT(employees.id)
   > FROM employees 
   > GROUP BY employees.departments_id 
   > HAVING COUNT(employees.id) <= 3

4. > SELECT *
   > FROM employees 
   > WHERE employees.chief_id is NULL

5. > SELECT employees.name, employees.surname 
   > FROM employees 
   > WHERE employees.chief_id is NULL and employees.departments_id = (select employees.departments_id 
   > FROM employees 
   > group by employees.departments_id 
   > having count(employees.id) <= 1)

6. > SELECT employees.departments_id
   > FROM employees
   > JOIN (select max(employees.payment) as payment
   > FROM departments
   > JOIN employees on employees.departments_id = departments.id) as head on  head.payment = employees.payment

7. > SELECT exp(sum(ln(employees.payment))) as payment
   > FROM employees

8. > SELECT departments.name, exp(sum(ln(employees.payment))) as payment
   > FROM departments
   > join employees on employees.departments_id = departments.id group by departments.name
