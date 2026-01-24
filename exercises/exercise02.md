# Exercise 02: World Database – Joins, Grouping, and Data Quality

- Name: Terry Konkin
- Course: Database for Analytics
- Module: 2
- Database Used: World Database (PostgreSQL)

---

## Instructions

- Answer each question below using SQL executed against the **World database**.
- All SQL commands **must be run by you**.
- For each SQL-based question:
  - Include the SQL command in a fenced code block
  - Include a **screenshot** showing the command and its results
- Store screenshots in the `screenshots/` folder and embed them below each answer.

---

## Question 1

When importing records from `worldPGSQL.sql`, **how many cities were imported**?

### Answer
_4001_

### Screenshot
_Show evidence of how you determined this (for example, a COUNT query)._

```sql
SELECT COUNT(DISTINCT name) FROM city;
```

![Q1 Screenshot](screenshots/q1_city_count.png)

---

## Question 2

Using the World database, write the SQL command to **display each country name along with the name of each language spoken in that country**.

### SQL

```sql
SELECT country.name AS country,
       countrylanguage.language AS language
FROM country
JOIN countrylanguage
      ON country.code = countrylanguage.countrycode
ORDER BY country.name, countrylanguage.language;
```

### Screenshot

![Q2 Screenshot](screenshots/q2_country_languages.png)

---

## Question 3

Using the World database, write the SQL command to **display each country name along with the name of each official language spoken in that country**.

### SQL

```sql
SELECT country.name AS country,
       countrylanguage.language AS officiallanguage
FROM country
JOIN countrylanguage
      ON country.code = countrylanguage.countrycode
WHERE countrylanguage.isofficial = 'T'
ORDER BY country.name, countrylanguage.language;
```

### Screenshot

![Q3 Screenshot](screenshots/q3_official_languages.png)

---

## Question 4

Consider the following two SQL statements:

```sql
SELECT *
FROM country, countrylanguage
WHERE country.code = countrylanguage.countrycode;
```

```sql
SELECT *
FROM country
LEFT OUTER JOIN countrylanguage
ON country.code = countrylanguage.countrycode;
```

**In your own words**, describe what data the **second query returns that the first query does not**.

### Answer
_The first statement returns only those rows where there is a matching countrycode in both the country and countrylanguage tables.

As a left outer join, the second statement returns all rows from the left (in this case, country) table, and matching rows from the right (countrylanguage) table.

Therefore, the additional info in the second query is those rows from country that do not have a matching code in countrylanguage.

---

## Question 5

Using the World database, write the SQL command to **list all different forms of government** found in the data.
Do **not** repeat any form of government more than once.

### SQL

```sql
SELECT DISTINCT governmentform
FROM country;
```

### Screenshot

![Q5 Screenshot](screenshots/q5b_government_forms.png)

---

## Question 6

Using the World database, write the SQL command to **list all names of cities and countries in one column**.
Label the column **"City or Country Name"**.

### SQL

```sql
SELECT name AS "City or Country Name"
FROM city
UNION
SELECT name AS "City or Country Name"
FROM country;
```

### Screenshot

![Q6 Screenshot](screenshots/q6_union_city_country.png)

---

## Question 7

Using the World database, write the SQL command to **list all countries by name**, along with the **number of languages spoken in each country**.
Be sure to **sort by country name**.

### SQL

```sql
SELECT country.name AS countryname,
       COUNT(countrylanguage.language) AS numberoflanguages
FROM country
LEFT JOIN countrylanguage
       ON country.code = countrylanguage.countrycode
GROUP BY country.name
ORDER BY country.name;
```

### Screenshot

![Q7 Screenshot](screenshots/q7_language_count_by_country.png)

---

## Question 8

Using the World database, write the SQL command to **list all languages**, along with the **number of countries where each language is spoken**.
Be sure to **sort by language name**.

### SQL

```sql
SELECT countrylanguage.Language,
       COUNT(DISTINCT countrylanguage.countrycode) AS numberofcountries
FROM countrylanguage
GROUP BY countrylanguage.language
ORDER BY countrylanguage.language;
```

### Screenshot

![Q8 Screenshot](screenshots/q8_language_country_count.png)

---

## Question 9

Using the World database, write the SQL command to **list countries that have more than two official languages**, along with the **number of official languages spoken**.

*Hint: There are 8 such countries in this dataset.*

### SQL

```sql
SELECT country.name AS country,
       COUNT(*) AS numberofofficiallanguages
FROM country
JOIN countrylanguage
      ON country.code = countrylanguage.countrycode
WHERE countrylanguage.isofficial = 'T'
GROUP BY country.name
HAVING COUNT(*) > 2
ORDER BY country.name;
```

### Screenshot

![Q9 Screenshot](screenshots/q9_multiple_official_languages.png)

---

## Question 10

Using the World database, write the SQL command to **find cities where the district value is missing**.

*Hint: Use `LIKE` and the dash (`-') since some rows use that instead of actual data.*

### SQL

```sql
SELECT Name, District
FROM city
WHERE District IS NULL
	OR District LIKE '–'
	OR TRIM(TRANSLATE(District, '–', '')) = '';
```

### Screenshot

![Q10 Screenshot](screenshots/q10_missing_districts.png)

---

## Question 11

Using the World database, write the SQL command to **calculate the percentage of cities with missing district values**.

*Hint: The result should be approximately 0.4%.*

### SQL

```sql
SELECT 
      (COUNT(CASE 
            WHEN District IS NULL 
            OR TRIM(District) LIKE '-%'
    	      OR TRIM(District) LIKE '–%' 
            THEN 1 
            END) * 100.0 
      / COUNT(*)
      ) AS PercentageMissingDistrict
      FROM city;
```

### Screenshot

![Q11 Screenshot](screenshots/q11_missing_district_percentage.png)
