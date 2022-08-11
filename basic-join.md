### African Cities

Given the **CITY** and **COUNTRY** tables, query the names of all cities where the `CONTINENT` is 'Africa'.

**Note**: CITY.CountryCode and COUNTRY.Code are matching key columns.
<br>

    SELECT
        city.name
    FROM city
    INNER JOIN country ON country.code = city.countrycode
    WHERE country.continent = 'Africa'


### Average Population of Each Continent

Given the **CITY** and **COUNTRY** tables, query the names of all the continents (COUNTRY.Continent) and their respective average city populations (CITY.Population) rounded down to the nearest integer.

**Note**: CITY.CountryCode and COUNTRY.Code are matching key columns.
<br>

    SELECT
        country.continent,
        FLOOR(AVG(city.population))
    FROM city
    INNER JOIN country ON country.code = city.countrycode
    GROUP BY country.continent

### Population Census

Given the **CITY** and **COUNTRY** tables, query the sum of the populations of all cities where the **CONTINENT** is 'Asia'.

**Note**: CITY.CountryCode and COUNTRY.Code are matching key columns.

    SELECT
        SUM(CITY.POPULATION)
    FROM CITY
    INNER JOIN COUNTRY ON COUNTRY.CODE = CITY.COUNTRYCODE
    WHERE COUNTRY.CONTINENT = 'Asia'

### The Report

You are given two tables: **Students** and **Grades**. Students contains three columns `ID`, `Name` and `Marks`.

Ketty gives Eve a task to generate a report containing three columns: `Name`, `Grade` and `Mark`. 

- Ketty doesn't want the NAMES of those students who received a grade lower than 8. 
- The report must be in descending order by grade -- i.e. higher grades are entered first. 
- If there is more than one student with the same grade (8-10) assigned to them, order those particular students by their name alphabetically. 
- Finally, if the grade is lower than 8, use "NULL" as their name and list them by their grades in descending order. 
- If there is more than one student with the same grade (1-7) assigned to them, order those particular students by their marks in ascending order.

Write a query to help Eve.
<br>

    UPDATE Students
    SET Name = NULL
    WHERE Marks < 70;

    SELECT
        Name,
        CASE
        WHEN Marks >= 90 THEN 10
        WHEN Marks >= 80 AND Marks < 90 THEN 9
        WHEN Marks >= 70 AND Marks < 80 THEN 8
        WHEN Marks >= 60 AND Marks < 70 THEN 7
        WHEN Marks >= 50 AND Marks < 60 THEN 6
        WHEN Marks >= 40 AND Marks < 50 THEN 5
        WHEN Marks >= 30 AND Marks < 40 THEN 4
        WHEN Marks >= 20 AND Marks < 30 THEN 3
        WHEN Marks >= 10 AND Marks < 20 THEN 2
        ELSE 1
        END AS Grade,
        Marks
    FROM Students
    ORDER BY Grade DESC, Name, Marks ASC


### Top Competitors

Julia just finished conducting a coding contest, and she needs your help assembling the leaderboard! Write a query to print the respective hacker_id and name of hackers who achieved full scores for more than one challenge. Order your output in descending order by the total number of challenges in which the hacker earned a full score. If more than one hacker received full scores in same number of challenges, then sort them by ascending hacker_id.

Tables: **Hackers, Difficulty, Challenges, Submissions** 
<br>

    SELECT
        DISTINCT ha.hacker_id,
        ha.name
    FROM Hackers ha 
    INNER JOIN Submissions sub ON ha.hacker_id = sub.hacker_id 
    INNER JOIN Challenges cha ON cha.challenge_id = sub.challenge_id
    INNER JOIN Difficulty diff ON diff.difficulty_level = cha.difficulty_level
    WHERE sub.score = diff.score
    GROUP BY ha.hacker_id, ha.name
    HAVING COUNT(sub.hacker_id) > 1
    ORDER BY COUNT(sub.hacker_id) DESC, ha.hacker_id ASC

### Ollivander's Inventory

Harry Potter and his friends are at Ollivander's with Ron, finally replacing Charlie's old broken wand.

Hermione decides the best way to choose is by determining the minimum number of gold galleons needed to buy each non-evil wand of high power and age. Write a query to print the `id`, `age`, `coins_needed`, and `power of the wands` that Ron's interested in, sorted in order of descending power. If more than one wand has same power, sort the result in order of descending age.

The following tables contain data on the wands in Ollivander's inventory: **Wands** and **Wands_Property**
<br>

    SELECT 
        w.id, 
        wp.age, 
        w.coins_needed, 
        w.power 
    FROM Wands w 
    INNER JOIN Wands_Property wp ON wp.code = w.code 
    WHERE  wp.is_evil = 0 
        AND w.coins_needed = (SELECT MIN(w1.coins_needed) 
                              FROM   Wands w1 
                              INNER JOIN wands_property wp1 ON wp1.code = w1.code 
                              WHERE  wp.age = wp1.age AND w.power = w1.power) 
    ORDER  BY w.power DESC, wp.age DESC

### Challenges

Julia asked her students to create some coding challenges. Write a query to print the hacker_id, name, and the total number of challenges created by each student. Sort your results by the total number of challenges in descending order. If more than one student created the same number of challenges, then sort the result by hacker_id. If more than one student created the same number of challenges and the count is less than the maximum number of challenges created, then exclude those students from the result.
<br>

    SELECT
        h1.hacker_id,
        h1.name,
        COUNT(DISTINCT c1.challenge_id) AS counts1
    FROM Hackers h1
    INNER JOIN Challenges c1 ON c1.hacker_id = h1.hacker_id
    GROUP BY h1.hacker_id, h1.name
    HAVING counts1 IN (SELECT student.counts
                       FROM(SELECT
                                h.hacker_id AS hacker_id,
                                h.name AS name,
                                COUNT(DISTINCT c.challenge_id) AS counts
                            FROM Hackers h
                            INNER JOIN Challenges c ON c.hacker_id = h.hacker_id
                            GROUP BY h.hacker_id, h.name
                            ) student
                       GROUP BY student.counts
                       HAVING COUNT(student.hacker_id) = 1 OR student.counts = 50
                       )
    ORDER BY counts1 DESC, h1.hacker_id


### Contest Leaderboard

You did such a great job helping Julia with her last coding contest challenge that she wants you to work on this one, too!

The total score of a hacker is the sum of their maximum scores for all of the challenges. Write a query to print the `hacker_id`, `name`, and `total score` of the hackers ordered by the descending score. If more than one hacker achieved the same total score, then sort the result by ascending hacker_id. Exclude all hackers with a total score of 0 from your result.

Table: **Hackers** and **Submissions**
<br>

    SELECT
        ms.hacker_id,
        ms.name,
        SUM(ms.score)
    FROM(SELECT
            h.hacker_id AS hacker_id,
            h.name AS name,
            s.challenge_id AS challenge_id,
            MAX(s.score) AS score
        FROM Hackers h
        INNER JOIN Submissions s ON s.hacker_id = h.hacker_id
        GROUP BY h.hacker_id, h.name, s.challenge_id
        ) ms
    GROUP BY ms.hacker_id, ms.name
    HAVING SUM(ms.score) > 0
    ORDER BY SUM(ms.score) DESC, ms.hacker_id ASC






