# Music_database_SQL

This repository contains a portfolio of SQL database scripts, including schema definition, sample data, and example queries. <br>

I have analyzed the data from the music database and answered 9 sample queries in this analysis. <br>

Sample queries: <br> 

Q.1 Who is the senior most employee based on job title? <br>
**Description**: This query returns the senior-most employee based on job title. <br>
**SQL**:
```sql
Select * From employee 
Order By levels Desc Limit 1  
```

Q.2 Which country has the most invoices? <br>
**Description**: This query returns the country having the most invoices. <br>
**SQL**:
```sql

Select Count(*) as c, billing_country 
From invoice 
Group By billing_country 
Order By c Desc
```


Q.3 What are the top 3 values of total invoices? <br>
**Description**: This query retrieves the top 3 values of total invoices. <br>
**SQL**:
```sql

Select total From invoices 
Orders By total Desc 
Limit 3
```

Q.4 Which city has the best customers? We would like to throw a promotional Music Festival in the city where we made the most money. Write a query that returns one city that has the highest sum of invoice totals. Return both the city name & sum of all invoice totals. <br>
**Description**: The query you provided aims to identify the city with the highest total sum of invoice amounts. <br>
**SQL**:
```sql

Select Sum(total) As invoice_total, billing_city 
From invoice
Group By billing_city 
Order By invoice_total Desc
```

Q.5 Who is the best customer? The customer who has spent the most money will be declared the best customer. <br>
**Description**: This query retrieves the data of the customer who has spent the most money. <br>
**SQL**:
```sql

Select customer.customer_id, customer.first_name, customer.last_name, 
Sum(invoice.total) as total 
From customer
Join invoice on customer.customer_id = invoice.customer_id 
Group By customer.customer_id 
Order By total Desc 
Limit 1 
```
Q.6 Write a query to return the email, first name, last name, & Genre of all Rock Music listeners. Return your list ordered alphabetically by email starting with A. <br>
**Description**: This will retrieve the first name, last name, & Genre of all Rock music listeners list ordered alphabetically by email starting with A. <br>
**SQL**
```sql

Select Distinct email,first_name, last_name
From customer
Join invoice On customer.customer_id = invoice.customer_id
Join invoice_line On invoice.invoice_id = invoice_line.invoice_id
Where track_id In(
  Select track_id From track
  Join genre On track.genre_id = genre.genre_id
  Where genre.name Like 'Rock'
)
Order By email;
```
Q.7 Let's invite the artists who have written the most rock music in our dataset. Write that returns the Artist name and total track count of the top 10 rock bands. <br>
**Description**: This will retrieve the Artist name and total crack count of the top 10 rock bands. <br>
**SQL**
```sql

Select artist.artist_id, artist.name,Count(artist.artist_id) As number_of_songs
From track
Join album On album.album_id = track.album_id
Join artist On artist.artist_id = album.artist_id
Join genre On genre.genre_id = track.genre_id
Where genre.name Like 'Rock'
Group By artist.artist_id
Order By number_of_songs Desc
Limit 10;
```
Q.8 Return all the track names that have a song length longer than the average song length. Return the names and milliseconds for each track. Order by the song length with the longest songs listed first. <br>
**Description*: This query will return the names and lengths (in milliseconds) of tracks that have a duration longer than the average duration of all songs in the dataset. The list will be ordered by song length, with the longest songs listed first. <br>
**SQL**
```sql

Select name,milliseconds
From track
Where milliseconds > (
  Select Avg(milliseconds) As avg_track_length
  From track)
Order By milliseconds Desc;
```




