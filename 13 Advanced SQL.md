Throughout this course, you'll be working with the countries database containing information about the most populous world 
cities as well as country-level economic data, population data, and geographic data. This countries database also contains 
information on languages spoken in each country.
You can see the different tables in this database by clicking on the tabs on the bottom right below query.sql. Click through 
them to get a sense for the types of data that each table contains before you continue with the course! Take note of the fields 
that appear to be shared across the tables.

Recall from the video the basic syntax for an INNER JOIN, here including all columns in both tables:

SELECT *
FROM left_table
INNER JOIN right_table
ON left_table.id = right_table.id;

You'll start off with a SELECT statement and then build up to an inner join with the cities and countries tables.
Let's get to it!

### Inner join
- Modify the SELECT statement to keep only the name of the city, the name of the country, and the name of the region the 
country resides in.

``` sql
SELECT c.name as city, ct.name as country, ct.region  FROM  cities c
INNER JOIN countries ct
ON c.country_code = ct.code
```
