--Introduction to Relational Database IN SQL
Query information_schema with SELECT
information_schema is a meta-database that holds information about your current database.
information_schema has multiple tables you can query with the known SELECT * FROM syntax:

tables: information about all tables in your current database
columns: information about all columns in all of the tables in your current database
…
In this exercise, you'll only need information from the 'public' schema,
which is specified as the column table_schema of the tables and columns tables.
The 'public' schema holds information about user-defined tables and databases.
The other types of table_schema hold system information
– for this course, you're only interested in user-defined stuff.

--Get information on all table names in the current database, while limiting your query to the 'public' table_schema.
-- Query the right table in information_schema
SELECT table_name
FROM information_schema.tables

-- Specify the correct table_schema value
WHERE table_schema = 'public';


--Now have a look at the columns in university_professors by selecting all entries in information_schema.columns
that correspond to that table.
-- Query the right table in information_schema to get columns
SELECT column_name, data_type
FROM information_schema.columns
WHERE table_name = 'university_professors' AND table_schema = 'public';


--Create a table professors with two text columns: firstname and lastname.
--Create a table universities with three text columns: university_shortname, university, and university_city.
-- Create a table for the professors entity type
CREATE TABLE professors (
 firstname text,
 lastname text
);

-- Print the contents of this table
SELECT *
FROM professors;

--Universities table
CREATE TABLE universities(
    university_shortname text,
    university text,
    university_city text
);

--print the contents of this table

SELECT *
FROM universities;

--Alter professors to add the text column university_shortname.
-- Add the university_shortname column
ALTER TABLE professors
ADD university_shortname text;

-- Print the contents of this table
SELECT *
FROM professors;

--Rename the organisation column to organization in affiliations.
-- Rename the organisation column
ALTER TABLE affiliations
RENAME COLUMN organisation TO organization;

-- Delete the university_shortname column
ALTER TABLE affiliations
DROP COLUMN university_shortname;

--Insert all DISTINCT professors from university_professors into professors.
--Print all the rows in professors.

-- Insert unique professors into the new table
INSERT INTO professors
SELECT DISTINCT firstname, lastname, university_shortname
FROM university_professors;

-- Doublecheck the contents of professors
SELECT *
FROM professors;

--Insert all DISTINCT affiliations into affiliations from university_professors
-- Insert unique affiliations into the new table
INSERT INTO affiliations
SELECT DISTINCT firstname, lastname, function, organization
FROM university_professors;

-- Doublecheck the contents of affiliations
SELECT *
FROM affiliations;

-- Delete the university_professors table
DROP TABLE university_professors;

==========Conforming with data types======================
--Execute the given sample code.As it doesn't work, have a look at the error message and
--correct the statement accordingly – then execute it again.
-- Let's add a record to the table
INSERT INTO transactions (transaction_date, amount, fee)
VALUES ('2018-09-24', 5454, '30');

-- Doublecheck the contents
SELECT *
FROM transactions;

-- Calculate the net amount as amount + fee
SELECT transaction_date, CAST(amount AS integer) + CAST( fee AS integer) AS net_amount
FROM transactions;

Change types with ALTER COLUMN
The syntax for changing the data type of a column is straightforward. The following code changes the data type of the column_name column in table_name to varchar(10):

ALTER TABLE table_name
ALTER COLUMN column_name
TYPE varchar(10)
Now it's time to start adding constraints to your database.

--Have a look at the distinct university_shortname values in the professors table and take note of the length of the strings.
-- Select the university_shortname column
SELECT DISTINCT university_shortname
FROM professors;

--Now specify a fixed-length character type with the correct length for university_shortname.
-- Specify the correct fixed-length character type
ALTER TABLE professors
ALTER COLUMN university_shortname
TYPE  CHAR(3);

-- Change the type of firstname
ALTER TABLE professors
ALTER COLUMN firstname
TYPE varchar(64);

--Convert types USING a function
--If you don't want to reserve too much space for a certain varchar column, you can truncate the values before converting its type.

--For this, you can use the following syntax:

--ALTER TABLE table_name
--ALTER COLUMN column_name
--TYPE varchar(x)
--USING SUBSTRING(column_name FROM 1 FOR x)
--You should read it like this: Because you want to reserve only x --characters for column_name, you have to retain a SUBSTRING of every --value, i.e. the first x characters of it, and throw away the rest. This --way, the values will fit the varchar(x) requirement.
--Run the sample code as is and take note of the error.
--Now use SUBSTRING() to reduce firstname to 16 characters so its type can --be altered to varchar(16).

-- Convert the values in firstname to a max. of 16 characters
ALTER TABLE professors
ALTER COLUMN firstname
TYPE varchar(16)
--if below line is skipped it throws an error
--value too long for type character varying(16)
USING SUBSTRING( firstname FROM 1 FOR 16);

--Disallow NULL values with SET NOT NULL
The professors table is almost ready now. However, it still allows for NULLs to be entered. Although some information might be missing about some professors, there's certainly columns that always need to be specified.

--Add a not-null constraint for the firstname column.
-- Disallow NULL values in firstname
ALTER TABLE professors
ALTER COLUMN firstname
SET NOT NULL;

--Add a not-null constraint for the lastname column.
-- Disallow NULL values in lastname
ALTER TABLE professors
ALTER COLUMN lastname
SET NOT NULL;

--Make your columns UNIQUE with ADD CONSTRAINT
As seen in the video, you add the UNIQUE keyword after the column_name that should be unique. This, of course, only works for new tables:

CREATE TABLE table_name (
 column_name UNIQUE
);
If you want to add a unique constraint to an existing table, you do it like that:

ALTER TABLE table_name
ADD CONSTRAINT some_name UNIQUE(column_name);
Note that this is different from the ALTER COLUMN syntax for the not-null constraint. Also, you have to give the constraint a name some_name.

--Add a unique constraint to the university_shortname column in universities. Give it the name university_shortname_unq.

-- Make universities.university_shortname unique
ALTER TABLE universities
ADD CONSTRAINT university_shortname_unq UNIQUE(university_shortname);

-- Make organizations.organization unique
ALTER TABLE organizations
ADD CONSTRAINT organization_unq UNIQUE(organization);

================KEYS AND SUPERKEYS===================
Get to know SELECT COUNT DISTINCT
Your database doesn't have any defined keys so far, and you don't know which columns or combinations of columns are suited as keys.

There's a simple way of finding out whether a certain column (or a combination) contains only unique values – and thus identifies the records in the table.

You already know the SELECT DISTINCT query from the first chapter. Now you just have to wrap everything within the COUNT() function and PostgreSQL will return the number of unique rows for the given columns:

SELECT COUNT(DISTINCT(column_a, column_b, ...))
FROM table;

==============================================
-- Count the number of rows in universities
SELECT COUNT(*)
FROM universities;
--Then, find out how many unique values there are in the university_city column.
-- Count the number of distinct values in the university_city column
SELECT COUNT(DISTINCT(university_city))
FROM universities;

--Identify keys with SELECT COUNT DISTINCT
There's a very basic way of finding out what qualifies for a key in an existing, populated table:

Count the distinct records for all possible combinations of columns. If the resulting number x equals the number of all rows in the table for a combination, you have discovered a superkey.

Then remove one column after another until you can no longer remove columns without seeing the number x decrease. If that is the case, you have discovered a (candidate) key.

The table professors has 551 rows. It has only one possible candidate key, which is a combination of two attributes. You might want to try different combinations using the "Run code" button. Once you have found the solution, you can submit your answer.
===============
-- Try out different combinations
SELECT COUNT(DISTINCT(lastname,firstname))
FROM professors;

==============
ADD key CONSTRAINTs to the tables
Two of the tables in your database already have well-suited candidate keys consisting of one column each: organizations and universities with the organization and university_shortname columns, respectively.

In this exercise, you'll rename these columns to id using the RENAME COLUMN command and then specify primary key constraints for them. This is as straightforward as adding unique constraints (see the last exercise of Chapter 2):

ALTER TABLE table_name
ADD CONSTRAINT some_name PRIMARY KEY (column_name)
Note that you can also specify more than one column in the brackets.
=============
Rename the organization column to id in organizations.
Make id a primary key and name it organization_pk.

-- Rename the organization column to id
ALTER TABLE organizations
RENAME COLUMN organization TO id;

-- Make id a primary key
ALTER TABLE organizations
ADD CONSTRAINT organization_pk PRIMARY KEY (id);

Rename the university_shortname column to id in universities.
Make id a primary key and name it university_pk.

-- Rename the university_shortname column to id
ALTER TABLE universities
RENAME COLUMN university_shortname TO id;

-- Make id a primary key
ALTER TABLE universities
ADD CONSTRAINT university_pk PRIMARY KEY(id);


========SURROGATE KEYS AND FOREIGN KEYS========================
Add a SERIAL surrogate key
Since there's no single column candidate key in professors (only a composite key candidate consisting of firstname, lastname), you'll add a new column id to that table.

This column has a special data type serial, which turns the column into an auto-incrementing number. This means that, whenever you add a new professor to the table, it will automatically get an id that does not exist yet in the table: a perfect primary key!

==========
Add a new column id with data type serial to the professors table.

-- Add the new column to the table
ALTER TABLE professors
ADD COLUMN id serial;

-- Make id a primary key
ALTER TABLE professors
ADD CONSTRAINT professors_pkey  PRIMARY KEY(id) ;

Write a query that returns all the columns and 10 rows from professors.

-- Have a look at the first 10 rows of professors
SELECT * FROM professors LIMIT 10;


=================
CONCATenate columns to a surrogate key
Another strategy to add a surrogate key to an existing table is to concatenate existing columns with the CONCAT() function.

Let's think of the following example table:

CREATE TABLE cars (
 make varchar(64) NOT NULL,
 model varchar(64) NOT NULL,
 mpg integer NOT NULL
)
The table is populated with 10 rows of completely fictional data.

Unfortunately, the table doesn't have a primary key yet. None of the columns consists of only unique values, so some columns can be combined to form a key.

In the course of the following exercises, you will combine make and model into such a surrogate key.

==================
Count the number of distinct rows with a combination of the make and model columns.
-- Count the number of distinct rows with columns make, model
SELECT COUNT(DISTINCT(make, model))
FROM cars;

Add a new column id with the data type varchar(128).
-- Add the id column
ALTER TABLE cars
ADD COLUMN id VARCHAR(64);

Concatenate make and model into id using an UPDATE table_name SET column_name = ... query and the CONCAT() function.
-- Update id with make + model
UPDATE cars
SET id = CONCAT(make, model);

Make id a primary key and name it id_pk.
-- Make id a primary key
ALTER TABLE cars
ADD CONSTRAINT id_pk PRIMARY KEY(id);

-- Have a look at the table
SELECT * FROM cars;
================
Test your knowledge before advancing
Before you move on to the next chapter, let's quickly review what you've learned so far about attributes and key constraints. If you're unsure about the answer, please quickly review chapters 2 and 3, respectively.

Let's think of an entity type "student". A student has:

a last name consisting of up to 128 characters (required),
a unique social security number, consisting only of integers, that should serve as a key,
a phone number of fixed length 12, consisting of numbers and characters (but some students don't have one).
================
Given the above description of a student entity, create a table students with the correct column types.
Add a PRIMARY KEY for the social security number ssn.
Note that there is no formal length requirement for the integer column. The application would have to make sure it's a correct SSN!

-- Create the table
CREATE TABLE students (
  last_name varchar(128) NOT NULL,
  ssn int PRIMARY KEY,
  phone_no char(12)

);
================Foreign keys and relationships===========
REFERENCE a table with a FOREIGN KEY
In your database, you want the professors table to reference the universities table. You can do that by specifying a column in professors table that references a column in the universities table.

As just shown in the video, the syntax for that looks like this:

ALTER TABLE a
ADD CONSTRAINT a_fkey FOREIGN KEY (b_id) REFERENCES b (id);
Table a should now refer to table b, via b_id, which points to id. a_fkey is, as usual, a constraint name you can choose on your own.

Pay attention to the naming convention employed here: Usually, a foreign key referencing another primary key with name id is named x_id, where x is the name of the referencing table in the singular form.
=========================
Rename the university_shortname column to university_id in professors.
-- Rename the university_shortname column
ALTER TABLE professors
RENAME COLUMN university_shortname TO university_id;

-- Rename the university_shortname column
ALTER TABLE professors
RENAME COLUMN university_shortname TO university_id;

-- Add a foreign key on professors referencing universities
ALTER TABLE professors
ADD CONSTRAINT professors_fkey FOREIGN KEY (university_id) REFERENCES universities (id);

============
Explore foreign key constraints
Foreign key constraints help you to keep order in your database mini-world. In your database, for instance, only professors belonging to Swiss universities should be allowed, as only Swiss universities are part of the universities table.

The foreign key on professors referencing universities you just created thus makes sure that only existing universities can be specified when inserting new data. Let's test this!
=============
Run the sample code and have a look at the error message.
"insert or update on table "professors" violates foreign key constraint "professors_fkey"
DETAIL:  Key (university_id)=(MIT) is not present in table "universities"."

What's wrong? Correct the university_id so that it actually reflects where Albert Einstein wrote his dissertation and became a professor – at the University of Zurich (UZH)!
-- Try to insert a new professor
INSERT INTO professors (firstname, lastname, university_id)
--MIT changed to UZH
VALUES ('Albert', 'Einstein', 'UZH');
===================================
JOIN tables linked by a foreign key
Let's join these two tables to analyze the data further!

You might already know how SQL joins work from the Intro to SQL for Data Science course (last exercise) or from Joining Data in PostgreSQL.

Here's a quick recap on how joins generally work:

SELECT ...
FROM table_a
JOIN table_b
ON ...
WHERE ...
While foreign keys and primary keys are not strictly necessary for join queries, they greatly help by telling you what to expect. For instance, you can be sure that records referenced from table A will always be present in table B – so a join from table A will always find something in table B. If not, the foreign key constraint would be violated.
===================================
JOIN professors with universities on professors.university_id = universities.id, i.e., retain all records where the foreign key of professors is equal to the primary key of universities.
Filter for university_city = 'Zurich'.

-- Select all professors working for universities in the city of Zurich
SELECT professors.lastname, universities.id, universities.university_city
FROM professors
INNER JOIN universities
ON professors.university_id = universities.id
WHERE universities.university_city = 'Zurich';
=================
Add foreign keys to the "affiliations" table
At the moment, the affiliations table has the structure {firstname, lastname, function, organization}, as you can see in the preview at the bottom right. In the next three exercises, you're going to turn this table into the form {professor_id, organization_id, function}, with professor_id and organization_id being foreign keys that point to the respective tables.

You're going to transform the affiliations table in-place, i.e., without creating a temporary table to cache your intermediate results.
===================
Add a professor_id column with integer data type to affiliations, and declare it to be a foreign key that references the id column in professors.
-- Add a professor_id column
ALTER TABLE affiliations
ADD COLUMN professor_id integer REFERENCES professors (id);

Rename the organization column in affiliations to organization_id.

-- Rename the organization column to organization_id
ALTER TABLE affiliations
RENAME COLUMN organization TO organization_id;

Add a foreign key constraint on organization_id so that it references the id column in organizations.

-- Add a foreign key on organization_id
ALTER TABLE affiliations
ADD CONSTRAINT affiliations_organization_fkey FOREIGN KEY (organization_id) REFERENCES organizations (id);

===================
Populate the "professor_id" column
Now it's time to also populate professors_id. You'll take the ID directly from professors.

Here's a way to update columns of a table based on values in another table:

UPDATE table_a
SET column_to_update = table_b.column_to_update_from
FROM table_b
WHERE condition1 AND condition2 AND ...;
This query does the following:

For each row in table_a, find the corresponding row in table_b where condition1, condition2, etc., are met.
Set the value of column_to_update to the value of column_to_update_from (from that corresponding row).
The conditions usually compare other columns of both tables, e.g. table_a.some_column = table_b.some_column. Of course, this query only makes sense if there is only one matching row in table_b.
===================
First, have a look at the current state of affiliations by fetching 10 rows and all columns.
SELECT * FROM affiliations LIMIT 10;

Update the professor_id column with the corresponding value of the id column in professors.
"Corresponding" means rows in professors where the firstname and lastname are identical to the ones in affiliations.
-- Set professor_id to professors.id where firstname, lastname correspond to rows in professors
UPDATE affiliations
SET professor_id = professors.id
FROM professors
WHERE affiliations.firstname = professors.firstname AND affiliations.lastname = professors.lastname;

Check out the first 10 rows and all columns of affiliations again. Have the professor_ids been correctly matched?
-- Have a look at the 10 first rows of affiliations again
SELECT * FROM affiliations LIMIT 10;
=========
Drop "firstname" and "lastname"
The firstname and lastname columns of affiliations were used to establish a link to the professors table in the last exercise – so the appropriate professor IDs could be copied over. This only worked because there is exactly one corresponding professor for each row in affiliations. In other words: {firstname, lastname} is a candidate key of professors – a unique combination of columns.

It isn't one in affiliations though, because, as said in the video, professors can have more than one affiliation.

Because professors are referenced by professor_id now, the firstname and lastname columns are no longer needed, so it's time to drop them. After all, one of the goals of a database is to reduce redundancy where possible.
============
Drop the firstname and lastname columns from the affiliations table.
-- Drop the firstname column
ALTER TABLE affiliations
DROP COLUMN firstname;

-- Drop the lastname column
ALTER TABLE affiliations
DROP COLUMN lastname;

=================
Referential integrity violations
Given the current state of your database, what happens if you execute the following SQL statement?

DELETE FROM universities WHERE id = 'EPF';

MESSAGE:update or delete on table "universities" violates foreign key constraint "professors_fkey" on table "professors"
DETAIL:  Key (id)=(EPF) is still referenced from table "professors".

ANSWER:It fails because referential integrity from professors to universities is violated.
==========
Change the referential integrity behavior of a key
So far, you implemented three foreign key constraints:

professors.university_id to universities.id
affiliations.organization_id to organizations.id
affiliations.professor_id to professors.id
These foreign keys currently have the behavior ON DELETE NO ACTION. Here, you're going to change that behavior for the column referencing organizations from affiliations. If an organization is deleted, all its affiliations (by any professor) should also be deleted.

Altering a key constraint doesn't work with ALTER COLUMN. Instead, you have to DROP the key constraint and then ADD a new one with a different ON DELETE behavior.

For deleting constraints, though, you need to know their name. This information is also stored in information_schema.
==========
Have a look at the existing foreign key constraints by querying table_constraints in information_schema.
-- Identify the correct constraint name
SELECT constraint_name, table_name, constraint_type
FROM information_schema.table_constraints
WHERE constraint_type = 'FOREIGN KEY';

constraint_name	table_name	constraint_type
affiliations_organization_id_fkey	affiliations	FOREIGN KEY
affiliations_professor_id_fkey	affiliations	FOREIGN KEY
professors_fkey	professors	FOREIGN KEY

Delete the affiliations_organization_id_fkey foreign key constraint in affiliations.
-- Drop the right foreign key constraint
ALTER TABLE affiliations
DROP CONSTRAINT affiliations_organization_id_fkey;

Add a new foreign key to affiliations that CASCADEs deletion if a referenced record is deleted from organizations. Name it affiliations_organization_id_fkey.
-- Add a new foreign key constraint from affiliations to organizations which cascades deletion
ALTER TABLE affiliations
ADD CONSTRAINT affiliations_organization_id_fkey FOREIGN KEY (organization_id) REFERENCES organizations (id) ON DELETE CASCADE;

Run the DELETE and SELECT queries to double check that the deletion cascade actually works.
--- Delete an organization
DELETE FROM organizations
WHERE id = 'CUREM';

-- Check that no more affiliations with this organization exist
SELECT * FROM affiliations
WHERE organization_id = 'CUREM';
