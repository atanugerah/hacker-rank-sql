### Placements

You are given three tables: **Students, Friends** and **Packages**. 
- Students contains two columns: ID and Name. 
- Friends contains two columns: ID and Friend_ID (ID of the ONLY best friend). 
- Packages contains two columns: ID and Salary (offered salary in $ thousands per month).

Write a query to output the names of those students whose best friends got offered a higher salary than them. Names must be ordered by the salary amount offered
to the best friends. It is guaranteed that no two students got same salary offer.
<br>

    SELECT s.Name
    FROM(SELECT
            f.ID AS ID,
            CASE
            WHEN f.ID THEN p2.Salary
            END AS Salary_ID,
            f.Friend_ID AS Friend_ID,
            CASE
            WHEN f.Friend_ID THEN p1.Salary
            END AS Salary_Friend
        FROM Friends f
        INNER JOIN Packages p1 ON p1.ID = f.Friend_ID
        INNER JOIN Packages p2 ON p2.ID = f.ID) T
    INNER JOIN Students s ON s.ID = T.ID
    WHERE T.Salary_Friend > T.Salary_ID
    ORDER BY T.Salary_Friend

### Symmetric Pairs

You are given a table, **Functions**, containing two columns: X and Y.

Two pairs (X1, Y1) and (X2, Y2) are said to be symmetric pairs if X1 = Y2 and X2 = Y1.

Write a query to output all such symmetric pairs in ascending order by the value of X. List the rows such that X1 ≤ Y1.
<br>

    SELECT X,
           Y
    FROM Functions f1
    WHERE EXISTS
        (SELECT *
         FROM Functions f2
         WHERE f2.Y = f1.X
            AND f2.X = f1.Y
            AND f2.X > f1.X)
        AND (X != Y)
    UNION
    SELECT X,
           Y
    FROM Functions f1
    WHERE X = Y
      AND ((SELECT COUNT(*)
            FROM Functions
            WHERE X = f1.X
            AND Y = f1.X) > 1)
    ORDER BY X;

### Interviews

Samantha interviews many candidates from different colleges using coding challenges and contests. Write a query to print the `contest_id`, `hacker_id`, `name`, and 
the sums of `total_submissions`, `total_accepted_submissions`, `total_views`, and `total_unique_views` for each contest sorted by `contest_id`. Exclude the contest
from the result if all four sums are 0.

**Note**: A specific contest can be used to screen candidates at more than one college, but each college only holds 1 screening contest.

Tables: **Contests**, **Challenges**, **Colleges**, **View_Stats**, **Submission_Stats**
<br>

    SELECT
        con.contest_id,
        con.hacker_id, 
        con.name, 
        SUM(total_submissions), 
        SUM(total_accepted_submissions), 
        SUM(total_views), 
        SUM(total_unique_views)
    FROM Contests con 
    JOIN Colleges col ON con.contest_id = col.contest_id 
    JOIN Challenges cha ON col.college_id = cha.college_id 
    LEFT JOIN (SELECT challenge_id, SUM(total_views) AS total_views, SUM(total_unique_views) AS total_unique_views 
              FROM view_stats 
              GROUP BY challenge_id
              ) vs ON cha.challenge_id = vs.challenge_id 
    LEFT JOIN (SELECT challenge_id, SUM(total_submissions) AS total_submissions, SUM(total_accepted_submissions) AS total_accepted_submissions 
              FROM submission_stats 
              GROUP BY challenge_id
              ) ss ON cha.challenge_id = ss.challenge_id
    GROUP BY con.contest_id, con.hacker_id, con.name
    HAVING SUM(total_submissions)!=0 
        OR SUM(total_accepted_submissions)!=0
        OR SUM(total_views)!=0 
        OR SUM(total_unique_views)!=0
    ORDER BY contest_id








