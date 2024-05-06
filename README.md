# Music_database_SQL

This repository contains a portfolio of SQL database scripts, including schema definition, sample data, and example queries. <br>

I have analyzed the data from the music database and answered 9 sample queries in this analysis. <br>

Q.1 Who is the senior most employee based on job title? <br>
**Description**: This query returns the senior-most employee based on job title. <be>
**SQL**:
```sql
Select * From employee <br>
Order By levels Desc Limit 1  

Q.2 Which country has the most invoices? <br>
**Description**: This query returns the country having the most invoices. <br>
**SQL**:
```sql

Select Count(*) as c, billing_country <br>
From invoice <br>
Group By billing_country <br>
Order By c Desc 

Q.3 What are the top 3 values of total invoices? <br>
**Description**: This query retrieves the top 3 values of total invoices. <br>
**SQL**:
```sql

Select total From invoices <br>
Orders By total Desc <br>
Limit 3

Q.4 Which city has the best customers? We would like to throw a promotional Music Festival in the city where we made the most money. Write a query that returns one city that has the highest sum of invoice totals. Return both the city name & sum of all invoice totals. <br>
**Description**: The query you provided aims to identify the city with the highest total sum of invoice amounts. <br>
**SQL**:
```sql

Select Sum(total) As invoice_total, billing_city <br>
From invoice <br>
Group By billing_city <br>
Order By invoice_total Desc 

Q.5 Who is the best customer? The customer who has spent the most money will be declared the best customer. <br>
**Description**: This query retrieves the data of the customer who has spent the most money. <br>
**SQL**:
```sql

Select customer.customer_id, customer.first_name, customer.last_name, <br>
Sum(invoice.total) as total <br>
From customer <br>
Join invoice on customer.customer_id = invoice.customer_id <br>
Group By customer.customer_id <br>
Order By total Desc <br>
Limit 1 
