Count affiliations per university
Now that your data is ready for analysis, let's run some example SQL queries on the database. You'll now use already known concepts such as grouping by columns and joining tables.

In this exercise, you will find out which university has the most affiliations (through its professors). For that, you need both affiliations and professors tables, as the latter also holds the university_id.

As a quick repetition, remember that joins have the following structure:

SELECT table_a.column1, table_a.column2, table_b.column1, ...
FROM table_a
JOIN table_b
ON table_a.column = table_b.column
This results in a combination of table_a and table_b, but only with rows where table_a.column is equal to table_b.column.

INSTRUCTIONS:
Count the number of total affiliations by university.
Sort the result by that count, in descending order.
-- Count the total number of affiliations per university
SELECT COUNT(*), professors.university_id
FROM affiliations
JOIN professors
ON affiliations.professor_id = professors.id
-- Group by the university ids of professors
GROUP BY professors.university_id
ORDER BY count DESC;

===============================
Join all the tables together
In this last exercise, your task is to find the university city of the professor with the most affiliations in the sector "Media & communication".

For this,

you need to join all the tables,
group by some column,
and then use selection criteria to get only the rows in the correct sector.
Let's do this in three steps!
=========================
Join all tables in the database (starting with affiliations, professors, organizations, and universities) and look at the result.
-- Join all tables
SELECT *
FROM affiliations
JOIN professors
ON affiliations.professor_id = professors.id
JOIN organizations
ON affiliations.organization_id = organizations.id
JOIN universities
ON professors.university_id = universities.id;

Now group the result by organization sector, professor, and university city.
Count the resulting number of rows.

-- Group the table by organization sector, professor ID and university city
SELECT COUNT(*), organizations.organization_sector,
professors.id, universities.university_city
FROM affiliations
JOIN professors
ON affiliations.professor_id = professors.id
JOIN organizations
ON affiliations.organization_id = organizations.id
JOIN universities
ON professors.university_id = universities.id
GROUP BY organizations.organization_sector,
professors.id, universities.university_city;

Only retain rows with "Media & communication" as organization sector, and sort the table by count, in descending order.
-- Filter the table and sort it
SELECT COUNT(*), organizations.organization_sector,
professors.id, universities.university_city
FROM affiliations
JOIN professors
ON affiliations.professor_id = professors.id
JOIN organizations
ON affiliations.organization_id = organizations.id
JOIN universities
ON professors.university_id = universities.id
WHERE organizations.organization_sector = 'Media & communication'
GROUP BY organizations.organization_sector,
professors.id, universities.university_city
ORDER BY COUNT DESC;
