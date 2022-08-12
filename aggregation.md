### Revising Aggregations - The Count Function

Query a count of the number of cities in **CITY** having a Population larger than 100.000.
<br>

    SELECT COUNT(ID)
    FROM CITY
    WHERE POPULATION > 100000

### Revising Aggregations - The Sum Function

Query the total population of all cities in **CITY** where District is California.
<br>

    SELECT SUM(POPULATION)
    FROM CITY
    WHERE DISTRICT = 'California'

### Revising Aggregations - Averages

Query the average population of all cities in **CITY** where District is California.
<br>

    SELECT AVG(POPULATION)
    FROM CITY
    WHERE DISTRICT = 'California'

### Average Population

Query the average population for all cities in **CITY**, rounded down to the nearest integer.
<br>

    SELECT FLOOR(AVG(POPULATION))
    FROM CITY

### Japan Population

Query the sum of the populations for all Japanese cities in **CITY**. The COUNTRYCODE for Japan is JPN.
<br>

    SELECT SUM(POPULATION)
    FROM CITY
    WHERE COUNTRYCODE = 'JPN'

### Population Density Difference

Query the difference between the maximum and minimum populations in **CITY**.
<br>

    SELECT MAX(POPULATION) - MIN(POPULATION)
    FROM CITY

### The Blunder

Samantha was tasked with calculating the average monthly salaries for all employees in the **EMPLOYEES** table, but did not realize her keyboard's  key was broken 
until after completing the calculation. She wants your help finding the difference between her miscalculation (using salaries with any zeros removed), and the actual 
average salary.

Write a query calculating the amount of error (i.e.: actual - miscalculated average monthly salaries), and round it up to the next integer.
<br>

    SELECT CEILING(AVG(Salary) - AVG(REPLACE(Salary, '0', '')))
    FROM EMPLOYEES

### Top Earners

We define an employee's total earnings to be their `monthly salary x months worked`, and the maximum total earnings to be the maximum total earnings for any employee
in the **Employee** table. Write a query to find the maximum total earnings for all employees as well as the total number of employees who have maximum total earnings.
Then print these values as 2 space-separated integers.
<br>

    SELECT total_earnings, COUNT(employee_id)
    FROM (SELECT 
              employee_id, 
              salary*months AS total_earnings 
          FROM Employee
          ) Table1
    GROUP BY total_earnings
    ORDER BY total_earnings DESC
    LIMIT 1

### Weather Observation Station 2

Query the following two values from the **STATION** table:
1. The sum of all values in LAT_N rounded to a scale of 2 decimal places.
2. The sum of all values in LONG_W rounded to a scale of 2 decimal places.
<br>

    SELECT
        ROUND(SUM(LAT_N), 2),
        ROUND(SUM(LONG_W), 2)
    FROM STATION

### Weather Observation Station 13

Query the sum of Northern Latitudes (LAT_N) from **STATION** having values greater than 38.7880 and less than 137.2345. Truncate your answer to 4 decimal places.
<br>

    SELECT
        ROUND(SUM(LAT_N), 4)
    FROM STATION
    WHERE LAT_N > 38.7880 AND LAT_N < 137.2345

### Weather Observation Station 14

Query the greatest value of the Northern Latitudes (LAT_N) from **STATION** that is less than 137.2345. Truncate your answer to 4 decimal places.
<br>

    SELECT
        ROUND(MAX(LAT_N), 4)
    FROM STATION
    WHERE LAT_N < 137.2345

### Weather Observation Station 15

Query the Western Longitude (LONG_W) for the largest Northern Latitude (LAT_N) in **STATION** that is less than 137.2345. Round your answer to 4 decimal places.
<br>

    SELECT
        ROUND(LONG_W, 4)
    FROM STATION
    WHERE LAT_N < 137.2345
    ORDER BY LAT_N DESC
    LIMIT 1

### Weather Observation Station 16

Query the smallest Northern Latitude (LAT_N) from STATION that is greater than 38.7780. Round your answer to 4 decimal places.
<br>

    SELECT
        ROUND(MIN(LAT_N), 4)
    FROM STATION
    WHERE LAT_N > 38.7780

### Weather Observation Station 17

Query the Western Longitude (LONG_W) where the smallest Northern Latitude (LAT_N) in STATION is greater than 38.7780. Round your answer to 4 decimal places.
<br>

    SELECT
        ROUND(LONG_W, 4)
    FROM STATION
    WHERE LAT_N > 38.7780
    ORDER BY LAT_N ASC
    LIMIT 1

### Weather Observation Station 18

Consider `P_1 (a,b)` and `P_2 (c,d)` to be two points on a 2D plane.

- `a` happens to equal the minimum value in Northern Latitude (LAT_N in **STATION**).
- `b` happens to equal the minimum value in Western Longitude (LONG_W in **STATION**).
- `c` happens to equal the maximum value in Northern Latitude (LAT_N in **STATION**).
- `d` happens to equal the maximum value in Western Longitude (LONG_W in **STATION**).

Query the Manhattan Distance between points `P_1` and `P_2` and round it to a scale of 4 decimal places.
<br>

    SELECT
        ROUND((ABS(MIN(LAT_N) - MAX(LAT_N)) + ABS(MIN(LONG_W) - MAX(LONG_W))), 4)
    FROM STATION

### Weather Observation Station 19

Consider `P_1 (a,c)` and `P_2 (b,d)` to be two points on a 2D plane where `(a,b)` are the respective minimum and maximum values of Northern Latitude (LAT_N) and
`(c,d)` are the respective minimum and maximum values of Western Longitude (LONG_W) in **STATION**.

Query the Euclidean Distance between points `P_1` and `P_2` and format your answer to display 4 decimal digits.
<br>

    SELECT
        ROUND(SQRT(POWER(MIN(LAT_N) - MAX(LAT_N), 2) + POWER(MIN(LONG_W) - MAX(LONG_W), 2)), 4)
    FROM STATION

### Weather Observation Station 20

A median is defined as a number separating the higher half of a data set from the lower half. Query the median of the Northern Latitudes (LAT_N) from **STATION** 
and round your answer to 4 decimal places.
<br>

    SELECT
        ROUND(T.LAT_N, 4)
    FROM(SELECT
            LAT_N,
            ROW_NUMBER() OVER (ORDER BY LAT_N) AS ROW_NUM
        FROM STATION) T
    WHERE T.ROW_NUM = (SELECT CEILING(COUNT(LAT_N)/2) FROM STATION)

