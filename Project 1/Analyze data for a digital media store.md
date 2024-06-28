# 🎹 Analyze data for a digital media store

## 📌 1 Overview

This project will explore multiple questions with regard to the database Chinook.db, which represents a digital media store. The database was retrieved from [Kaggle](https://www.kaggle.com/datasets/marwandiab/chinookdatabase). 

I explored the data in this database by asking questions related to the company's sales or HR in order to showcast my SQL skills. 


## 📐 2. Entity Relation Diagram for the Database - chinook.db

Following there is ERD for the database used for this project. It is composed of 11 tables and represents a digital media store, including tables for artists, albums, media tracks, invoices, and customers.


![image](https://github.com/alexalra/SQL-Practice/assets/78654579/e7bc5c38-3576-4f64-9bba-0c856afa5220)

## 💵 3. Sales Questions

The store manager is interested in knowing more about the store's sales, and see what and who are generating more profits for the company. 

1. How much money was generated in sales in total?

<details>

  <summary>Answer</summary>
  

```
2,328.6 USD was generated
```
Code

```sql

SELECT 
  ROUND(SUM(UnitPrice * Quantity), 2) AS SALES
FROM 
  InvoiceLine; 

```
</details>

   
2. Which genre generated most sales and how much?

<details>

  <summary>Answer</summary>
  

```
Rock with 826.65 USD in sales.
```
Code

```sql
SELECT 
  G.Name, 
  ROUND(SUM(I.UnitPrice * I.Quantity),2) AS SALES
FROM 
  InvoiceLine as I
JOIN
  Track as T 
ON 
  I.TrackId = T.TrackId
JOIN 
  Genre AS G
ON 
  T.GenreId = G.GenreId
GROUP BY 
  1
ORDER BY 
  2 DESC
LIMIT 
  1;

```
</details>
   
3. How many genres generated less than 50 USD in sales?

<details>

  <summary>Answer</summary>
  


16 genres


Code

```sql
WITH T1 AS 
  (SELECT 
    SUM(I.UnitPrice * I.Quantity) AS FUNDS, 
    G.Name AS NAME
  FROM 
    InvoiceLine as I
  JOIN 
    Track as T 
  ON 
    I.TrackId = T.TrackId
  JOIN 
    Genre AS G
  ON 
    T.GenreId = G.GenreId
  GROUP BY
    2), 

T2 AS 

  (SELECT 
    FUNDS, 
    NAME
  FROM 
    T1
    WHERE 
    FUNDS < 50
  GROUP BY 
    2
  ORDER BY 
    1 DESC)

SELECT 
  COUNT(T2.FUNDS) AS COUNT
FROM 
  T2
JOIN 
  T1
ON 
  T1.NAME = T2.NAME

```
</details>
  
   
4. Who is the employe who generated more sales?

<details>

  <summary>Answer</summary>
  

```
Jane Peacock with 833.04 USD in sales
```

Code

```sql
SELECT 
  SUM(I.total) TOTAL_SALES, 
  E.EmployeeId, 
  E.FirstName, 
  E.LastName
FROM 
  Invoice AS I
JOIN 
  Customer as C
ON 
  C.CustomerId = I.CustomerId
JOIN 
  Employee as E
ON 
  C.SupportRepId = E.EmployeeId
GROUP BY 
  E.EmployeeId
ORDER 
  BY TOTAL_SALES DESC
LIMIT
  1;
```
</details>
   
5. What is the average duration of the tracks sold by the employee with higher sales?

<details>

  <summary>Answer</summary>

```
386867.45 is the average duration of the tracks sold by Jane Peacock

```

Code

To answer this question I created 2 subquerries.
The first subquerry (TABLE_1) returned the employee with higher sales.
The second subquerry (TABLE_2) returned the average duration of the tracks sold by every employee.


```sql
WITH TABLE_1 AS 

  (SELECT 
    E.FirstName NAME, 
    E.LastName LAST_NAME,
    SUM(I.total) TOTAL_SALES, 
    E.EmployeeId EMPLOYEE_ID 
  FROM 
    Invoice AS I
  JOIN 
    Customer as C
  ON 
    C.CustomerId = I.CustomerId
  JOIN 
    Employee as E
  ON 
    C.SupportRepId = E.EmployeeId
  GROUP BY 
    4
  ORDER 
    BY 3 DESC
  LIMIT 
    1),

TABLE_2 AS 

  (SELECT 
    AVG(T.Milliseconds) AVERAGE_DURATION, 
    E.FirstName NAME,
    E.LastName LAST_NAME
  FROM 
    TRACK as T
  JOIN 
    InvoiceLine as IL 
  ON 
    T.TrackId = IL.TrackId
  JOIN 
    Invoice as I
  ON 
    IL.InvoiceId = I.InvoiceId
  JOIN 
    Customer as C
  ON 
    C.CustomerId = I.CustomerId
  JOIN 
    Employee as E
  ON 
    C.SupportRepId = E.EmployeeId
  GROUP BY 
    2)

SELECT  
  TABLE_1.NAME, 
  TABLE_1.LAST_NAME,
  TABLE_2.AVERAGE_DURATION
FROM 
  TABLE_1
JOIN 
  TABLE_2
ON 
  TABLE_1.NAME = TABLE_2.NAME AND  
  TABLE_1.LAST_NAME =  TABLE_2.LAST_NAME
```
</details>
