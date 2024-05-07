# Music Database SQL

Welcome to the Music Database SQL repository! This repository is a comprehensive collection of SQL database scripts tailored for music industry analysis. It includes schema definitions, sample datasets, and a variety of example queries. These scripts have been meticulously crafted to explore various facets of the music database, ranging from customer spending behavior to genre popularity across different regions. 

## Sample Queries

Below are 11 example queries along with their descriptions and corresponding SQL scripts:



### 1. Senior Most Employee
**Description:** Returns the senior-most employee based on job title. <br>
**SQL**:
```sql
Select * From employee 
Order By levels Desc Limit 1  
```

### 2. Most Invoices by Country
**Description**: Returns the country with the most invoices. <br>
**SQL**:
```sql

Select Count(*) as c, billing_country 
From invoice 
Group By billing_country 
Order By c Desc
```


### 3. Top 3 Values of Total Invoices
**Description**: Retrieves the top 3 values of total invoices. <br>
**SQL**:
```sql

Select total From invoices 
Orders By total Desc 
Limit 3
```

### 4. City with Best Customers
**Description**: Query to find the city with the highest sum of invoice totals.. <br>
**SQL**:
```sql

Select Sum(total) As invoice_total, billing_city 
From invoice
Group By billing_city 
Order By invoice_total Desc
```

### 5. Best Customer
**Description**: Query to retrieve data of the customer who has spent the most money. <br>
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
### 6. Rock Music Listeners
**Description**: Query to return the email, first name, last name, & genre of all Rock music listeners ordered alphabetically by email. <br>
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
### 7. Top Rock Bands
**Description**: Query to return the artist name and total track count of the top 10 rock bands. <br>
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
### 8. Longest Tracks
**Description*: Query to return all track names with a song length longer than the average song length, ordered by song length. <br>
**SQL**
```sql

Select name,milliseconds
From track
Where milliseconds > (
  Select Avg(milliseconds) As avg_track_length
  From track)
Order By milliseconds Desc;
```
### 9. Customer Spending on Artists
**Description**: Query to return the customer name, artist name, and total spent by each customer on artists. <br>
**SQL**
```sql

With best_selling_artist As (
  Select artist.artist_id As artist_id, artist.name As artist_name,
  Sum(invoice_line.unit_price*invoice_line.quantity) As total_sales
  From invoice_line
  Join track On track.track_id = invoice_line.track_id
  Join album On album.album_id = track.album_id
  Join artist On artist.artist_id = album.artist_id
  Group By 1, 2
  Order By 3 Desc
  Limit 1
)
Select c.customer_id, c.first_name, c.last_name, bsa.artist_name,
Sum(il.unit_price*il.quantity) As amount_spent
From invoice
Join customer c On c.customer_id = invoice.customer_id
Join invoice_line il On il.invoice_id = invoice.invoice_id
Join track t On t.track_id = il.track_id
Join album alb On alb.album_id = t.album_id
Join best_selling_artist bsa On bsa.artist_id = alb.artist_id
Group By 1, 2, 3, 4
Order By 5 Desc;
```
### 10. Popular Music Genre by Country
**Description**: Query to return each country along with the most popular music genre(s). <br>
**SQL**
```sql

With popular_genre As
(
  Select Count(invoice_line.quantity) As purchases, customer.country, genre.name, genre.genre_id,
  Row_Number() Over(Partition By customer.country Order By Count(invoice_line.quantity) Desc) As Rowno
  From invoice_line
  Join invoice On invoice.invoice_id = invoice_line.invoice_id
  Join customer On customer.customer_id = invoice.customer_id
  Join track On track.track_id = invoice_line.track_id
  Join genre On genre.genre_id = track.genre_id
  Group By 2,3,4
)
Select * From popular_genre Where RowNo = 1;
```
### 11. Top Spending Customers by Country
**Description**:Query to return the country along with the top customer(s) and their spending. <br>
**SQL**
```sql

With Recursive
 customer_with_country As (
  Select customer.customer_id,first_name, last_name,billing_country,Sum(total) As total_spending
  From invoice
  Join customer On customer.customer_id = invoice.customer_id
  Group By 1,2,3,4
  Order by 2,3 Desc),

country_max_spending As(
  Select billing_country,Max(total_spending) As max_spending
  From customer_with_country
  Group By billing_country)

Select cc.billing_country, cc.total_spending, cc.first_name, cc.last_name, cc.customer_id
From customer_with_country cc
Join country_max_spending ms
On cc.billing_country = ms.billing_country
Where cc.total_spending = ms.max_spending
Order by 1;
```


