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
Full join

In this exercise, you'll examine how your results differ when using a full join versus using a left join versus using an inner join with the countries and currencies tables.

You will focus on the North American region and also where the name of the country is missing. Dig in to see what we mean!

Begin with a full join with countries on the left and currencies on the right. The fields of interest have been SELECTed for you throughout this exercise.

Then complete a similar left join and conclude with an inner join.

1 Choose records in which region corresponds to North America or is NULL.
2. Repeat the same query as above but use a LEFT JOIN instead of a FULL JOIN. Note what has changed compared to the FULL JOIN result!

``` sql
SELECT name AS country, code, region, basic_unit
FROM countries
FULL JOIN currencies
USING (code)
WHERE region = 'North America' OR region IS NULL
ORDER BY region;


SELECT name AS country, code, region, basic_unit
FROM countries
LEFT JOIN currencies
USING (code)
WHERE region = 'North America' OR region IS NULL
ORDER BY region;


SELECT name AS country, code, region, basic_unit
FROM countries
INNER JOIN currencies
USING (code)
WHERE region = 'North America' OR region IS NULL
ORDER BY region;

```
Have you kept an eye out on the different numbers of records these queries returned? The FULL JOIN query returned 17 rows, the OUTER JOIN returned 4 rows, and the INNER JOIN only returned 3 rows. Do these results make sense to you?


### Full join (2)

You'll now investigate a similar exercise to the last one, but this time focused on using a table with more records on the left than the right. You'll work with the languages and countries tables.

Begin with a full join with languages on the left and countries on the right. Appropriate fields have been selected for you again here.


    Choose records in which countries.name starts with the capital letter 'V' or is NULL.
    Arrange by countries.name in ascending order to more clearly see the results.

``` sql
SELECT countries.name, code, languages.name AS language
FROM languages
FULL JOIN countries
USING (code)
WHERE countries.name LIKE 'V%' OR countries.name IS NULL
ORDER BY countries.name;
```

Repeat the same query as above but use a left join instead of a full join. Note what has changed compared to the full join result!

``` sql
SELECT countries.name, code, languages.name AS language
FROM languages
LEFT JOIN countries
USING (code)
WHERE countries.name LIKE 'V%' OR countries.name IS NULL
ORDER BY countries.name;
```
Repeat once more, but use an inner join instead of a left join. Note what has changed compared to the full join and left join results.
``` sql
SELECT countries.name, code, languages.name AS language
FROM languages
INNER JOIN countries
USING (code)
WHERE countries.name LIKE 'V%' OR countries.name IS NULL
ORDER BY countries.name;
```

### Full Join (3)


    Complete a full join with countries on the left and languages on the right.
    Next, full join this result with currencies on the right.
    Select the fields corresponding to the country name AS country, region, language name AS language, and basic and fractional units of currency.
    Use LIKE to choose the Melanesia and Micronesia regions (Hint: 'M%esia').

``` sql
SELECT c.name AS country, region, l.name AS language,
       basic_unit, frac_unit
FROM countries AS c
FULL JOIN languages AS l
USING (code)
FULL JOIN currencies AS cr
USING (code)
WHERE region LIKE 'M%esia';
```

### Cross Join
This exercise looks to explore languages potentially and most frequently spoken in the cities of Hyderabad, India and Hyderabad, Pakistan.

You will begin with a cross join with cities AS c on the left and languages AS l on the right. Then you will modify the query using an inner join in the next tab


    Create the cross join above and select only the city name AS city and language name AS language. (Recall that cross joins do not use ON or USING.)
    Make use of LIKE and Hyder% to choose Hyderabad in both countries.

``` sql
SELECT c.name AS city, l.name AS language
FROM cities AS c        
CROSS JOIN languages AS l
WHERE c.name LIKE 'Hyder%';
```


    Create the cross join above and select only the city name AS city and language name AS language. (Recall that cross joins do not use ON or USING.)
    Make use of LIKE and Hyder% to choose Hyderabad in both countries.

``` sql
SELECT c.name AS city, l.name AS language
FROM cities AS c        
INNER JOIN languages AS l
ON c.country_code = l.code
WHERE c.name LIKE 'Hyder%';
```

### Outer Challenge
Now that you're fully equipped to use outer joins, try a challenge problem to test your knowledge!

In terms of life expectancy for 2010, determine the names of the lowest five countries and their regions.
``` sql
SELECT c.name as country, region, p.life_expectancy as life_exp
FROM countries as c
LEFT JOIN populations p
ON c.code = p.country_code
WHERE p.year =2010
ORDER BY life_exp
LIMIT 5

```

### Union

Near query result to the right, you will see two new tables with names economies2010 and economies2015

Combine these two tables into one table containing all of the fields in economies2010. The economies table is also included for reference.
Sort this resulting single table by country code and then by year, both in ascending order

``` sql
-- pick specified columns from 2010 table
SELECT *
-- 2010 table will be on top
FROM economies2010
-- which set theory clause?
UNION
-- pick specified columns from 2015 table
SELECT *
-- 2015 table on the bottom
FROM economies2015
-- order accordingly
ORDER BY code, year;

```

### Union (2)

UNION can also be used to determine all occurrences of a field across multiple tables. Try out this exercise with no starter code.

    Determine all (non-duplicated) country codes in either the cities or the currencies table. The result should be a table with only one field called country_code.
    Sort by country_code in alphabetical order.

``` sql
SELECT country_code  FROM cities
UNION
SELECT code as country_code FROM currencies
ORDER BY country_code

```

### Union all

As you saw, duplicates were removed from the previous two exercises by using UNION.

To include duplicates, you can use UNION ALL


    Determine all combinations (include duplicates) of country code and year that exist in either the economies or the populations tables. Order by code then year.
    The result of the query should only have two columns/fields. Think about how many records this query should result in.
    You'll use code very similar to this in your next exercise after the video. Make note of this code after completing it.


``` sql
SELECT code, year
FROM economies
UNION ALL
SELECT country_code as code, year
FROM populations
ORDER BY code, year;

```
Can you spot some duplicates in the query result?

### Intersect

Repeat the previous UNION ALL exercise, this time looking at the records in common for country code and year for the economies and populations tables.


    Again, order by code and then by year, both in ascending order.
    Note the number of records here (given at the bottom of query result) compared to the similar UNION ALL query result (814 records).

``` sql
SELECT code, year FROM economies
INTERSECT
SELECT country_code as code, year FROM populations
ORDER BY code, year

```
### Intersect (2)

As you think about major world cities and their corresponding country, you may ask which countries also have a city with the same name as their country name?

``` sql

SELECT name as city  FROM cities
INTERSECT
SELECT name as city FROM countries
```
### Except

Get the names of cities in cities which are not noted as capital cities in countries as a single field result.

Note that there are some countries in the world that are not included in the countries table, which will result in some cities not being labeled as capital cities when in fact they are.


```sql
SELECT name
FROM cities
EXCEPT
SELECT capital as name
FROM countries
ORDER BY name;
```
### Except (2)

Now you will complete the previous query in reverse!

Determine the names of capital cities that are not listed in the cities table


    Order by capital in ascending order.
    The cities table contains information about 236 of the world's most populous cities. The result of your query may surprise you in terms of the number of capital cities that DO NOT appear in this list!

``` sql

SELECT capital
FROM countries
EXCEPT
SELECT name as capital
FROM cities
ORDER BY capital;
```
Is this query surprising, as the instructions suggested?

### SEMI JOIN
You are now going to use the concept of a semi-join to identify languages spoken in the Middle East.
step 1
Flash back to our Intro to SQL for Data Science course and begin by selecting all country codes in the Middle East as a single field result using SELECT, FROM, and WHERE.
``` sql
SELECT code FROM countries
WHERE region LIKE 'Midd%'
```
step 2

    Comment out the answer to the previous tab by surrounding it in /* and */. You'll come back to it!
    Below the commented code, select only unique languages by name appearing in the languages table.
    Order the resulting single field table by name in ascending order.

``` sql
SELECT DISTINCT name
FROM languages
ORDER BY name
```
step 3
Now combine the previous two queries into one query:

    Add a WHERE IN statement to the SELECT DISTINCT query, and use the commented out query from the first instruction in there. That way, you can determine the unique languages spoken in the Middle East.

Carefully review this result and its code after completing it. It serves as a great example of subqueries, which are the focus of Chapter 4

``` sql
SELECT DISTINCT name
FROM languages
WHERE code IN (SELECT code FROM countries
WHERE region LIKE 'Midd%')
ORDER BY name
```

### Diagnosing problems using anti-join

Another powerful join in SQL is the anti-join. It is particularly useful in identifying which records are causing an incorrect number of records to appear in join queries.

You will also see another example of a subquery here, as you saw in the first exercise on semi-joins. Your goal is to identify the currencies used in Oceanian countries!
step 1
Begin by determining the number of countries in countries that are listed in Oceania using SELECT, FROM, and WHERE.
``` sql
SELECT count(name) FROM countries
WHERE continent LIKE 'Ocea%'
```
step 2

    Complete an inner join with countries AS c1 on the left and currencies AS c2 on the right to get the different currencies used in the countries of Oceania.
    Match ON the code field in the two tables.
    Include the country code, country name, and basic_unit AS currency.

Observe query result and make note of how many different countries are listed here.
``` sql
SELECT c1.code,name, basic_unit AS currency FROM countries c1
INNER JOIN currencies c2
ON c1.code = c2.code
WHERE continent LIKE 'Ocea%'
```
step 3
Note that not all countries in Oceania were listed in the resulting inner join with currencies. Use an anti-join to determine which countries were not included!

Use NOT IN and (SELECT code FROM currencies) as a subquery to get the country code and country name for the Oceanian countries that are not included in the currencies table.
``` sql
SELECT code, name
FROM countries
WHERE continent = 'Oceania'
  AND code NOT IN
  (SELECT code
   FROM currencies);
```
my wrong answer !!!!!
``` sql
SELECT c1.code, name, basic_unit AS currency FROM countries c1
INNER JOIN currencies c2
ON c1.code = c2.code
WHERE continent LIKE 'Ocea%'
AND c1.code NOT IN (SELECT code FROM currencies)

```
### Set theory challenge

Congratulations! You've now made your way to the challenge problem for this third chapter. Your task here will be to incorporate two of UNION/UNION ALL/INTERSECT/EXCEPT to solve a challenge involving three tables.

In addition, you will use a subquery as you have in the last two exercises! This will be great practice as you hop into subqueries more in Chapter 4


    Identify the country codes that are included in either economies or currencies but not in populations.
    Use that result to determine the names of cities in the countries that match the specification in the previous instruction.

``` sql
-- select the city name
SELECT name
-- alias the table where city name resides
FROM cities AS c1
-- choose only records matching the result of multiple set theory clauses
WHERE country_code IN
(
    -- select appropriate field from economies AS e
    SELECT e.code
    FROM economies AS e
    -- get all additional (unique) values of the field from currencies AS c2  
    UNION
    SELECT c2.code
    FROM currencies AS c2
    -- exclude those appearing in populations AS p
    EXCEPT
    SELECT p.country_code
    FROM populations AS p
);

```

### Subquery inside where
You'll now try to figure out which countries had high average life expectancies (at the country level) in 2015.
step 1 - Begin by calculating the average life expectancy across all countries for 2015.


``` sql
SELECT AVG(life_expectancy) FROM populations
WHERE year ='2015'
```
step 2 - 
Select all fields from populations with records corresponding to larger than 1.15 times the average you calculated in the first task for 2015. In other words, change the 100 in the example above with a subquery.
``` sql
SELECT * FROM populations WHERE life_expectancy > 1.15 * (
    SELECT AVG(life_expectancy) FROM populations)
    AND year =2015
    ORDER BY life_expectancy DESC LIMIT 10

```
### Subquery inside where (2)
Use your knowledge of subqueries in WHERE to get the urban area population for only capital cities.
``` sql
-- select the appropriate fields
SELECT name, country_code, urbanarea_pop
-- from the cities table
FROM cities
-- with city name in the field of capital cities
WHERE name IN
  (SELECT capital
   FROM countries)
ORDER BY urbanarea_pop DESC;
```

### Subquery inside select
In this exercise, you'll see how some queries can be written using either a join or a subquery.

You have seen previously how to use GROUP BY with aggregate functions and an inner join to get summarized information from multiple tables.

The code given in query.sql selects the top nine countries in terms of number of cities appearing in the cities table. Recall that this corresponds to the most populous cities in the world. Your task will be to convert the commented out code to get the same result as the code shown.

``` sql
SELECT countries.name AS country, COUNT(*) AS cities_num
FROM cities
INNER JOIN countries
ON countries.code = cities.country_code
GROUP BY country
ORDER BY cities_num DESC, country
LIMIT 9;


SELECT countries.name AS country,
  (SELECT COUNT(*)
   FROM cities
   WHERE countries.code = cities.country_code) AS cities_num
FROM countries
ORDER BY cities_num DESC, country
LIMIT 9;
```

### Subquery inside FROM clause
The last type of subquery you will work with is one inside of FROM.

You will use this to determine the number of languages spoken for each country, identified by the country's local name! (Note this may be different than the name field and is stored in the local_name field.)


    Begin by determining for each country code how many languages are listed in the languages table using SELECT, FROM, and GROUP BY.
    Alias the aggregated field as lang_num.
    
``` sql


```
