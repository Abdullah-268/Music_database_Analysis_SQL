# Music_database_SQL
I have analyzed the data from the music database
I have answered 9 questions in this analysis

Q.1 Who is the senior most employee based on job title?

Select * From employee <br>
Order By levels Desc Limit 1

Q.2 Which country has the most invoices?

Select Count(*) as c, billing_country <br>
From invoice <br>
Group By billing_country <br>
Order By c Desc 

Q.3 What are the top 3 values of total invoices?

Select total From invoices <br>
Orders By total Desc <br>
Limit 3

Q.4 Which city has the best customers? We would like to throw a promotional Music Festival in the city we made the most money. Write a query that returns one city that has the highest sum of invoice totals. Return both the city name & sum of all invoice totals.

Select Sum(total) As invoice_total, billing_city <br>
From invoice <br>
Group By billing_city <br>
Order By invoice_total Desc 

Q.5 Who is the best customer? The customer who has spent the most money wll be declared the best customer.

Select customer.customer_id, customer.first_name, customer.last_name, <br>
Sum(invoice.total) as total <br>
From customer <br>
Join invoice on customer.customer_id = invoice.customer_id <br>
Group By customer.customer_id <br>
Order By total Desc <br>
Limit 1 
