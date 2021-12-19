### SQL Queries

### 1. Sales Information (Customers, Sales, Avg Sales) by Country

``` sql
WITH Others AS
  (SELECT C.Country country,
          COUNT(*) total_customers
   FROM Customer C
   GROUP BY 1
   HAVING total_customers = 1),
     countries AS
  (SELECT CASE
              WHEN C.Country IN
                     (SELECT country
                      FROM Others) THEN "Other"
              ELSE C.Country
          END AS country,
          I.*
   FROM Customer C
   JOIN Invoice I ON I.CustomerId = C.CustomerId)
SELECT country,
       COUNT(DISTINCT CustomerId) AS total_customers,
       SUM(Total) AS total_sales,
       SUM(Total) / COUNT(DISTINCT CustomerId) AS avg_sales
FROM countries
GROUP BY 1
ORDER BY 2 DESC,
         3 DESC
```

<hr>

### 2. Top 10 Music Genres

``` sql
SELECT G.Name,
       SUM(IL.Quantity * IL.UnitPrice) AS Total_Sales
FROM Track T
JOIN InvoiceLine IL ON IL.TrackId = T.TrackId
JOIN Genre G ON G.GenreId = T.GenreId
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10
```

<hr>

### 3. Employee Performance

``` sql
SELECT E.EmployeeId,
       E.FirstName,
       E.LastName,
       SUM(I.Total) AS total_sales
FROM Employee E
JOIN Customer C ON E.EmployeeId = C.SupportRepId
JOIN Invoice I ON I.CustomerId = C.CustomerId
GROUP BY 1
```

<hr>

### 4. Is there a correlation between song length and sales / likeability?

``` sql
SELECT T.TrackId,
       T.Name,
       (T.Milliseconds / 60000) AS LENGTH,
       SUM(IL.UnitPrice * IL.Quantity) AS Total_Sales
FROM Track T
JOIN InvoiceLine IL ON IL.TrackId = T.TrackId
JOIN Invoice I ON I.InvoiceId = IL.InvoiceId
GROUP BY 1,
         2,
         3
ORDER BY Total_Sales DESC
```
