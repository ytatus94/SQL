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
方法 1.
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
方法 2.
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

### 21.
```SQL
```

### 22.
```SQL
```

### 23.
```SQL
```

### 24.
```SQL
```

### 25.
```SQL
```

### 26.
```SQL
```

### 27.
```SQL
```

### 28.
```SQL
```

### 29.
```SQL
```

### 30.
```SQL
```

### 31.
```SQL
```

### 32.
```SQL
```

### 33.
```SQL
```

### 34.
```SQL
```

### 35.
```SQL
```

### 36.
```SQL
```

### 37.
```SQL
```

### 38.
```SQL
```

### 39.
```SQL
```

### 40.
```SQL
```

### 41.
```SQL
```

### 42.
```SQL
```

### 43.
```SQL
```

### 44.
```SQL
```

### 45.
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
