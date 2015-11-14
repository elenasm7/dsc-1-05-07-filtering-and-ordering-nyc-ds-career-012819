# Basic SQL Queries

## Overview

In this lesson, we'll cover how to write SQL queries to retrieve and add specific data to SQL database tables.

## Objectives

1. Define a query as an SQL statement that retrieves data from a database
2. Use the `ORDER BY`modifier to order tables by specific `SELECT` statements
3. Use the `ASC` and `DESC` modifier to sort queries in ascending and descending orders
4. Use the `LIMIT` modifier to determine the number of records to retrieve from a dataset
5. Use the `BETWEEN` modifier to retrieve a specific data set between two ranges
6. Use the `NULL` data type keyword to insert new records into a table
7. Use the `COUNT` function to  count the number of records that meet a certain condition

## What is a SQL Query?

The term "query" refers to any SQL statement that retrieve data from your database. In fact, we've already written a number of SQL queries using basic `SELECT` statements. We've already seen how to retrieve single units of data, or rows, with queries like these: 

To select all of the rows from a `cats` table:

```sql
SELECT * FROM cats;
```

To select only rows representing data meeting certain conditions: 

```sql
SELECT * FROM cats WHERE name = "Maru";
```

What if, however, we wanted to select the oldest cat? Or all of the cats that don't currently belong to an owner? Or all of the cats with short names? 

Data storage isn't very useful if we can't manipulate, view, and analyze that data. Luckily for us, SQL is actually a powerful tool for doing just that. 

In this exercise, we'll walk through executing a handful of common and handy SQL queries. If you want to take a deeper dive, however, feel free to check out the resources at the bottom of the page. 

## Code Along: SQL Queries

### Creating our Database

In this code along, we'll be creating a `cats` table in a `pets_database.db`. So, let's navigate to our terminal and get started.

First let's create our `pets_database` by running the following command.

```bash
sqlite3 pets_database.db
```

Good work. Let's type `ls` in the terminal and see what just happened. A new file should appear called `pets_database.db`! This is the binary representation of the database. You can think of this like a .jpg file. It won't open up in a text editor, but it does open up in the image viewer app. It is the same way for .db files. They won't open in your editor, but they can be read by the appropriate database engine.

Now that we have a database, let's create our `cats` table along with `id`, `name`, `age`, `breed`, and `owner_id` columns.

```sql
	CREATE TABLE cats (
	 	id INTEGER PRIMARY KEY,
	 	name TEXT,
	 	age INTEGER,
	 	breed TEXT,
	 	owner_id INTEGER
	);
```

Let's add some cats to our `cats` table to make this more interesting: 

```sql
sqlite> INSERT INTO cats (name, age, breed, owner_id) VALUES ("Maru", 3 , "Scottish Fold", 1);
sqlite> INSERT INTO cats (name, age, breed, owner_id) VALUES ("Hana", 1 , "Tabby", 1);
sqlite> INSERT INTO cats (name, age, breed) VALUES ("Lil' Bub", 5, "American Shorthair");
sqlite> INSERT INTO cats (name, age, breed) VALUES ("Moe", 10, "Tabby");
sqlite> INSERT INTO cats (name, age, breed) VALUES ("Patches", 2, "Calico");
```

Let's checkout our `cats` table now: 

```sql
sqlite> SELECT * FROM cats;
```

This should return: 

```bash
1|Maru|3|Scottish Fold|1
2|Hana|1|Tabby|1
3|Lil' Bub|5|American Shorthair|
4|Moe|10|Tabby|
5|Patches|2|Calico|
```

**Top-Tip:** You can format the output of your select statements with a few helpful options: 

```sql
.header on       # output the name of each column
.mode column     # now we are in column mode, enabling us to run the next two .width commands
.width auto      # adjusts and normalizes column width
# or
.width NUM1, NUM2 # customize column width
```

Run the first two commands and then execute the above `SELECT` statement instead and you should see something like this: 

```bash
id  name        age    breed          owner_id  
--  ----------  -----  -------------  ----------
1   Maru        3      Scottish Fold  1         
2   Hana        1      Tabby          1         
3   Lil' Bub    5      American Shor            
4   Moe         10     Tabby                    
5   Patches     2      Calico     
```

Much better. 


### `ORDER BY`

The first query modifier we'll explore is `ORDER BY`. This modifier allows us to order the table rows returned by a certain `SELECT` statement. Here's a boilerplate `SELECT` statement that uses `ORDER BY`:

```sql
SELECT column_name FROM table_name ORDER BY column_name ASC|DESC;
```

Let's select our cats and order them by age: 

```sql
sqlite> SELECT * FROM cats ORDER BY age;
```

This should return the following: 

```bash
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
2   Hana        1      Tabby       1         
5   Patches     2      Calico                
1   Maru        3      Scottish F  1         
3   Lil' Bub    5      American S            
4   Moe         10     Tabby  
```
When using `ORDER BY`, the default is to order in ascending order. If we want to specify though, we can use `ASC` for "ascending" or `DESC` for "descending." Let's try to select all of our cats and sort them by age in descending order. 

```sql
sqlite> SELECT * FROM cats ORDER BY age DESC;
```

This should return

```bash
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
4   Moe         10     Tabby                 
3   Lil' Bub    5      American S            
1   Maru        3      Scottish F  1         
5   Patches     2      Calico                
2   Hana        1      Tabby       1 
```   

### `LIMIT`

What if we want the oldest cat? If we want to select extremes from a database table––for example, the employee with the highest paycheck or the patient with the most recent appointment––we can use `ORDER BY` in conjunction with `LIMIT`. 

`LIMIT` is used to determine the number of records you want to return from a dataset. For example: 

```sql
SELECT * FROM cats ORDER BY age DESC LIMIT 1;
```

This part of the statement: `SELECT * FROM cats ORDER BY age DESC LIMIT 1;` returns all of the cats in order from oldest to youngest. Setting a `LIMIT` of `1` returns just the first, i.e. oldest, cat on the list. 

Execute the above statement in your terminal and you should see: 

```bash
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
4   Moe         10     Tabby                 
```
Let's get the two oldest cats: 

```sql
SELECT * FROM cats ORDER BY age DESC LIMIT 2;
```

Execute that statement and you should see: 

```bash
id  name        age    breed       owner_id  
--  ----------  -----  ----------  ----------
4   Moe         10     Tabby                 
3   Lil' Bub    5      American S            
```

### `BETWEEN`

As we've already established, being able to sort and select specific data sets is important. Continuing on with our example, let's say we urgently need to select all of the cats whose age is between 1 and 3. To create such a query, we can use `BETWEEN`. Here's an boilerplate `SELECT` statement using `BETWEEN`:

```sql
SELECT column_name(s) FROM table_name WHERE column_name BETWEEN value1 AND value2;
```

Let's try it out on our `cats` table:

```sql
SELECT name FROM cats WHERE age BETWEEN 1 AND 3;
```
This should return:

```bash
Maru
Hana
Patches
```

### NULL

Let's say the adminstrator of our Pets Database has found a new cat. This kitty doesn't have a name yet, but should be added to our database right away. We can add data with missing values using the `NULL` keyword. 

Let's insert our new cat into the database. Our abandoned kitty has a breed, but no name or age as of yet: 

```sql
INSERT INTO cats (name, age, breed) VALUES (NULL, NULL, "Tabby");
```

Now, if we look at our `cats` data with `SELECT * FROM cats;`, we should see:

```bash
id          name        age         breed          owner_id  
----------  ----------  ----------  -------------  ----------
1           Maru        3           Scottish Fold  1         
2           Hana        1           Tabby          1         
3           Lil' Bub    5           American Shor            
4           Moe         10          Tabby                    
5           Patches     2           Calico                   
6                                   Tabby            
```

We can even select the mysterious, nameless kitty with the following query:

```sql
SELECT * FROM cats WHERE name IS NULL;
```
This should return the following: 

```bash
id          name        age         breed       owner_id  
----------  ----------  ----------  ----------  ----------
6                                   Tabby      
```

### `COUNT`

Lastly, we'll talk about one SQL aggregate function, `COUNT`. 

**SQL aggregate functions** are SQL statements that retrieve minimum and maximum values from a column, sum values in a column, get the average of a column's values, or count a number of records that meet a certain conditions. You can learn more about the these SQL aggregators [here](http://www.sqlclauses.com/sql+aggregate+functions) and [here](http://zetcode.com/db/sqlite/select/). 

For now, we'll just focus on `COUNT`. `COUNT` will count the number of records that meet a certain condition. Here's a standard SQL query using `COUNT`:

```sql
 "SELECT COUNT([column name]) FROM [table name] WHERE [column name] = [value]"
```
Let's try it out and count the number of cats who have an `owner_id` of `1`:

```sql
SELECT COUNT(owner_id) FROM cats WHERE owner_id = 1;
```
This should return:

```bash
COUNT(owner_id)
---------------
2
``` 

###Note on `SELECT`

We are now familiar with this syntax:

```sql
SELECT name FROM cats;
```

However, you may not know that this can be written like this as well:

```sql
SELECT cats.name FROM cats;
```

Both return:

```bash
name      
----------
Maru      
Hana
Lil' Bub
Moe
Patches    
```

SQLite allows us to explicitly state the tableName.columnName we want to select. This is particularly useful when we want data from two different tables.

Imagine we have another table called `dogs` with a column for the dog names:

```sql
	CREATE TABLE dogs (
      id INTEGER PRIMARY KEY,
      name TEXT,
   );
```

```sql
	sqlite> INSERT INTO dogs (name) VALUES ("Clifford");
```


If we want to get the names of all the dogs and cats, we can no longer run a query with just the column name.
`SELECT name FROM cats,dogs;` will return `Error: ambiguous column name: name`.

Instead, we must explicitly follow the tableName.columnName syntax.
```sql
SELECT cats.name, dogs.name FROM cats, dogs;
```

You may see this in the future. Don't let it trip you up.
