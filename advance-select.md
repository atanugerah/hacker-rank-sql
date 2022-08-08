### Type of Triangle

Write a query identifying the type of each record in the **TRIANGLES** table using its three side lengths. Output one of the following statements for each record in the table:
- Equilateral: It's a triangle with  sides of equal length.
- Isosceles: It's a triangle with  sides of equal length.
- Scalene: It's a triangle with  sides of differing lengths.
- Not A Triangle: The given values of A, B, and C don't form a triangle.
<br>


        SELECT
            CASE
            WHEN A + B > C AND A + C > B AND B + C > A THEN 
                (CASE
                WHEN A = B AND B = C THEN "Equilateral"
                WHEN A = B OR B = C OR A = C AND A != B != C THEN "Isosceles"
                ELSE "Scalene"
                END)
            ELSE "Not A Triangle"
            END
        FROM TRIANGLES


### The PADS

Generate the following two result sets:
1. Query an alphabetically ordered list of all `names` in OCCUPATIONS, immediately followed by the first letter of each `profession` as a parenthetical (i.e.: enclosed in parentheses). For example: AnActorName(A), ADoctorName(D), AProfessorName(P), and ASingerName(S).
2. Query the number of ocurrences of each `occupation` in OCCUPATIONS. Sort the occurrences in ascending order, and output them in the following format:
 `There are a total of [occupation_count] [occupation]s.` Where `[occupation_count]` is the number of occurrences of an occupation in OCCUPATIONS and `[occupation]` is the lowercase occupation name. If more than one Occupation has the same `[occupation_count]`, they should be ordered alphabetically.

**Note**: There will be at least two entries in the table for each type of occupation.
<br>


        SELECT
            CONCAT(Name,'(', SUBSTR(Occupation,1,1), ')')
        FROM OCCUPATIONS
        ORDER BY Name ASC;

        SELECT
            CONCAT('There are a total of ', COUNT(DISTINCT Name), ' ', LOWER(Occupation), 's.')
        FROM OCCUPATIONS
        GROUP BY Occupation
        ORDER BY COUNT(DISTINCT Name), Occupation


### Occupations

Pivot the `Occupation` column in OCCUPATIONS so that each `Name` is sorted alphabetically and displayed underneath its corresponding Occupation. The output column headers should be `Doctor`, `Professor`, `Singer`, and `Actor`, respectively.

**Note**: Print NULL when there are no more names corresponding to an occupation.

Occupation will only contain one of the following values: Doctor, Professor, Singer or Actor.
<br>


        SELECT
            Doctor,
            Professor,
            Singer,
            Actor
        FROM (SELECT
                name_order,
                MAX(CASE Occupation WHEN 'Doctor' THEN Name end) AS Doctor,
                MAX(CASE Occupation WHEN 'Professor' THEN Name end) AS Professor,
                MAX(CASE Occupation WHEN 'Singer' THEN Name end) AS Singer,
                MAX(CASE Occupation WHEN 'Actor' THEN Name end) AS Actor
              FROM (SELECT
                        Occupation,
                        Name,
                        ROW_NUMBER() OVER(PARTITION BY Occupation ORDER BY Name ASC) AS name_order
                    FROM Occupations
                    ) AS list
        GROUP BY name_order
        ) AS names

### Binary Tree Nodes

You are given a table, BST, containing two columns: `N` and `P`, where `N` represents the value of a node in Binary Tree, and `P` is the parent of `N`.

Write a query to find the node type of Binary Tree ordered by the value of the node. Output one of the following for each node:
- Root: If node is root node.
- Leaf: If node is leaf node.
- Inner: If node is neither root nor leaf node.
<br>

        SELECT
            N,
            CASE
            WHEN N IN (SELECT N FROM BST WHERE P IS NULL) THEN "Root" 
            WHEN NOT N IN (SELECT N FROM BST WHERE N IN (SELECT P FROM BST)) THEN "Leaf"
            ELSE "Inner"
            END
        FROM BST
        ORDER BY N


### New Companies

Amber's conglomerate corporation just acquired some new companies. Each of the companies follows this hierarchy:

Founder &#8594; Lead Manager &#8594; Senior Manager &#8594; Manager &#8594; Employee

Given the table schemas below, write a query to print the `company_code`, `founder name`, `total number of lead managers`, `total number of senior managers`, 
`total number of managers`, and `total number of employees`. Order your output by ascending company_code.

**Note**:
- The tables may contain duplicate records.
- The `company_code` is string, so the sorting should not be numeric. For example, if the `company_code`s are C_1, C_2, and C_10, then the ascending company_codes 
will be C_1, C_10, and C_2.

<br>

        SELECT
            c.company_code,
            c.founder,
            COUNT(DISTINCT lm.lead_manager_code),
            COUNT(DISTINCT sm.senior_manager_code),
            COUNT(DISTINCT m.manager_code),
            COUNT(DISTINCT e.employee_code)
        FROM Company c
        INNER JOIN Employee e ON e.company_code = c.company_code
        INNER JOIN Manager m ON m.company_code = c.company_code
        INNER JOIN Senior_Manager sm ON sm.company_code = c.company_code
        INNER JOIN Lead_Manager lm ON lm.company_code = c.company_code
        GROUP BY c.company_code, c.founder
        ORDER BY c.company_code
