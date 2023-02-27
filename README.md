# dualbot-sql

## Создание таблиц

    CREATE TABLE Regions (
    id serial PRIMARY KEY,
    Name varchar);
    
    CREATE TABLE Locations
    (
        id        SERIAL PRIMARY KEY,
        Address   VARCHAR,
        Region_id INT REFERENCES regions(id)
    ); 
  
    CREATE TABLE Employees 
    (
        id     SERIAL PRIMARY KEY,
        Name   VARCHAR,
        Last_name  VARCHAR,
        Hire_date date,
        Salary int,
        Email  VARCHAR, 
        Manager_id INT REFERENCES Employees(id)	
    ); 
  

    CREATE TABLE Departments 
    (
        id     SERIAL PRIMARY KEY,
        Name   VARCHAR,
        Location_id INT REFERENCES Regions(id),
        Manager_id INT REFERENCES Employees(id)
    ); 
    
    ALTER TABLE Employees ADD COLUMN Department_id INT REFERENCES Departments(id);

 
## Получение данных


### Показать работников у которых нет почты или почта не в корпоративном домене (домен dualbootpartners.com)

    SELECT name
    FROM   employees
    WHERE  email IS NULL
            OR NOT email LIKE '%dualbootpartners.com' 

### Получить список работников нанятых в последние 30 дней

    SELECT name FROM employees
    WHERE CURRENT_DATE - hire_date < 30

### Найти максимальную и минимальную зарплату по каждому департаменту

    SELECT departments.name,
        max(salary),
        min(salary)
    FROM employees
    LEFT JOIN departments ON departments.id = department_id
    GROUP BY departments.name

### Посчитать количество работников в каждом регионе

    SELECT count(employees.id) AS employees_count,
        regions.name AS region
    FROM employees
    LEFT JOIN departments ON departments.id = department_id
    LEFT JOIN locations ON locations.id = location_id
    LEFT JOIN regions ON regions.id = region_id
    GROUP BY regions.name

### Показать сотрудников у которых фамилия длиннее 10 символов

    SELECT employees.name,
        employees.last_name
    FROM   employees
    WHERE  Char_length(employees.last_name) > 10 

### Показать сотрудников с зарплатой выше средней по всей компании

SELECT employees.NAME,
       employees.salary
FROM   employees
WHERE  employees.salary > (SELECT Avg(salary)
                           FROM   employees) 
