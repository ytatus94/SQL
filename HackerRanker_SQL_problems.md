# HackerRanker SQL problems

### 01. Revising the Select Query I
```SQL
SELECT *
FROM CITY
WHERE POPULATION > 100000
AND COUNTRYCODE = 'USA'
```

### 02. Revising the Select Query II
```SQL
SELECT NAME
FROM CITY
WHERE POPULATION > 120000
AND COUNTRYCODE = 'USA'
```

### 03. Select All
```SQL
SELECT *
FROM CITY
```

### 04. Select By ID
```SQL
SELECT *
FROM CITY
WHERE ID = 1661
```

### 05. Japanese Cities' Attributes
```SQL
SELECT *
FROM CITY
WHERE COUNTRYCODE = 'JPN'
```

### 06. Japanese Cities' Names
```SQL
SELECT NAME
FROM CITY
WHERE COUNTRYCODE = 'JPN'
```

### 07. Weather Observation Station 1
```SQL
SELECT
    CITY,
    STATE
FROM STATION
```

### 08. Weather Observation Station 3
```SQL
SELECT
    DISTINCT CITY
FROM STATION
WHERE ID % 2 = 0
```

### 09. Weather Observation Station 4
```SQL
SELECT
    COUNT(CITY) - COUNT(DISTINCT CITY)
FROM STATION
```

### 10. Weather Observation Station 5
* 方法 1. 用 union 時，如果 query 中有 `order by`, `limit` 等關鍵字，就要先用括號把 query 刮起來
* `Union` 結合的兩個 query 要有相同的欄位
```SQL
(
    SELECT
        CITY,
        LENGTH(CITY)
    FROM STATION
    WHERE LENGTH(CITY) = (
        SELECT
            MIN(LENGTH(CITY))
        FROM STATION
    )
    ORDER BY 1
    LIMIT 1
)
UNION
(
    SELECT
        CITY,
        LENGTH(CITY)
    FROM STATION
    WHERE LENGTH(CITY) = (
        SELECT
            MAX(LENGTH(CITY))
        FROM STATION
    )
    ORDER BY 1
    LIMIT 1
)
```
* 方法 2. 分別寫兩個 query 用分號區隔開來
```SQL
SELECT
    CITY,
    MIN(LENGTH(CITY))
FROM STATION
GROUP BY 1
HAVING LENGTH(CITY) = (
    SELECT
        MIN(LENGTH(CITY))
    FROM STATION
)
ORDER BY 1
LIMIT 1;
SELECT
    CITY,
    MAX(LENGTH(CITY))
FROM STATION
GROUP BY 1
HAVING LENGTH(CITY) = (
    SELECT
        MAX(LENGTH(CITY))
    FROM STATION
)
ORDER BY 1
LIMIT 1;
```

### 11. Weather Observation Station 6
```SQL
SELECT
    CITY
FROM STATION
WHERE LEFT(CITY, 1) IN ('A', 'E', 'I', 'O', 'U')
```

### 12. Weather Observation Station 7
```SQL
SELECT
    DISTINCT CITY
FROM STATION
WHERE RIGHT(CITY, 1) IN ('A', 'E', 'I', 'O', 'U')
```

### 13. Weather Observation Station 8
```SQL
SELECT
    DISTINCT CITY
FROM STATION
WHERE LEFT(CITY, 1) IN ('A', 'E', 'I', 'O', 'U')
AND RIGHT(CITY, 1) IN ('A', 'E', 'I', 'O', 'U')
```

### 14. Weather Observation Station 9
```SQL
SELECT
    DISTINCT CITY
FROM STATION
WHERE LEFT(CITY, 1) NOT IN ('A', 'E', 'I', 'O', 'U')
```

### 15. Weather Observation Station 10
```SQL
SELECT
    DISTINCT CITY
FROM STATION
WHERE RIGHT(CITY, 1) NOT IN ('A', 'E', 'I', 'O', 'U')
```

### 16. Weather Observation Station 11
```SQL
SELECT
    DISTINCT CITY
FROM STATION
WHERE LEFT(CITY, 1) NOT IN ('A', 'E', 'I', 'O', 'U')
OR RIGHT(CITY, 1) NOT IN ('A', 'E', 'I', 'O', 'U')
```

### 17. Weather Observation Station 12
```SQL
SELECT
    DISTINCT CITY
FROM STATION
WHERE LEFT(CITY, 1) NOT IN ('A', 'E', 'I', 'O', 'U')
AND RIGHT(CITY, 1) NOT IN ('A', 'E', 'I', 'O', 'U')
```

### 18. Higher Than 75 Marks
```SQL
SELECT
    NAME
FROM STUDENTS S
WHERE MARKS > 75
ORDER BY RIGHT(NAME, 3), ID
```

### 19. Employee Names
```SQL
SELECT
    NAME
FROM EMPLOYEE
ORDER BY 1
```

### 20. Employee Salaries
```SQL
SELECT
    NAME
FROM EMPLOYEE
WHERE SALARY > 2000
AND MONTHS < 10
ORDER BY EMPLOYEE_ID
```

### 21. Type of Triangle
* 注意用"兩邊之和大於第三邊"先判斷是不是三角形
```SQL
SELECT
    CASE
        WHEN A + B <= C OR B + C <= A OR C + A <= B THEN 'Not A Triangle'
        WHEN A = B AND A = C THEN 'Equilateral'
        WHEN A = B OR A = C OR B = C THEN 'Isosceles'
        ELSE 'Scalene'
    END AS TRIANGLE
FROM TRIANGLES
```

### 22. The PADS
* `CONCAT()` 不會產生空白，需要空白要自己加入
```SQL
SELECT
    CONCAT(NAME, '(', LEFT(OCCUPATION, 1), ')')
FROM OCCUPATIONS
ORDER BY NAME;
SELECT
    CONCAT('There are a total of ', COUNT(*), ' ', LOWER(OCCUPATION), 's.')
FROM OCCUPATIONS
GROUP BY OCCUPATION
ORDER BY COUNT(*) , OCCUPATION;
```

### 23. Occupations (難)
* 如果只用 case 述句，那會形成一個有很多 NULL 的表格，每一個 row 只有一個欄位不是 NULL，這不是答案要的結果
* 要利用到 `ROW_NUMBER()` 先建立編號，然後用 `GROUP BY 編號` 時，相同編號的就會在同一 row
* `MAX()` 在這邊的功用只是因為要使用 `GROUP BY 編號` 需要一個 aggregation functions，所以用 `MIN()` 也是可以的
* 要記得 subquery 都要有名字
* 方法 1. 用 `MAX()`
```SQL
SELECT
    MAX(CASE WHEN SUB.OCCUPATION = 'Doctor' THEN SUB.NAME END) AS DOCTOR,
    MAX(CASE WHEN SUB.OCCUPATION = 'Professor' THEN SUB.NAME END) AS PROFESSOR,
    MAX(CASE WHEN SUB.OCCUPATION = 'Singer' THEN SUB.NAME END) AS SINGER,
    MAX(CASE WHEN SUB.OCCUPATION = 'Actor' THEN SUB.NAME END) AS ACTOR
FROM (
    SELECT
        ROW_NUMBER() OVER (PARTITION BY OCCUPATION ORDER BY NAME) AS ID,
        NAME,
        OCCUPATION
    FROM OCCUPATIONS
) SUB
GROUP BY SUB.ID
```
* 方法 2. 用 `MIN()`
```SQL
SELECT
    MIN(CASE WHEN SUB.OCCUPATION = 'Doctor' THEN SUB.NAME ELSE NULL END) AS 'Doctor',
    MIN(CASE WHEN SUB.OCCUPATION = 'Professor' THEN SUB.NAME ELSE NULL END) AS 'Professor',
    MIN(CASE WHEN SUB.OCCUPATION = 'Singer' THEN SUB.NAME ELSE NULL END) AS 'Singer',
    MIN(CASE WHEN SUB.OCCUPATION = 'Actor' THEN SUB.NAME ELSE NULL END) AS 'Actor'
FROM (
    SELECT
        ROW_NUMBER() OVER (PARTITION BY OCCUPATION ORDER BY NAME) AS 'ROW_NUMBER',
        NAME,
        OCCUPATION
    FROM OCCUPATIONS
) SUB
GROUP BY SUB.ROW_NUMBER
```

### 24. Binary Tree Nodes
* 如果沒有父節點，那就是 root
* 如果一個節點是別人的父節點，那就會屬於 inner
* 如果不是別的節點的父節點，那就是 leaf
```SQL
SELECT
    N,
    CASE
        WHEN P IS NULL THEN 'Root'
        WHEN N IN (SELECT DISTINCT P FROM BST) AND N IS NOT NULL THEN 'Inner'
        ELSE 'Leaf'
    END AS 'TYPE'
FROM BST
ORDER BY 1
```

### 25. New Companies
* 方法 1.
```SQL
SELECT
    C.COMPANY_CODE,
    C.FOUNDER,
    L.LCOUNT,
    S.SCOUNT,
    M.MCOUNT,
    E.ECOUNT
FROM COMPANY AS C
LEFT JOIN (
    SELECT
        COMPANY_CODE,
        COUNT(DISTINCT LEAD_MANAGER_CODE) AS LCOUNT
    FROM LEAD_MANAGER
    GROUP BY COMPANY_CODE
) AS L
ON C.COMPANY_CODE = L.COMPANY_CODE
LEFT JOIN (
    SELECT
        COMPANY_CODE,
        COUNT(DISTINCT SENIOR_MANAGER_CODE) AS SCOUNT
    FROM SENIOR_MANAGER
    GROUP BY COMPANY_CODE
) AS S
ON C.COMPANY_CODE = S.COMPANY_CODE
LEFT JOIN (
    SELECT
        COMPANY_CODE,
        COUNT(DISTINCT MANAGER_CODE) AS MCOUNT
    FROM MANAGER
    GROUP BY COMPANY_CODE
) AS M
ON C.COMPANY_CODE = M.COMPANY_CODE
LEFT JOIN (
    SELECT
        COMPANY_CODE,
        COUNT(DISTINCT EMPLOYEE_CODE) AS ECOUNT
    FROM EMPLOYEE
    GROUP BY COMPANY_CODE
) AS E
ON C.COMPANY_CODE = E.COMPANY_CODE
ORDER BY C.COMPANY_CODE;
```
* 方法 2. employee 表格中已經有 lead_manager, senior_manager, 和 manager 的資訊了，所以只用 employee 表格就好
* 因為各階層的主管底下會有不同員工，所以主管會重複很多次，要記得取 distinct
```SQL
SELECT
    C.COMPANY_CODE,
    C.FOUNDER,
    E.NUM_LEAD,
    E.NUM_SENIOR,
    E.NUM_MANAGER,
    E.NUM_EMPLOYEE
FROM COMPANY AS C
JOIN (
    SELECT
        COMPANY_CODE,
        COUNT(DISTINCT LEAD_MANAGER_CODE) AS NUM_LEAD,
        COUNT(DISTINCT SENIOR_MANAGER_CODE) AS NUM_SENIOR,
        COUNT(DISTINCT MANAGER_CODE) AS NUM_MANAGER,
        COUNT(DISTINCT EMPLOYEE_CODE) AS NUM_EMPLOYEE
    FROM EMPLOYEE
    GROUP BY 1
    ORDER BY 1
) AS E
ON C.COMPANY_CODE = E.COMPANY_CODE
ORDER BY C.COMPANY_CODE
```

### 26. Revising Aggregations - The Count Function
```SQL
SELECT
    COUNT(NAME)
FROM CITY
WHERE POPULATION > 100000
```

### 27. Revising Aggregations - The Sum Function
```SQL
SELECT
    SUM(POPULATION)
FROM CITY
WHERE DISTRICT = 'California'
```

### 28. Revising Aggregations - Averages
```SQL
SELECT
    AVG(POPULATION)
FROM CITY
WHERE DISTRICT='California'
```

### 29. Average Population
```SQL
SELECT
    FLOOR(AVG(POPULATION))
FROM CITY
```

### 30. Japan Population
```SQL
SELECT
    SUM(POPULATION)
FROM CITY
WHERE COUNTRYCODE = 'JPN'
```

### 31. Population Density Difference
```SQL
SELECT
    MAX(POPULATION) - MIN(POPULATION)
FROM CITY
```

### 32. The Blunder
```SQL
SELECT
    CEIL(AVG(Salary) - AVG(REPLACE(Salary, '0', '')))
FROM EMPLOYEES
```

### 33. Top Earners
```SQL
SELECT
    MONTHS * SALARY, COUNT(*)
FROM EMPLOYEE
    WHERE MONTHS * SALARY = (
    SELECT
        MAX(MONTHS * SALARY)
    FROM EMPLOYEE
)
GROUP BY MONTHS * SALARY
```

### 34. Weather Observation Station 2
```SQL
SELECT
    ROUND(SUM(LAT_N), 2),
    ROUND(SUM(LONG_W), 2)
FROM STATION
```

### 35. Weather Observation Station 13
```SQL
SELECT ROUND(SUM(LAT_N), 4)
FROM STATION
WHERE LAT_N > 38.7880 AND LAT_N < 137.2345
```
```SQL
SELECT
    ROUND(SUM(SUB.LAT_N), 4)
FROM (
    SELECT
        ID,
        LAT_N
    FROM STATION
    WHERE LAT_N BETWEEN 38.7880 AND 137.2345
) SUB

```

### 36. Weather Observation Station 14
```SQL
SELECT
    ROUND(MAX(LAT_N), 4)
FROM STATION
WHERE LAT_N < 137.2345
```

### 37. Weather Observation Station 15
```SQL
SELECT
    ROUND(LONG_W, 4)
FROM STATION
WHERE LAT_N = (
    SELECT
        MAX(LAT_N)
    FROM STATION
    WHERE LAT_N < 137.2345
) 
```

### 38. Weather Observation Station 16
```SQL
SELECT
    ROUND(MIN(LAT_N), 4)
FROM STATION
WHERE LAT_N > 38.7780
```

### 39. Weather Observation Station 17
```SQL
SELECT
    ROUND(LONG_W, 4)
FROM STATION
WHERE LAT_N = (
    SELECT
        MIN(LAT_N)
    FROM STATION
    WHERE LAT_N > 38.7780
)
```

### 40. Weather Observation Station 18
```SQL
-- Manhattan distance = |x1 - x2| + |y1 - y2|
SELECT
    ROUND(ABS(MIN(LAT_N) - MAX(LAT_N)) + ABS(MIN(LONG_W) - MAX(LONG_W)), 4)
FROM STATION
```

### 41. Weather Observation Station 19
```SQL
-- Euclidean distance = sqrt( (x1 - x2)^2 + (y1 - y2)^2 )
SELECT
    ROUND(SQRT(POWER(MIN(LAT_N) - MAX(LAT_N), 2) + POWER(MIN(LONG_W) - MAX(LONG_W), 2)), 4)
FROM STATION
```

### 42. Weather Observation Station 20
```SQL
```

### 43. Population Census 
```SQL
```

### 44. African Cities
```SQL
```

### 45. Average Population of Each Continent
```SQL
```

### 46.
```SQL
```

### 47.
```SQL
```

### 48.
```SQL
```

### 49.
```SQL
```

### 50.
```SQL
```

### 51.
```SQL
```

### 52.
```SQL
```

### 53.
```SQL
```

### 54.
```SQL
```

### 55.
```SQL
```

### 56.
```SQL
```

### 57.
```SQL
```

### 58.
```SQL
```

### 59.
```SQL
```

### 60.
```SQL
```
