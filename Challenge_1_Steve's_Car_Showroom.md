# Challenge 1 - Steve's Car Showroom

## Introduction
Explore the 'Top-End Car Showroom Data Challenge,' where we step into Steve's shoes to analyze data for his high-end car showroom. Join me in unraveling insights to help Steve make informed decisions.

## Tables Creation
```sql
CREATE TABLE cars (
car_id INT PRIMARY KEY,
make VARCHAR(50),
type VARCHAR(50),
style VARCHAR(50),
cost_$ INT
);
--------------------
INSERT INTO cars (car_id, make, type, style, cost_$)
VALUES (1, 'Honda', 'Civic', 'Sedan', 30000),
(2, 'Toyota', 'Corolla', 'Hatchback', 25000),
(3, 'Ford', 'Explorer', 'SUV', 40000),
(4, 'Chevrolet', 'Camaro', 'Coupe', 36000),
(5, 'BMW', 'X5', 'SUV', 55000),
(6, 'Audi', 'A4', 'Sedan', 48000),
(7, 'Mercedes', 'C-Class', 'Coupe', 60000),
(8, 'Nissan', 'Altima', 'Sedan', 26000);
--------------------
CREATE TABLE salespersons (
salesman_id INT PRIMARY KEY,
name VARCHAR(50),
age INT,
city VARCHAR(50)
);
--------------------
INSERT INTO salespersons (salesman_id, name, age, city)
VALUES (1, 'John Smith', 28, 'New York'),
(2, 'Emily Wong', 35, 'San Fran'),
(3, 'Tom Lee', 42, 'Seattle'),
(4, 'Lucy Chen', 31, 'LA');
--------------------
CREATE TABLE sales (
sale_id INT PRIMARY KEY,
car_id INT,
salesman_id INT,
purchase_date DATE,
FOREIGN KEY (car_id) REFERENCES cars(car_id),
FOREIGN KEY (salesman_id) REFERENCES salespersons(salesman_id)
);
--------------------
INSERT INTO sales (sale_id, car_id, salesman_id, purchase_date)
VALUES (1, 1, 1, '2021-01-01'),
(2, 3, 3, '2021-02-03'),
(3, 2, 2, '2021-02-10'),
(4, 5, 4, '2021-03-01'),
(5, 8, 1, '2021-04-02'),
(6, 2, 1, '2021-05-05'),
(7, 4, 2, '2021-06-07'),
(8, 5, 3, '2021-07-09'),
(9, 2, 4, '2022-01-01'),
(10, 1, 3, '2022-02-03'),
(11, 8, 2, '2022-02-10'),
(12, 7, 2, '2022-03-01'),
(13, 5, 3, '2022-04-02'),
(14, 3, 1, '2022-05-05'),
(15, 5, 4, '2022-06-07'),
(16, 1, 2, '2022-07-09'),
(17, 2, 3, '2023-01-01'),
(18, 6, 3, '2023-02-03'),
(19, 7, 1, '2023-02-10'),
(20, 4, 4, '2023-03-01');
```

## Business Queries

### 1. What are the details of all cars purchased in the year 2022?
```sql
SELECT * 
FROM cars c
LEFT JOIN sales s
ON c.car_id = s.car_id
WHERE YEAR(purchase_date) = 2022;
```
### 2. What is the total number of cars sold by each salesperson?
```sql
SELECT p.salesman_id salesman_id,
       p.name Salesperson,
       COUNT(s.car_id) Cars_Sold
FROM salespersons p
LEFT JOIN sales s
ON p.salesman_id = s.salesman_id
GROUP BY 1,2
ORDER BY 3 DESC;
```
### 3. What is the total revenue generated by each salesperson?
```sql
SELECT p.salesman_id salesman_id,
       p.name Salesperson,
       COUNT(s.car_id) Cars_Sold,
       SUM(cost_$) Revenue_gen
FROM salespersons p
LEFT JOIN sales s 
ON p.salesman_id = s.salesman_id
LEFT JOIN cars c 
ON s.car_id = c.car_id
GROUP BY 1,2
ORDER BY 4 DESC;
```
### 4. What are the details of the cars sold by each salesperson?
```sql
SELECT p.name Salesperson,
       c.car_id Car_id,
       c.make Make,
       c.type Type,
       c.style Style,
       c.cost_$ cost_$
FROM salespersons p 
LEFT JOIN sales s 
on p.salesman_id = s.salesman_id
LEFT JOIN cars c 
ON s.car_id = c.car_id;
```
### 5. What is the total revenue generated by each car type?
```sql
SELECT c.type Type, 
       SUM(c.cost_$) Total_Revenue
FROM cars c
JOIN sales s
ON c.car_id = s.car_id
GROUP BY 1
ORDER BY 2 DESC;
```
### 6. What are the details of the cars sold in the year 2021 by salesperson 'Emily Wong'?
```sql
SELECT c.*
FROM salespersons p 
JOIN sales s 
on p.salesman_id = s.salesman_id
JOIN cars c 
ON s.car_id = c.car_id
WHERE YEAR(purchase_date) = 2021 AND p.name = 'Emily Wong';
```
### 7. What is the total revenue generated by the sales of hatchback cars?
```sql
SELECT SUM(c.cost_$) Total_Revenue
FROM cars c
JOIN sales s
ON c.car_id = s.car_id
WHERE style = 'Hatchback';
```
### 8. What is the total revenue generated by the sales of SUV cars in the year 2022?
```sql
SELECT  SUM(c.cost_$) Total_Revenue
FROM cars c
JOIN sales s
ON c.car_id = s.car_id
WHERE style = 'SUV' AND YEAR(purchase_date) = 2022;
```
### 9. What is the name and city of the salesperson who sold the most number of cars in the year 2023?
```sql
SELECT p.name AS Salesperson,
       p.city AS City,
       COUNT(s.sale_id) AS No_of_Cars_sold
FROM salespersons p
JOIN sales s ON p.salesman_id = s.salesman_id AND YEAR(s.purchase_date) = 2023
GROUP BY p.name,p.city 
ORDER BY No_of_Cars_sold DESC
LIMIT 1;
```
### 10. What is the name and age of the salesperson who generated the highest revenue in the year 2022?
```sql
SELECT p.name AS Salesperson,
	   p.age AS Age,
       SUM(c.cost_$) AS Revenue_gen
FROM salespersons p 
JOIN sales s 
ON p.salesman_id = s.salesman_id
JOIN cars c 
ON s.car_id = c.car_id
WHERE YEAR(purchase_date) = 2022
GROUP BY p.name, p.age
ORDER BY SUM(c.cost_$) DESC
LIMIT 1;
```
