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

### Inner Join (2)
Instead of writing the full table name, you can use table aliasing as a shortcut. For tables you also use AS to add the alias immediately after the table name with a space. Check out the aliasing of cities and countries below.

SELECT c1.name AS city, c2.name AS country
FROM cities AS c1
INNER JOIN countries AS c2
ON c1.country_code = c2.code;

Notice that to select a field in your query that appears in multiple tables, you'll need to identify which table/table alias you're referring to by using a . in your SELECT statement.

You'll now explore a way to get data from both the countries and economies tables to examine the inflation rate for both 2010 and 2015.

Join the tables countries (left) and economies (right). What field do you need to use in ON to match the two tables?
Alias countries AS c and economies AS e.
From this join, SELECT:
    c.code, aliased as country_code.
    name, year, and inflation_rate, not aliased.

``` sql
SELECT c.code AS country_code, name, year, inflation_rate
FROM countries AS c
INNER JOIN economies AS e
ON c.code = e.code;
```
### Inner join (3)

The ability to combine multiple joins in a single query is a powerful feature of SQL, e.g:

SELECT *
FROM left_table
INNER JOIN right_table
ON left_table.id = right_table.id
INNER JOIN another_table
ON left_table.id = another_table.id;

As you can see here it becomes tedious to continually write long table names in joins. This is when it becomes useful to alias each table using the first letter of its name (e.g. countries AS c)! It is standard practice to alias in this way and, if you choose to alias tables or are asked to specifically for an exercise in this course, you should follow this protocol.

Now, for each country, you want to get the country name, its region, and the fertility rate and unemployment rate for both 2010 and 2015.

Step1

Inner join countries (left) and populations (right) on the code and country_code fields respectively.
Alias countries AS c and populations AS p.
Select code, name, and region from countries and also select year and fertility_rate from populations (5 fields in total).

``` sql
SELECT c.code, c.name,c.region, p.year, p.fertility_rate FROM countries AS c
INNER JOIN populations AS p
ON
c.code = p.country_code
```
Step2

Add an additional inner join with economies to your previous query by joining on code.
Include the unemployment_rate column that became available through joining with economies.
Note that year appears in both populations and economies, so you have to explicitly use e.year instead of year as you did before.

``` sql
SELECT c.code, name, region, e.year, fertility_rate, unemployment_rate
FROM countries AS c
INNER JOIN populations AS p
ON c.code = p.country_code
INNER JOIN economies AS e
ON c.code = e.code;
```
Step 3

    Scroll down the query result and take a look at the results for Albania from your previous query. Does something seem off to you?
    The trouble with doing your last join on c.code = e.code and not also including year is that e.g. the 2010 value for fertility_rate is also paired with the 2015 value for unemployment_rate.
    Fix your previous query: in your last ON clause, use AND to add an additional joining condition. In addition to joining on code in c and e, also join on year in e and p.

``` sql
SELECT c.code, name, region, e.year, fertility_rate, unemployment_rate
FROM countries AS c
INNER JOIN populations AS p
ON c.code = p.country_code
INNER JOIN economies AS e
ON c.code = e.code AND p.year = e.year;
```

### USING - Review inner join using on 
When joining tables with a common field name, e.g.

SELECT *
FROM countries
INNER JOIN economies
ON countries.code = economies.code

You can use USING as a shortcut:

SELECT *
FROM countries
INNER JOIN economies
USING(code)

You'll now explore how this can be done with the countries and languages tables
``` sql
SELECT c.name AS country, continent, l.name AS language, official
FROM countries AS c
INNER JOIN languages AS l
USING(code);
```
### SELF JOIN 
Self-join

In this exercise, you'll use the populations table to perform a self-join to calculate the percentage increase in population from 2010 to 2015 for each country code!

Since you'll be joining the populations table to itself, you can alias populations as p1 and also populations as p2. This is good practice whenever you are aliasing and your tables have the same first letter. Note that you are required to alias the tables with self-joins.
###### Step 1
Join populations with itself ON country_code.
Select the country_code from p1.
Select the size field from both p1 and p2. SQL won't allow same-named fields, so alias p1.size as size2010 and p2.size as size2015.
``` sql
SELECT p1.country_code, 
       p1.size AS size2010,
       p2.size AS size2015
FROM populations AS p1
INNER JOIN populations AS p2
ON  p1.country_code = p2.country_code;
```
###### Step 2
Notice from the result that for each country_code you have four entries laying out all combinations of 2010 and 2015.

Extend the ON in your query to include only those records where the p1.year (2010) matches with p2.year - 5 (2015 - 5 = 2010).

This will omit the three entries per country_code that you aren't interested in.

``` sql
SELECT p1.country_code,
       p1.size AS size2010,
       p2.size AS size2015
FROM populations AS p1
INNER JOIN populations AS p2
ON p1.country_code = p2.country_code
    AND p1.year = p2.year - 5;
```
###### Step 3
As you just saw, you can also use SQL to calculate values like p2.year - 5 for you. With two fields like size2010 and size2015, you may want to determine the percentage increase from one field to the next:

With two numeric fields A
and B, the percentage growth from A to B can be calculated as (B−A)/A∗100.0

.

To SELECT add a new field aliased as growth_perc that calculates the percentage population growth from 2010 to 2015 for each country, using p2.size and p1.size.

```sql
SELECT p1.country_code,
       p1.size AS size2010,
       p2.size AS size2015,
       ((p2.size - p1.size)/p1.size) * 100.0 AS growth_perc
FROM populations AS p1
INNER JOIN populations AS p2
ON p1.country_code = p2.country_code
    AND p1.year = p2.year - 5;

```

### Case when and then

Often it's useful to look at a numerical field not as raw data, but instead as being in different categories or groups.

You can use CASE with WHEN, THEN, ELSE, and END to define a new grouping field.

Using the countries table, create a new field AS geosize_group that groups the countries into three groups:

    If surface_area is greater than 2 million, geosize_group is 'large'.
    If surface_area is greater than 350 thousand but not larger than 2 million, geosize_group is 'medium'.
    Otherwise, geosize_group is 'small'
    
``` sql 
SELECT name, continent, code, surface_area,
        -- first case
    CASE WHEN surface_area > 2000000 THEN 'large'
        -- second case
        WHEN surface_area > 350000 THEN 'medium'
        -- else clause + end
        ELSE 'small' END
        AS geosize_group
FROM countries;

```

### Inner challenge
The table you created with the added geosize_group field has been loaded for you here with the name countries_plus. Observe the use of (and the placement of) the INTO command to create this countries_plus table:

SELECT name, continent, code, surface_area,
    CASE WHEN surface_area > 2000000
            THEN 'large'
       WHEN surface_area > 350000
            THEN 'medium'
       ELSE 'small' END
       AS geosize_group
INTO countries_plus
FROM countries;

You will now explore the relationship between the size of a country in terms of surface area and in terms of population using grouping fields created with CASE.

By the end of this exercise, you'll be writing two queries back-to-back in a single script. You got this!

step 1
Using the populations table focused only for the year 2015, create a new field AS popsize_group to organize population size into

    'large' (> 50 million),
    'medium' (> 1 million), and
    'small' groups.

Select only the country code, population size, and this new popsize_group as fields

``` sql
SELECT country_code, size,
    CASE WHEN size > 50000000 THEN 'large'
        WHEN size > 1000000 THEN 'medium'
        ELSE 'small' END
        AS popsize_group
FROM populations
WHERE year = 2015;
```

Step 2


    Use INTO to save the result of the previous query as pop_plus. You can see an example of this in the countries_plus code in the assignment text. Make sure to include a ; at the end of your WHERE clause!

    Then, include another query below your first query to display all the records in pop_plus using SELECT * FROM pop_plus; so that you generate results and this will display pop_plus in query result.

``` sql
SELECT country_code, size,
    CASE WHEN size > 50000000 THEN 'large'
        WHEN size > 1000000 THEN 'medium'
        ELSE 'small' END
        AS popsize_group
INTO pop_plus
FROM populations
WHERE year = 2015;

SELECT * FROM pop_plus;
 ```
 
 step 3
 

    Use INTO to save the result of the previous query as pop_plus. You can see an example of this in the countries_plus code in the assignment text. Make sure to include a ; at the end of your WHERE clause!

    Then, include another query below your first query to display all the records in pop_plus using SELECT * FROM pop_plus; so that you generate results and this will display pop_plus in query result.

``` sql

SELECT country_code, size,
    CASE WHEN size > 50000000 THEN 'large'
        WHEN size > 1000000 THEN 'medium'
        ELSE 'small' END
        AS popsize_group
INTO pop_plus
FROM populations
WHERE year = 2015;


SELECT name, continent, geosize_group, popsize_group
FROM countries_plus AS c
INNER JOIN pop_plus AS p
ON p.country_code = c.code
ORDER BY geosize_group;
```
### LEFT and RIGHT JOINs
#### Left Join

Now you'll explore the differences between performing an inner join and a left join using the cities and countries tables.

You'll begin by performing an inner join with the cities table on the left and the countries table on the right. Remember to alias the name of the city field as city and the name of the country field as country.

You will then change the query to a left join. Take note of how many records are in each query here!

step 1
Fill in the code shown to complete the inner join. Note how many records are in the result of the join in the query result tab.
``` sql
-- get the city name (and alias it), the country code,
-- the country name (and alias it), the region,
-- and the city proper population
SELECT c1.name AS city, code, c2.name AS country,
       region, city_proper_pop
-- specify left table
FROM cities AS c1
-- specify right table and type of join
INNER JOIN countries AS c2
-- how should the tables be matched?
ON c1.country_code = c2.code
-- sort based on descending country code
ORDER BY code DESC;
```
step2
Change the code to perform a LEFT JOIN instead of an INNER JOIN. After executing this query, note how many records the query result contains.
``` sql
-- get the city name (and alias it), the country code,
-- the country name (and alias it), the region,
-- and the city proper population
SELECT c1.name AS city, code, c2.name AS country,
       region, city_proper_pop
-- specify left table
FROM cities AS c1
-- specify right table and type of join
LEFT JOIN countries AS c2
-- how should the tables be matched?
ON c1.country_code = c2.code
-- sort based on descending country code
ORDER BY code DESC;
```
Notice that the INNER JOIN version resulted in 230 records. The LEFT JOIN version returned 236 rows.

### Left Join (2)
Next, you'll try out another example comparing an inner join to its corresponding left join. Before you begin though, take note of how many records are in both the countries and languages tables below.

You will begin with an inner join on the countries table on the left with the languages table on the right. Then you'll change the code to a left join in the next bullet.

Note the use of multi-line comments here using /* and */.
``` sql
/*
select country name AS country, the country's local name,
the language name AS language, and
the percent of the language spoken in the country
*/
SELECT c.name AS country, local_name, l.name AS language, percent
-- countries on the left (alias as c)
FROM countries AS c
-- appropriate join with languages (as l) on the right
INNER JOIN languages AS l
-- give fields to match on
ON c.code = l.code
-- sort by descending country name
ORDER BY country DESC;

```
Perform a left join instead of an inner join. Observe the result, and also note the change in the number of records in the result. Carefully review which records appear in the left join result, but not in the inner join result.

``` sql
/*
select country name AS country, the country's local name,
the language name AS language, and
the percent of the language spoken in the country
*/
SELECT c.name AS country, local_name, l.name AS language, percent
-- countries on the left (alias as c)
FROM countries AS c
-- appropriate join with languages (as l) on the right
lEFT JOIN languages AS l
-- give fields to match on
ON c.code = l.code
-- sort by descending country name
ORDER BY country DESC;
```

Notice that the INNER JOIN version resulted in 914 records. The LEFT JOIN version returned 921 rows.

### Left join (3)

You'll now revisit the use of the AVG() function introduced in our Intro to SQL for Data Science course. You will use it in combination with left join to determine the average gross domestic product (GDP) per capita by region in 2010.

step 1

    Begin with a left join with the countries table on the left and the economies table on the right.
    Focus only on records with 2010 as the year.

``` sql
-- select name, region, and gdp_percapita
SELECT name, region, gdp_percapita
-- from countries (alias c) on the left
FROM countries AS c
-- left join with economies (alias e)
LEFT JOIN economies AS e
-- match on code fields
ON c.code = e.code
-- focus on 2010 entries
WHERE year = 2010;
```
step 2
Modify your code to calculate the average GDP per capita AS avg_gdp for each region in 2010. Select the region and avg_gdp fields.
``` sql
-- select name, region, and gdp_percapita
SELECT c.region, AVG(e.gdp_percapita) AS avg_gdp
-- from countries (alias c) on the left
FROM countries AS c
-- left join with economies (alias e)
LEFT JOIN economies AS e
-- match on code fields
ON c.code = e.code
-- focus on 2010 entries
WHERE year = 2010

GROUP By c.region
```
step 3
Arrange this data on average GDP per capita for each region in 2010 from highest to lowest average GDP per capita.
``` sql
-- Select region, average gdp_percapita (alias avg_gdp)
SELECT region, AVG(gdp_percapita) AS avg_gdp
-- From countries (alias c) on the left
FROM countries AS c
-- Join with economies (alias e)
LEFT JOIN economies AS e
-- Match on code fields
ON e.code = c.code
-- Focus on 2010 
WHERE year = 2010
-- Group by region
GROUP BY region
-- Order by avg_gdp, descending
ORDER BY avg_gdp DESC;
```

Well done. Notice how gradually you're adding more and more building blocks to your SQL vocabulary. This enables you to answer questions of ever-increasing complexity!

### Right join

Right joins aren't as common as left joins. One reason why is that you can always write a right join as a left join.
The left join code is commented out here. Your task is to write a new query using rights joins that produces the same result as what the query using left joins produces. Keep this left joins code commented as you write your own query just below it using right joins to solve the problem.

Note the order of the joins matters in your conversion to using right joins!


``` sql
-- convert this code to use RIGHT JOINs instead of LEFT JOINs
/*
SELECT cities.name AS city, urbanarea_pop, countries.name AS country,
       indep_year, languages.name AS language, percent
FROM cities
LEFT JOIN countries
ON cities.country_code = countries.code
LEFT JOIN languages
ON countries.code = languages.code
ORDER BY city, language;
*/

SELECT cities.name AS city, urbanarea_pop, countries.name AS country,
       indep_year, languages.name AS language, percent
FROM languages
RIGHT JOIN countries
ON languages.code = countries.code
RIGHT JOIN cities
ON cities.country_code = countries.code
ORDER BY city, language;

```

### FULL JOIN

