# 🐘 The CARTO Guide to PostGIS and PostgreSQL

[ ] Add in `FILTER`
# Overview

Welcome to the CARTO Guide to PostGIS and PostgreSQL. Both of these skills are some of the most valuable and useful you can learn at CARTO for working with geospatial and other data. This guide is mean to be tactical, allowing you to review and find specific things that you need, and assumes a basic knowledge of SQL and PostGIS. 

To learn more about PostgreSQL go [here](http://www.postgresqltutorial.com/what-is-postgresql/) and to learn more about PostGIS go here.

As you are likely aware, CARTO is built on a PostGIS/PostgreSQL database. All this means is that your data is stored in tables and you can query them using SQL commands. This makes our database very flexible and easy to use.

You can also store non-spatial data in CARTO and use that as well. Just upload it and it will work just like any other table. What makes it a spatial table is a geometry (line, point, or polygon). This is stored in many formats, and CARTO will take this in, and create two columns from that geometry: `the_geom` and `the_geom_webmercator`.

These both store the same data, but one allows us to render the map in the correct projection of our basemaps (`the_geom_webmercator`) and the other is used for everything else. `the_geom` uses the [WGS 84 projection system](https://epsg.io/4326) and `the_geom_webmercator` uses [Google Web Mercator](https://epsg.io/3857) which is the standard for web maps.

## The CARTO Trinity

In every dataset you must have the three following columns, **without exception,** to render a map in CARTO:


1. `the_geom`
2. `the_geom_webmercator`
3. `cartodb_id`

Make sure that every query includes these columns if you are planning on rendering data. If you aren’t then you don’t need them. If you are missing any of them, you will definitely see an error.


## The Most Basic Query

Whenever you open a dataset in CARTO you will see the following query show up:


    SELECT * FROM tablename

This is the foundation of every single query in CARTO, and in PostGIS/PostgreSQL. Let’s break it down:

`SELECT` is telling the query to retrieve something from the database and display it
`*` is telling the query to grab all columns
`FROM` is telling the query which table in your database to get the data from
`tablename` is the specific table in the database that you are getting the data from.

In this query you will return all rows and columns in that data table. Congrats! You just wrote your first query.

You can also select specific columns like this:


    SELECT column_one FROM tablename

or this:


    SELECT column_one, column_two FROM tablename

These are the fundamentals of any SQL query. In the next section, we will learn how to filter your results using some basic SQL commands in PostgreSQL. 

# The Basics

Now that you know how to return results from a table, let’s try out some conditional statements to filter the data that we want to see. As you go through these exercises you will see how CARTO filters and changes data and you should think about how this shows up in our products (how widgets work, how to create buffers, etc.). This will give you a much deeper understanding of how CARTO works.

To test this you can use this dataset of [rail accidents in the US](https://team.carto.com/u/mforrest/dataset/accidents)

## WHERE

Let’s filter our data a bit. To show accidents that happened in Minnesota:


    SELECT * FROM accidents WHERE state = 'Minnesota'

Check out your results and they should only show results where the value in the `state` column corresponds to `Minnesota`. This is one of the most common operations in SQL. 

For numeric values you can use the following values as well `<, >, >=, <=, != (Not Equal)`

[More on](http://www.postgresqltutorial.com/postgresql-where/) `[WHERE](http://www.postgresqltutorial.com/postgresql-where/)`

## AND

But now you want to see more than one condition. Let’s use the `AND` operator and a numeric column:


    SELECT * FROM accidents 
    WHERE state = 'Minnesota' 
    AND equipment_damage > 1000000

This will return results in Minnesota with equipment damage over $1M. You can chain together as many `AND` statements as you want

## OR

Similar to `AND` you can use an `OR` statement, however this will include all values that meet both conditions. So for example this query:


    SELECT * FROM accidents 
    WHERE state = 'Minnesota' 
    OR state = 'Wisconsin'

This will return all results that are in Minnesota and Wisconsin. If you changed this to an `AND` query, you would see no results since there are no values in both states.

## ORDER BY

Now let’s order our data. Let’s run this query but we should add an `ORDER BY` statement:


    SELECT * FROM accidents 
    WHERE state = 'Minnesota' 
    AND equipment_damage > 1000000
    ORDER BY equipment_damage DESC

This will order the data by the values in the `equipment_damage` column in descending order. You can also use `ASC` to order it in ascending order.

[More on](http://www.postgresqltutorial.com/postgresql-order-by/) `[ORDER BY](http://www.postgresqltutorial.com/postgresql-order-by/)`

## DISTINCT

You can also grab distinct values from a dataset. For example this query:


    SELECT DISTINCT state FROM accidents 

Will return a list of distinct values in the state column. You can also use this in this format


    SELECT DISTINCT * FROM accidents 

Which will remove duplicate values from your dataset, although there are no duplicates in this particular dataset.

[More on](http://www.postgresqltutorial.com/postgresql-select-distinct/) `[DISTINCT](http://www.postgresqltutorial.com/postgresql-select-distinct/)`

## IN

We can also find multiple values from one column using the `IN` method:


    SELECT * FROM accidents 
    WHERE state IN ('Minnesota', 'Wisconsin', 'Iowa', 'Illinois')

This will return all the results within that query, so any value where `state` is equal to `'Minnesota', 'Wisconsin', 'Iowa', 'Illinois``'`

[More on](http://www.postgresqltutorial.com/postgresql-in/) `[IN](http://www.postgresqltutorial.com/postgresql-in/)`


## BETWEEN

We can find values that fall between two other values using the `BETWEEN` method:


    SELECT * FROM accidents 
    WHERE total_damage 
    BETWEEN 1000000 AND 3000000

You can also do this for date ranges:


    SELECT * FROM accidents 
    WHERE date 
    BETWEEN '2010-07-01T00:00:00Z' AND '2010-12-31T00:00:00Z'

[More on](http://www.postgresqltutorial.com/postgresql-between/) `[BETWEEN](http://www.postgresqltutorial.com/postgresql-between/)`

## LIKE & ILIKE

Let’s say you want to search text values in a specific column but don’t want to find an exact match, just if a specific phrase or word is contained in some text. In this dataset we have the `narrative` column that has a long paragraph about what caused the accident. Let’s look for values that contain the word ‘SPEED’:


    SELECT * FROM accidents 
    WHERE narrative 
    LIKE '%SPEED%'

This will return all results that have the word ‘SPEED’ within the text in the `narrative` column

There are several pattern matching methods that you can use to match different parts of the text:


    SELECT * FROM accidents 
    WHERE narrative 
    LIKE 'SPEED'

This query will return no results since it is looking for text that matches ‘SPEED’ exactly.


    SELECT * FROM accidents 
    WHERE narrative 
    LIKE 'SPEED%'

This returns one results since it looks for values that start with ‘SPEED’ and has any other text after. In reverse:


    SELECT * FROM accidents 
    WHERE narrative 
    LIKE '%SPEED'

This will look for results that end with ‘SPEED’ of which there are none in this dataset.

Our original query looks for the word ‘SPEED’ in any part of the string. To look for the word ‘SPEED’ at the beginning, end, and middle, we can use an `OR` statement


    SELECT * FROM accidents 
    WHERE narrative 
    LIKE '%SPEED'
    OR narrative LIKE 'SPEED%'
    OR narrative LIKE '%SPEED%'

Additionally you can use `NOT LIKE` as well to exclude results:


    SELECT * FROM accidents 
    WHERE narrative 
    NOT LIKE '%SPEED%'

`ILIKE` is another version of `LIKE`, which is the same, but it is **not case sensitive,** whereas `LIKE` is **case sensitive.** For example:


    SELECT * FROM accidents 
    WHERE narrative 
    ILIKE '%speed%'

Will return the same results as our original query, however this query will return no results:


    SELECT * FROM accidents 
    WHERE narrative 
    LIKE '%speed%'

[More on](http://www.postgresqltutorial.com/postgresql-like/) `[LIKE](http://www.postgresqltutorial.com/postgresql-like/)` [and](http://www.postgresqltutorial.com/postgresql-like/) `[ILIKE](http://www.postgresqltutorial.com/postgresql-like/)`

## LIMIT

You can also limit the number of results you return using a `LIMIT` statement:


    SELECT * FROM accidents 
    LIMIT 5

This should return only 5 rows. A `LIMIT` statement always goes at the end of a statement. For example:


    SELECT * FROM accidents 
    WHERE state = 'Minnesota'
    AND narrative LIKE '%SPEED%'
    LIMIT 5

[More on](http://www.postgresqltutorial.com/postgresql-limit/) `[LIMIT](http://www.postgresqltutorial.com/postgresql-limit/)`

# Aggregations & Transformations

Now we have the basics for querying and organizing our data. Next we can start to aggregate and transform our data. Let’s look at some basic aggregations and transformations.


## SUM, AVG, MIN, MAX, COUNT

Let’s run the following query:


    SELECT SUM(total_damage) 
    FROM accidents 

This should return a value of 1,441,014,085 which is the sum of the `total_damage` column. Great! We just did a simple math operation in PostgreSQL. You can do the same with the following values:


- `MIN` - returns the minimum value from the column selected
- `MAX` - returns the maximum value from the column selected
- `AVG` - returns the average value from the column selects

You can also see the count of all the attributes in your table by running this query:


    SELECT COUNT(*) 
    FROM accidents 

Great! That gives us the count. We can also rename our columns if we want to get multiple aggregation values:


    SELECT 
    SUM(total_damage) as sum_of_total_damage,
    SUM(equipment_damage) as sum_of_equipment_damage
    FROM accidents 

Give this a shot for a few values and methods. You can also do some simple math in your query which will work with common math operators:


    SELECT 
    SUM(total_damage) + SUM(equipment_damage) as equipment_plus_total_damage
    FROM accidents 

But let’s say we want to see the sum of `total_damage` in each state. We could try:


    SELECT 
    SUM(total_damage),
    state
    FROM accidents 

And we would get an error:

`Syntax error: column "accidents.state" must appear in the GROUP BY clause or be used in an aggregate function`


## GROUP BY

Now we can introduce the `GROUP BY` method which allows you to group by a specific value in your data and which works similarly to a `SELECT DISTINCT` statement. Let’s give it a shot:


    SELECT 
    SUM(total_damage),
    state
    FROM accidents 
    GROUP BY state

This should return a list of every state and the sum of the total damage in each state. Let’s order it so we can see the list in descending order:


    SELECT 
    SUM(total_damage),
    state
    FROM accidents 
    GROUP BY state
    ORDER BY sum DESC

Great! You can change this to `MIN`, `MAX`, and `AVG` as well, or change the grouping column to something else (give `railroad` a try).


## CONCAT

Now let’s get into some more advanced data manipulation. These next functions will allow us to manipulate and format our data in a variety of ways so that we can create columns and data to fit our specific needs.

The first is `CONCAT` which will allow us to manipulate and join strings together in a new column of data. Let’s try this with an example.


    SELECT 
    CONCAT(
      'RR: ',
      railroad,
      ' / Accident Type: ',
      accident_type                  
    )
    FROM accidents

This should return a value that looks like this:

`RR: SEPA / Accident Type: Fire/violent rupture` 

Basically what we did is joined our values together with some text using the concat function:

`'``RR:` `'` `+ {railroad} +` `'` `/ Accident Type` `'` `+ {accident_type}` 

You can join as many columns or values as you want, just keep adding a comma after each value and make sure to have your spacing correct (notice the spaces within the quotes). We also manually wrote in string values here and contained them within quotes (i.e. `' / Accident Type:` `'`). This is totally valid and allows you to template the data in your query a bit. Let’s combine this with a `GROUP BY` and `SUM`:


    SELECT 
    CONCAT(
      state, 
        ' has $',
        SUM(total_damage),
        ' in total damage'
    )
    FROM accidents 
    GROUP BY state
    ORDER BY concat ASC

This should return:

`Alabama has $25805738 in total damage`
`Alaska has $493564 in total damage`
etc…

Great! Try this out a few different times with different values to practice this method as many of the other functions we are about to review use a very similar methodology.

## TO_CHAR

We can transform numeric and date variables into formatted text fields using `TO_CHAR` like this:


    SELECT
    TO_CHAR(total_damage, '999,999,999,999.99')
    FROM accidents

This should return a field that has a text value of `total_damage` represented as a string with commas:

`11,293.00`

You can do the same with date variables as well:


    SELECT
    TO_CHAR(date, 'Mon-DD-YYYY')
    FROM accidents

Which will return a date like this:

`Oct-11-2011`

There are many different ways to format data using `TO_CHAR` so it is worth reading and testing out different options:

[More on](http://www.postgresqltutorial.com/postgresql-to_char/) `[TO_CHAR](http://www.postgresqltutorial.com/postgresql-to_char/)`


## TO_DATE

The `TO_DATE` function will allow you to transform a text field to a date timestamp:


    SELECT
    TO_DATE(month, 'YY-Mon')
    FROM accidents

This will return something like this: 

`2011-10-01T00:00:00Z`

We will learn more about data formats soon but a date field is a specific type of data that we use for dates and times.

[More on](http://www.postgresqltutorial.com/postgresql-to_date) `[TO_DATE](http://www.postgresqltutorial.com/postgresql-to_date)`


## TO_NUMBER

What if we need to turn a string text variable into a number:


    SELECT
    TO_NUMBER(year, '9999')
    FROM accidents

This should return the same thing, but as a number. You can do this with any text field, for example:


    SELECT
    TO_NUMBER('123,456,789', '99999999999')

Will perform the same operation.

[More on](http://www.postgresqltutorial.com/postgresql-to_number) `[TO_NUMBER](http://www.postgresqltutorial.com/postgresql-to_number)`
[](http://www.postgresqltutorial.com/postgresql-to_date)

## ROUND

If you need to round a numeric variable you can use `ROUND`:


    SELECT
    ROUND(random)
    FROM accidents

You can also specify the number of decimal places:


    SELECT
    ROUND(random, 2)
    FROM accidents

[More on](http://www.postgresqltutorial.com/postgresql-round/) `[ROUND](http://www.postgresqltutorial.com/postgresql-round/)`

## CEIL

Similarly to `ROUND` you can use `CEIL` to round up:


    SELECT
    CEIL(random)
    FROM accidents

This will round up but only to the nearest full integer.

[More on](http://www.postgresqltutorial.com/postgresql-ceil/) `[CEIL](http://www.postgresqltutorial.com/postgresql-ceil/)`

## TRUNC

`TRUNC` will truncate your numeric value in any specific direction. For example to truncate your number to two decimal places you can run this query:


    SELECT
    TRUNC(random, 2)
    FROM accidents

This will also work for numbers before the decimal by using a negative value:


    SELECT
    TRUNC(random, -3)
    FROM accidents

[More on](http://www.postgresqltutorial.com/postgresql-trunc/) `[TRUNC](http://www.postgresqltutorial.com/postgresql-trunc/)`


## ARRAY_AGG

Let’s say that you want to see a list of the total damage of accidents over $5M, in each state,  and the companies that caused them. This sounds tricky, but using `ARRAY_AGG` we can do that like this:


    SELECT 
    SUM(total_damage),
    state,
    ARRAY_AGG(railroad)
    FROM accidents 
    WHERE total_damage > 5000000
    GROUP BY state
    ORDER BY sum DESC

Let’s look at one row of data from this query:

| **sum**  | **state** | **array_agg**    |
| -------- | --------- | ---------------- |
| 30846938 | Missouri  | UP,BNSF,BNSF,KCS |

What we see here is the sum of the damage from accidents over $5M in Missouri and the companies responsible for those accidents, in this case UP, BNSF, BNSF (again) , and KCS

What this does is it allows us to preserve other data in a field without doing two `GROUP BY` clauses. You can do this in `GROUP BY` but the results are much different:


    SELECT 
    SUM(total_damage),
    state,
    railroad,
    cartodb_id
    FROM accidents 
    WHERE total_damage > 5000000
    AND state = 'Missouri'
    GROUP BY state, railroad, cartodb_id
    ORDER BY sum DESC

As you can see here we added the `cartodb_id` to ensure we got the individual accidents data back. Let’s look at the results, which we limited to accidents in Missouri.


| **sum** | **state** | **array_agg** | **cartodb_id** |
| ------- | --------- | ------------- | -------------- |
| 8686769 | Missouri  | UP            | 3825           |
| 8686769 | Missouri  | BNSF          | 8724           |
| 6736700 | Missouri  | KCS           | 8906           |
| 6736700 | Missouri  | BNSF          | 2709           |

This shows us the individual accidents so the `SUM` function is doing nothing here. It is the equivalent of writing this query:


    SELECT 
    total_damage,
    state,
    railroad,
    cartodb_id
    FROM accidents 
    WHERE total_damage > 5000000
    AND state = 'Missouri'
    ORDER BY total_damage DESC

We want to see the individual accidents **and the total damage in the state.** Let’s try this again, but with another function we already know:


    SELECT 
    SUM(total_damage),
    state,
    ARRAY_AGG(
      CONCAT(railroad, 
             ': $', 
             TO_CHAR(total_damage, '999,999,999,999')))
    FROM accidents 
    WHERE total_damage > 5000000
    GROUP BY state
    ORDER BY sum DESC

Let’s check out our results for Missouri again:

| **sum**  | **state** | **array_agg**                                                           |
| -------- | --------- | ----------------------------------------------------------------------- |
| 30846938 | Missouri  | UP: $ 8,686,769,
BNSF: $ 8,686,769,
BNSF: $ 6,736,700,
KCS: $ 6,736,700 |

Awesome! We have the sum of the total damage of all accidents in Missouri.

We can do this with any sort of data. Let’s take a look at two other aggregation methods.


## STRING_AGG

The `STRING_AGG` function is more or less the same as `ARRAY_AGG` but you can choose the formatting for how you separate the elements in your array. Such as:


    SELECT 
    SUM(total_damage),
    state,
    STRING_AGG(
      CONCAT(railroad, 
             ': $', 
             TO_CHAR(
               total_damage, '999,999,999,999')
              ),
            ' || ')
    FROM accidents 
    WHERE total_damage > 5000000
    GROUP BY state
    ORDER BY sum DESC

This will return:

| **sum**  | **state** | **array_agg**                                                                                         |
| -------- | --------- | ----------------------------------------------------------------------------------------------------- |
| 30846938 | Missouri  | UP: $       8,686,769 || BNSF: $       8,686,769 || BNSF: $       6,736,700 || KCS: $       6,736,700 |

This is somewhere where you can embed HTML elements if you are building an interactive map or table. For example:


    SELECT 
    SUM(total_damage),
    state,
    STRING_AGG(
      CONCAT('<p>',
             railroad, 
             ': $', 
             TO_CHAR(
               total_damage, '999,999,999,999'),
             '</p>'
              ),
            '</p><p>')
    FROM accidents 
    WHERE total_damage > 5000000
    GROUP BY state
    ORDER BY sum DESC

Will return:

| **sum**  | **state** | **array_agg**                                                                                                                                 |
| -------- | --------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| 30846938 | Missouri  | <p>UP: $       8,686,769</p>
<br />
<p>BNSF: $       8,686,769</p>
<br />
<p>BNSF: $       6,736,700</p>
<br />
<p>KCS: $       6,736,700</p> |

## JSON_AGG

Just like the other functions, `JSON_AGG` returns data but in JSON format. This is useful when making requests to the CARTO SQL API.


    SELECT 
    SUM(total_damage),
    state,
    JSON_AGG(
      CONCAT(railroad, 
             ': $', 
             total_damage
            )
          )
    FROM accidents 
    WHERE total_damage > 5000000
    GROUP BY state
    ORDER BY sum DESC

This query will return the following results from the SQL API (in JSON format)


    ...
    {
        - sum: 30846938,
        - state: "Missouri",
        - json_agg: 
        - [
          - "UP: $8686769",
          - "BNSF: $8686769",
          - "BNSF: $6736700",
          - "KCS: $6736700"
        - ]
    },
    {
        - sum: 23459246,
        - state: "Oklahoma",
        - json_agg: 
        - [
          - "UP: $11729623",
          - "UP: $11729623"
        - ]
    },
    ...

This makes it very useful especially when using the SQL API in a Javascript application.


## Data Types & Casting

There are many, many, many different data types that you can store in PostgreSQL. [Here is a list of a few of them](http://www.postgresqltutorial.com/postgresql-data-types/). In CARTO there are a few that you will be dealing with a lot:


- `numeric`: Numeric falue
- `text`: Text or string value
- `geometry`: Geometry for a geographic feature
- `boolean`: True or False value
- `date`: Date/Time

You can set the value of any column at any time as we will learn shortly, or you can cast it to a specific data type within a query, although this will only stay that way in the context of the query:


    SELECT 
    equipment_damage::text,
    year::numeric
    FROM accidents

This effectively changed the `equipment_damage` column to a text value and the `year` column to a numeric value. This comes in handy when you need to easily change data in the context of a query, but not on the core dataset.

[More on Casting](http://www.postgresqltutorial.com/postgresql-cast/)

## CASE/WHEN

Another handy function is to use `CASE` and `WHEN` as a conditional operation. Let’s say we want to add a numeric value to our dataset based on the `cause` column:


    SELECT
    cause,
    CASE 
      WHEN cause = 'Signal and Communication' THEN 2
      WHEN cause = 'Mechanical and Electrical Failures' THEN 1
      WHEN cause = 'Rack, Roadbed and Structures' THEN 3
      ELSE 0
    END as rating
    FROM accidents

This will set each value that matched the condition to a number that we give it, for all other values that don’t match the condition, it will return 0.

| Rack, Roadbed and Structures              | 3 |
| ----------------------------------------- | - |
| Signal and Communication                  | 2 |
| Mechanical and Electrical Failures        | 1 |
| Train operation - Human Factors           | 0 |
| Miscellaneous Causes Not Otherwise Listed | 0 |

This is a very helpful function for classifying and creating conditional logic in your dataset.

[More on](http://www.postgresqltutorial.com/postgresql-case/) `[CASE](http://www.postgresqltutorial.com/postgresql-case/)`

## NULL Values

Many times in your data you will see the word `null` which basically means there is no data in that cell. This works fine for text fields, but for numbers you will want to change this. We can actually do that with an `UPDATE` statement, which we will learn about in the next section!

# More Advanced Operations

So far we have only been running `SELECT` statements which allows us to query and filter or data, but not modify it at all. Let’s learn some new operations to modify our data with SQL.


> ⚠️ **NOTE: All these statements will change the dataset you are testing them on. You should create a copy of your dataset to run these queries ⚠️** 
## UPDATE

The first new statement we will look at is an update statement:


    UPDATE accidents
    SET railroad = 'No Value'

This statement would set all the values in the `railroad` column equal to ‘No Value’. This also works with conditionals:


    UPDATE accidents
    SET railroad = 'Union Pacific'
    WHERE railroad = 'UP'

Which would only change the value for rows where `railroad` was equal to ‘UP’

This statement **would change your data completely** and there are no redos here so **make sure this is what you want to do.** The recommended path is to create a new column which we will learn about in the `ALTER TABLE` command shortly and update that new column with the update statement:


    UPDATE accidents
    SET railroad_new = 'Union Pacific'
    WHERE railroad = 'UP'

You can also use another table to update values in the table you are working on:


    UPDATE accidents
    SET railroad_new = accidents_new.railroad_names
    FROM accidents_new
    WHERE accidents_new.railroad_names = accidents.railroad

This is also another important concept. You will see this reference in the query `accidents.railroad` which is referencing a column in the `accidents` dataset. It follows this format: `{tablename}.{columnname}` which allows you to reference two tables as we are doing above. We are:


- `UPDATE`-ing the `accidents` table
- With values that are `FROM` the `accidents_new` table

[More on](http://www.postgresqltutorial.com/postgresql-update/) `[UPDATE](http://www.postgresqltutorial.com/postgresql-update/)`

## INSERT

You can also run an `INSERT` statement on your table which will add new values to the table:


    INSERT INTO accidents (railroad, total_damage)
    VALUES ('UP', 100000)

This is a direct insert statement but you can also run the following statement to get values from another table:


    INSERT INTO accidents (railroad, total_damage)
    SELECT railroad, total_damage
    FROM accidents_new

Or you can add a `WHERE` statement here as well:


    INSERT INTO accidents (railroad, total_damage)
    SELECT railroad, total_damage
    FROM accidents_new
    WHERE accidents_new.railroad = 'UP'

You can also run a conditional `INSERT`


    INSERT INTO accidents (railroad, total_damage)
    SELECT railroad, total_damage
    FROM accidents_new
    WHERE accidents_new.railroad = 'UP'
    ON CONFLICT (railroad)
    DO NOTHING

This will tell the query to do nothing if there is a `railroad` value that is equal to ‘UP’

You can also run a conditional update:


    INSERT INTO accidents (railroad, cause)
    SELECT railroad, cause
    FROM accidents_new
    WHERE accidents_new.railroad = 'UP'
    ON CONFLICT (railroad)
    DO 
      UPDATE
        SET cause = EXCLUDED.cause || ', ' || accidents.cause

For that result it would concatenate the `cause` column so before the insert it would look like

`'``Train problem``'`

And after it would look like

`'Train problem``'``,` `'``Radio issue``'`

[More on](http://www.postgresqltutorial.com/postgresql-insert/) `[INSERT](http://www.postgresqltutorial.com/postgresql-insert/)`
[More on conditional](http://www.postgresqltutorial.com/postgresql-upsert/) `[INSERT](http://www.postgresqltutorial.com/postgresql-upsert/)`


## ALTER TABLE

The `ALTER TABLE` function allows you to modify the structure of a table completely. There are many different actions you can use with `ALTER TABLE`.


    ALTER TABLE accidents
    ADD COLUMN description TEXT,
    ADD COLUMN cost_of_accident NUMERIC

Obviously `ADD COLUMN` will add a new column with a column name and the data type following it.


    ALTER TABLE accidents
    DROP COLUMN description TEXT,
    DROP COLUMN cost_of_accident NUMERIC

And you can easily remove a column using `DROP COLUMN`


    ALTER TABLE accidents
    RENAME COLUMN description TO some_description,
    RENAME COLUMN cost_of_accident TO accident_cost

Or rename your column with `RENAME COLUMN`


    ALTER TABLE accidents
    ADD CONSTRAINT valid_equipment_damage 
    CHECK (
      total_damage > 0
      AND equipment_damage >= 0
      AND equipment_damage <= total_damage
    )

We can also add a `[CHECK](http://www.postgresqltutorial.com/postgresql-check-constraint/)` constraint to our table which will validate inserted or updated data. This adds rules to our table so if a value is `INSERT`-ed or `UPDATE`-d on `equipment_damage` it must:


- Have a `total_damage` greater than 0
- Have an `equipment_damage` greater than or equal to 0
- And `total_damage` must be greater than or equal to `equipment_damage`


    ALTER TABLE accidents
    RENAME TO accidents_other

And you can rename your table as well.

[More on](http://www.postgresqltutorial.com/postgresql-alter-table/) `[ALTER TABLE](http://www.postgresqltutorial.com/postgresql-alter-table/)`


## CREATE TABLE

You can also create tables from other queries using the `CREATE TABLE` syntax:


    CREATE TABLE up_accidents AS
    SELECT * 
    FROM accidents
    WHERE railroad = 'UP'

This will create a new table called `up_accidents` from the results of the query which will show only accidents associated with 'UP’.

This is a **very useful query** to create derivative data based on a larger query that may timeout in a standard `SELECT` statement.

However in CARTO this will only show up on the database. To get it to show up in the user interface you need to run this query as well:


    SELECT 
    cdb_cartodbfytable('user_name', 'up_accidents')

Run that query once and your table will show up.

[More on](http://www.postgresqltutorial.com/postgresql-create-table/) `[CREATE TABLE](http://www.postgresqltutorial.com/postgresql-create-table/)`


## JOINS

Joins are a big and somewhat complex topic. This is basically joining one set of values from one table to another set of values from a different table based on some sort of condition. Let’s take a look at a few different types of joins.


- `INNER JOIN`: Will return the records where *table1* and *table2* intersect
- `LEFT JOIN`: Will return the all records from *table1* and only those records from *table2* that intersect with *table1*
- `RIGHT JOIN`: Will return the all records from *table2* and only those records from *table1* that intersect with *table2*.
- `FULL OUTER JOIN`: Will return the all records from both *table1* and *table2*
- `CROSS JOIN`: Will join every value from *table1* to all other values of *table2*


We can use this table to test these out:

[accident_types](https://team.carto.com/u/mforrest/dataset/accident_types)

The format for joins are for the most part the same:


    SELECT 
      accidents.railroad,
      accidents.accident_type,
      accident_types.type,
      accident_types.total_damage
    FROM
      accidents
    INNER JOIN
      accident_types ON type = accidents.accident_type

This will return all results from `accidents` which will have many repeat values from `accident_types`

You can also add conditionals


    SELECT 
      accidents.railroad,
      accidents.accident_type,
      accident_types.type,
      accident_types.total_damage
    FROM
      accidents
    INNER JOIN
      accident_types ON type = accidents.accident_type
    WHERE accident_types.total_damage > 50000000

[More on](http://www.postgresqltutorial.com/postgresql-inner-join/) `[INNER JOIN](http://www.postgresqltutorial.com/postgresql-inner-join/)`

Now, since in each table there are the same set of values, each of these joins, apart from `CROSS JOIN` will more or less work the same way. For the most part `INNER JOIN` is the most common type of join used in CARTO. You can try each of these and find the best fit join for your data.

[More on](http://www.postgresqltutorial.com/postgresql-left-join/) `[LEFT JOIN](http://www.postgresqltutorial.com/postgresql-left-join/)`
[More on](http://www.postgresqltutorial.com/postgresql-cross-join/) `[CROSS JOIN](http://www.postgresqltutorial.com/postgresql-cross-join/)`
[More on](http://www.postgresqltutorial.com/postgresql-full-outer-join/) `[FULL OUTER JOIN](http://www.postgresqltutorial.com/postgresql-full-outer-join/)`

One very common way of using joins is to quickly query and join data to geometries. For example imagine that you have created your list of states grouped by total damage, and you want to join that to a datset of state boundaries. Let’s give that a shot using [this dataset of states](https://team.carto.com/u/mforrest/dataset/us_states).


    SELECT 
      us_states.*,
      sum(accidents.total_damage) as all_damage,
      sum(accidents.equipment_damage) as equipment_damage
    FROM
      accidents
    INNER JOIN
      us_states ON us_states.state = accidents.state
    GROUP BY
      us_states.state,
      us_states.cartodb_id

This will return a joined dataset of all the columns from the `us_states` dataset using `us_states.*` and the sum of `total_damage` and `equipment_damage` from the `accidents` table. You could optionally use a `WHERE` clause in this query as well.


## UNION

Let’s say that you have two different tables with the exact same table structure, meaning you have the exact same number of columns, the column names and data types are the same, but the values are different. You can easily join them with a `UNION`:


    SELECT * FROM table_a
    UNION
    SELECT * FROM table_b
    UNION
    SELECT * FROM table_c

This joins all these tables together, so if each of the above tables has 100 rows, you would have a dataset with 300 rows with the above query.

In CARTO however you will need the `cartodb_id` column, but if you have many tables with a `cartodb_id` you will get an error since the id must be a unique id, i.e. it must not repeat. So if there are two `cartodb_id`'s with the same value you must reset them. You can do this like so:


    WITH a AS (
    SELECT
      railroad,
      total_damage,
      state
    FROM accidents
    UNION
    SELECT
      railroad,
      total_damage,
      state
    FROM accidents_new
    )
    SELECT 
    *,
    row_number() over() as cartodb_id
    FROM a

This introduces two concepts, the first is a nested table which we will cover in the next section. The other is this function:

`row_number() over() as cartodb_id`

This will create a new number and increase it by one as a new column called `cartodb_id`. This will work as long as you do not include the `cartodb_id` in your original query.


## Subqueries

As you have seen above you can use subqueries to query different tables, store the results of that query within the main query, and use them to do something else later. Let’s test this out:


> ⚠️ Note that subqueries, or too many subqueries can degrade performance so try to use them only when necessary


    WITH a AS (
    SELECT
      *
    FROM accidents
    WHERE total_damage > 10000000
    )
    SELECT 
      *
    FROM a

If you run this you will see results from accidents where `total_damage` is over $10M - however this data is now stored in a subquery with the table reference of `a` if we run this:


    WITH a AS (
    SELECT
      *
    FROM accidents
    WHERE total_damage > 10000000
    )
    SELECT 
      *
    FROM a
    WHERE nearest_city = 'GOODWELL'

We will see two rows, but the `WHERE` clause is being applied to the `a` table. You can also chain queries together:


    WITH a AS (
    SELECT
      *
    FROM accidents
    WHERE total_damage > 100000
    ),
    b AS (
    SELECT
      *
    FROM a
    WHERE year BETWEEN '2010' AND '2012'
    )
    SELECT 
      *
    FROM b

This also works in queries like this:


    SELECT
    *
    FROM 
    us_states
    WHERE state IN 
    (SELECT state FROM accidents WHERE equipment_damage > 5000000)

You can also use other methods such as `EXISTS`, `ANY`, or `ALL`


    SELECT 
    *
    FROM 
    accidents
    WHERE EXISTS
    (SELECT 
      type 
    FROM accident_types 
    WHERE total_damage < 15000000 
    AND type = accidents.accident_type)


    SELECT 
    *
    FROM 
    accidents
    WHERE equipment_damage >= ANY
    (SELECT total_damage FROM accident_types)

This subquery must return only one column

This will select all the results from `accidents` that match the `type` from `accident_types` where the `total_damage` is over $15M. We can also use `ALL` in a very similar way.

[More on Subqueries](http://www.postgresqltutorial.com/postgresql-subquery/)
[More on](http://www.postgresqltutorial.com/postgresql-exists/) `[EXISTS](http://www.postgresqltutorial.com/postgresql-exists/)`
[More on](http://www.postgresqltutorial.com/postgresql-any/) `[ANY](http://www.postgresqltutorial.com/postgresql-any/)`
[](http://www.postgresqltutorial.com/postgresql-any/)[More on](http://www.postgresqltutorial.com/postgresql-all/) `[ALL](http://www.postgresqltutorial.com/postgresql-all/)`

## INDEX

Adding an `INDEX` to a table is a method that will help you quickly query your data by indexing values in that column. This helps speed up queries on the columns you are querying. This will help performance on querying large datasets.


    CREATE INDEX
    ON accidents
    (total_damage)

You can add an index for any column in your dataset but only add them for the columns you are going to query against.

We are only scratching the surface of some of these topics and you can learn more in [this set of guides](http://www.postgresqltutorial.com/).

# PostGIS Functions & Overview

As you have noticed we have not done much with PostGIS up to this point and that is because a good base in PostgreSQL will allow you to have a much better base of PostGIS. PostGIS itself is an extension of PostgreSQL that adds the ability to work with geometries in the database and perform different functions on the geometry column.

CARTO stores two different geometry types as listed above and in this guide we will point out how to use and work with both, however the main one you will work with is called `the_geom`. This stores your geographic data in a PostGIS geometry format and we will use it to perform many different operations.

First, let’s take a look at `the_geom` in our dataset of accidents:


    SELECT
    the_geom
    FROM accidents

You should see a pair of lat, long coordinates. If we try this:


    SELECT
    the_geom::geography
    FROM accidents

You will see something like this:

`0101000020E61000004FCE50DCF1BB52C0637C98BD6C1D4440`

This string represents the position of the geometry on the curved surface of the Earth, since the earth is our reference system. One could create a geogrpahy for anything (a baseball field, Mars, a house, etc.) but since we are dealing with the Earth, that is our geography system.

# Geometry Functions
## ST_AsText

Now let’s try this:


    SELECT
    ST_AsText(the_geom)
    FROM accidents

This will return a plain text version of our geometry:

`POINT(-74.936637 40.229881)`

This is how PostGIS will read our geometry. There are many geometry types: `POINT`, `LINE`, `POLYGON`, `MULTILINESTRING`, `MULTIPOINT`, `MULTIPOLYGON`, and `GEOMETRYCOLLECTION`.

`ST_AsText` is very helpful when looking at your data and when using different functions.

[More on](https://postgis.net/docs/ST_AsText.html) `[ST_AsText](https://postgis.net/docs/ST_AsText.html)`


## ST_GeomFromText

You could do this in reverse and use text to generate a geometry such as:


    SELECT
    ST_GeomFromText('POINT(-74.936637 40.229881)')

[More on](https://postgis.net/docs/ST_GeomFromText.html) `[ST_GeomFromText](https://postgis.net/docs/ST_GeomFromText.html)`


## ST_GeomFromGeoJSON

Or you can use GeoJSON which is a common format for 


    SELECT
    ST_GeomFromText('{"type":"Point","coordinates":[-74.936637,40.229881]}')

[More on](https://postgis.net/docs/ST_GeomFromGeoJSON.html) `[ST_GeomFromGeoJSON](https://postgis.net/docs/ST_GeomFromGeoJSON.html)`

## ST_MakePoint and ST_SetSRID

You can also create a point using `ST_MakePoint` and set the correct Projection System using `ST_SetSRID`:


    SELECT
    ST_SetSRID(ST_MakePoint(-74.936637, 40.229881), 4326) as the_geom

This is a useful function for insert statements.

[More on](https://postgis.net/docs/ST_MakePoint.html) `[ST_MakePoint](https://postgis.net/docs/ST_MakePoint.html)`
[More on](https://postgis.net/docs/ST_SetSRID.html) `[ST_SetSRID](https://postgis.net/docs/ST_SetSRID.html)`

## ST_MakeLine

Let’s make a line from a few of our points.


    WITH a AS (
    SELECT ARRAY_AGG(the_geom) AS the_geom 
    FROM accidents 
    WHERE cartodb_id BETWEEN 5 AND 10)
    
    SELECT ST_AsText(ST_MakeLine(the_geom)) 
    FROM a

This query will return a line from our points (as text). 

If we want to see it on the map we have to add a few more things that we already know about, and a few we don’t but we will see soon:


    WITH a AS (
    SELECT ARRAY_AGG(the_geom) AS the_geom 
    FROM accidents 
    WHERE cartodb_id BETWEEN 5 AND 10)
    
    SELECT 
    ST_MakeLine(the_geom) as the_geom,
    ST_Transform(ST_MakeLine(the_geom), 3857) as the_geom_webmercator,
    row_number() over() as cartodb_id
    FROM a

You will see `ST_Transform` here which is re-projecting our `the_geom` column into the format that `the_geom_webmercator` needs. If you remember this is one of the three columns CARTO needs to render a map. If we open this up in a map we will see the following:


![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1528490375672_Screen+Shot+2018-06-08+at+4.39.22+PM.png)


[More on](https://postgis.net/docs/ST_MakeLine.html) `[ST_MakeLine](https://postgis.net/docs/ST_MakeLine.html)`


## ST_Envelope

Now let’s try the same thing but for a bounding box around our points:


    WITH a AS (
    SELECT the_geom 
    FROM accidents 
    WHERE cartodb_id BETWEEN 5 AND 10)
    
    SELECT 
    ST_Envelope(ST_Collect(the_geom)) as the_geom,
    ST_Transform(ST_Envelope(ST_Collect(the_geom)), 3857) as the_geom_webmercator,
    row_number() over() as cartodb_id
    FROM a

This use a function called `ST_Envelope` which takes a collection of geometries, which is generated by `ST_Collect`. The result is:


![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1528491129790_Screen+Shot+2018-06-08+at+4.51.38+PM.png)


This would work for lines and polygons as well.

[More on](https://postgis.net/docs/ST_Envelope.html) `[ST_Envelope](https://postgis.net/docs/ST_Envelope.html)`
[More on](https://postgis.net/docs/ST_Collect.html) `[ST_Collect](https://postgis.net/docs/ST_Collect.html)`


## ST_Intersects

Let’s say that you want to see which points intersect the boundary of Minnesota. You can use `ST_Intersects` to do so:


    SELECT 
    accidents.*
    FROM accidents, us_states
    WHERE ST_Intersects(accidents.the_geom, us_states.the_geom)
    AND us_states.state = 'Minnesota'

This is a very common spatial analysis that you will come across again and again and is incredibly useful to see which locations intersect another location. With `ST_Intersects` there is some wiggle room to find things that are just touching the border of another feature, so there are other functions to find things that are completely contained by another feature.


![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1530129333145_Screen+Shot+2018-06-27+at+3.55.23+PM.png)


[More on](https://postgis.net/docs/ST_Intersects.html) `[ST_Intersects](https://postgis.net/docs/ST_Intersects.html)`

## ST_MakeEnvelope

You can also make an envelope on the fly and find areas that intersect it with `ST_MakeEnvelope`:


    SELECT * 
    FROM accidents
    WHERE accidents.the_geom && ST_MakeEnvelope(-97.6273,32.3811,-96.3858,33.2984, 4326)


![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1530133221121_Screen+Shot+2018-06-27+at+5.00.01+PM.png)

## ST_Within

While very similar to `ST_Intersects`, `ST_Within` will work only if all points or features are **totally** within another feature:


    SELECT 
    accidents.* 
    FROM accidents, us_states 
    WHERE ST_Within(accidents.the_geom, us_states.the_geom) 
    AND us_states.state = 'Minnesota'

[More on](https://postgis.net/docs/ST_Within.html) `[ST_Within](https://postgis.net/docs/ST_Within.html)`


## ST_Disjoint

What about finding things that are not within another feature. You can do so with `ST_Disjoint`:


    SELECT 
    accidents.* 
    FROM accidents, us_states 
    WHERE ST_Disjoint(us_states.the_geom, accidents.the_geom) 
    AND us_states.state = 'Minnesota'

[More on](https://postgis.net/docs/ST_Disjoint.html) `[ST_Disjoint](https://postgis.net/docs/ST_Disjoint.html)`


## ST_Union

What if you want to join several geometries together to make a region, like the Midwest. We can do that with `ST_Union`:


    SELECT 
    row_number() over() as cartodb_id,
    ST_Union(the_geom) as the_geom,
    ST_Union(the_geom_webmercator) as the_geom_webmercator
    FROM us_states 
    WHERE state IN ('Minnesota', 'Wisconsin', 'Iowa', 'Illinois', 'Indiana', 'Ohio', 'Michigan')
![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537216631366_Screen+Shot+2018-09-17+at+4.36.54+PM.png)


[More on](http://www.postgis.net/docs/ST_Union.html) `[ST_Union](http://www.postgis.net/docs/ST_Union.html)`


## ST_Transform

`ST_Transform` allows you to transform your data into different projections. CARTO displays it’s data in WGS 84 / Pseudo-Mercator or [EPSG:3857](https://epsg.io/3857) (i.e. `the_geom`) and PostGIS (or `the_geom_webmercator`) uses WGS84 - World Geodetic System 1984 or [EPSG:4326](https://epsg.io/4326). However there are thousands and thousands of different projection types, each appropriate for different types of maps. You can read more about map projections [here](https://en.wikipedia.org/wiki/Map_projection).

Let’s say that we wanted to reproject our data into the [Robinson Projection](https://epsg.io/54030). We can run this query:


    SELECT 
    cartodb_id,
    ST_Transform(the_geom, 54030) as the_geom,
    ST_Transform(the_geom_webmercator, 54030) as the_geom_webmercator
    FROM us_states

This allows us to reproject the data (but not the base map) so it looks like this:

![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537217221427_Screen+Shot+2018-09-17+at+4.46.48+PM.png)


[More on](https://epsg.io/54030) `[ST_Transform](https://epsg.io/54030)`


## ST_Centroid

What if you want to find the center point, or centroid, of a polygon? You can use `ST_Centroid`:


    SELECT 
    cartodb_id,
    ST_Centroid(the_geom) as the_geom,
    ST_Centroid(the_geom_webmercator) as the_geom_webmercator
    FROM us_states 

Which results in a map that looks like this:


![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537217652342_Screen+Shot+2018-09-17+at+4.53.25+PM.png)


[More on](https://postgis.net/docs/ST_Centroid.html) `[ST_Centroid](https://postgis.net/docs/ST_Centroid.html)`


## ST_Distance

What if you want to find the distance between two points? Use `ST_Distance`:

*We are using a cross join of the same table to illustrate this example and we will use* `*the_geom_webmercator*` *since it returns meters as the unit of measurement.*


    SELECT 
    a.cartodb_id as id_one,
    b.cartodb_id as id_two,
    ST_Distance(a.the_geom_webmercator, b.the_geom_webmercator) / 1609 as distance_miles
    FROM accidents a, accidents b

This returns a data table of distances (in miles since we are multiplying by 1609).

![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537218398452_Screen+Shot+2018-09-17+at+5.05.51+PM.png)


[More on](https://postgis.net/docs/ST_Distance.html) `[ST_Distance](https://postgis.net/docs/ST_Distance.html)`


## ST_DWithin

Let’s say that you want to find the points that are within a specified distance of another geometry, you can use `ST_DWithin` to do that.


    WITH a AS (SELECT cartodb_id as id, the_geom_webmercator FROM accidents LIMIT 1)
    
    SELECT 
    a.id,
    accidents.cartodb_id,
    accidents.the_geom,
    accidents.the_geom_webmercator
    FROM a, accidents
    WHERE ST_DWithin(a.the_geom_webmercator, accidents.the_geom_webmercator, 1609 * 100) IS true

First we will create a subquery that will have a single point in it which we will measure from. Then we will compare the two points in the `WHERE` clause and give the function a distance to measure against (in this case 100 miles which is 1609 meters * 100), and see when that condition is `TRUE`.

We should see a map that looks like this:

![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537286793791_Screen+Shot+2018-09-18+at+12.06.12+PM.png)


[More on](https://postgis.net/docs/ST_DWithin.html) `[ST_DWithin](https://postgis.net/docs/ST_DWithin.html)`


## ST_Length

If you want to find the length of a line, you can use `ST_Line` to find that length (we will use [this table to measure the lines](https://team.carto.com/u/mforrest/dataset/us_railroads)):


    SELECT ST_Length(the_geom_webmercator) / 1609 as length 
    FROM mforrest.us_railroads LIMIT 5

Which will return these results:

![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537289726054_Screen+Shot+2018-09-18+at+12.55.06+PM.png)


[More on](https://postgis.net/docs/ST_Length.html) `[ST_Length](https://postgis.net/docs/ST_Length.html)`


## ST_Area

What if you want to find the area of a polygon? You can use `ST_Area`:


    SELECT 
    state,
    ST_Area(the_geom_webmercator) / 1609 as area
    FROM us_states 
    ORDER BY area DESC

That query will return a table with the areas of each polygon:


![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537289953754_Screen+Shot+2018-09-18+at+12.58.53+PM.png)


[More on](https://postgis.net/docs/ST_Area.html) `[ST_Area](https://postgis.net/docs/ST_Area.html)`


## ST_Overlaps, ST_Contains, ST_Crosses

What if you want to see if two features overlap? `ST_Overlaps` can help us out. The exact definition of `ST_Overlaps` is:


> Returns TRUE if the Geometries "spatially overlap". By that we mean they intersect, but one does not completely contain another

So we can use a where clause to see which areas overlap, but aren’t completely contained by another area such as a bounding box:


    WITH a AS (SELECT ST_MakeEnvelope(-106.48,38.42,-89.55,45.23, 4326) as the_geom)
    
    SELECT
    us_states.cartodb_id,
    us_states.the_geom,
    us_states.the_geom_webmercator,
    ST_Overlaps(a.the_geom, us_states.the_geom)
    FROM a, us_states
    WHERE ST_Overlaps(a.the_geom, us_states.the_geom) IS true

This will return a map that looks like this:


![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537290721678_Screen+Shot+2018-09-18+at+1.11.47+PM.png)


Since Iowa and Nebraska are completely contained by the bounding box, they do not show up in the map.

The opposite is `ST_Contains` which returns values that totally contain the other values:


    WITH a AS (SELECT ST_MakeEnvelope(-106.48,38.42,-89.55,45.23, 4326) as the_geom)
    
    SELECT
    us_states.cartodb_id,
    us_states.the_geom,
    us_states.the_geom_webmercator,
    ST_Contains(a.the_geom, us_states.the_geom)
    FROM a, us_states
    WHERE ST_Contains(a.the_geom, us_states.the_geom) IS true


![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537290860820_Screen+Shot+2018-09-18+at+1.13.30+PM.png)


`ST_Crosses` is another similar operation, but looks for items that cross the border of another geometry:


    WITH a AS (SELECT ST_MakeEnvelope(-106.48,38.42,-89.55,45.23, 4326) as the_geom)
    
    SELECT
    us_states.cartodb_id,
    us_states.the_geom,
    us_states.the_geom_webmercator,
    ST_Contains(a.the_geom, us_states.the_geom)
    FROM a, us_states
    WHERE ST_Contains(a.the_geom, us_states.the_geom) IS true


![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537291027116_Screen+Shot+2018-09-18+at+1.16.18+PM.png)


[More on](https://postgis.net/docs/ST_Overlaps.html) `[ST_Overlaps](https://postgis.net/docs/ST_Overlaps.html)`
[](https://postgis.net/docs/ST_Overlaps.html)[More on](https://postgis.net/docs/ST_Contains.html) `[ST_Contains](https://postgis.net/docs/ST_Contains.html)`
[](https://postgis.net/docs/ST_Contains.html)[More on](https://postgis.net/docs/ST_Crosses.html) `[ST_Crosses](https://postgis.net/docs/ST_Crosses.html)`


## ST_Buffer

If you want to create a buffer around a geometry (such as a point, line or polygon) you can use `ST_Buffer`:


    SELECT 
    cartodb_id,
    ST_Buffer(the_geom, 250 * 1609) as the_geom,
    ST_Buffer(the_geom_webmercator, 250 * 1609) as the_geom_webmercator
    FROM accidents
    LIMIT 1

This will create a 250 mile buffer around a single point:


![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537292815309_Screen+Shot+2018-09-18+at+1.44.32+PM.png)


[More on](https://postgis.net/docs/ST_Buffer.html) `[ST_Buffer](https://postgis.net/docs/ST_Buffer.html)`

## ST_Perimeter

To find the perimiter of a feature, use `ST_Perimeter`:


    SELECT 
    state,
    ST_Perimeter(the_geom_webmercator) / 1609 as perimiter
    FROM us_states
    WHERE state = 'Iowa’

Which will return this result:

![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537294011539_Screen+Shot+2018-09-18+at+2.06.37+PM.png)

## Combining Queries

You can also use multiple functions in one. Let’s take a look at this:


    SELECT 
    row_number() over() as cartodb_id,
    ST_Union(ST_Buffer(the_geom, 50 * 1609)) as the_geom,
    ST_Union(ST_Buffer(the_geom_webmercator, 50 * 1609)) as the_geom_webmercator
    FROM accidents
    WHERE railroad = 'UP'

This will create a 50 mile, unioned buffer around all the accidents caused by `UP`.


![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537293089830_Screen+Shot+2018-09-18+at+1.50.37+PM.png)


You can use subqueries as well:


    WITH a AS (SELECT 
    row_number() over() as cartodb_id,
    ST_Union(ST_Buffer(the_geom, 50 * 1609)) as the_geom,
    ST_Union(ST_Buffer(the_geom_webmercator, 50 * 1609)) as the_geom_webmercator
    FROM accidents
    WHERE railroad = 'UP')
    
    SELECT 
    accidents.*
    FROM accidents, a
    WHERE ST_Intersects(a.the_geom_webmercator, accidents.the_geom_webmercator)
![](https://d2mxuefqeaa7sj.cloudfront.net/s_4E425262DBB316D138719FFCD67F354E68CB9AC775AC7F94CCABF11EEF63D643_1537293819985_Screen+Shot+2018-09-18+at+2.03.16+PM.png)

# Practical PostGIS 

unions of longer data like USAC
use this slide - https://docs.google.com/presentation/d/1KB3rp33xSGDDpcyWJyjz-j23_bkQE4aisl7DoffV--Y/edit#slide=id.g2d4058dbfe_1_190
counts and geom grouping

Do this in the context of site selection problems

# CARTO Operations
- Geocoding
- Routing
- Isolines
- Crankshaft
- Grid & Hexbin


