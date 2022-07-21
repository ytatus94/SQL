# SQL

### SQL 語句分成
* DDL (Data Definition Language) 數據定義語言
* DML (Data Manipulation Language) 數據操作語言
  * 插入，刪除，更新
* DCL (Data Control Language) 數據控制語言
* DQL (Data Query Language) 數據查詢語言
  * 就是平時見到的 Queries

### SQL order of operations
1. `FROM`, `JOIN`
2. `WHERE`
3. `GROUP BY`
4. aggregate functions
5. `HAVING`
6. Window functions
7. `SELECT`
8. `DISTINCT`
9. `UNION`/`INTERSECT`/`EXCEPT`
10. `ORDER BY`
11. `OFFSET`
12. `LIMIT`/`FETCH`/`TOP`

### SQL window functions
* One major advantage of window functions is that it allows you to work with both aggregate and non-aggregate values all at once because the rows are not collapsed together.
  * 使用 window functions 後，表格的 row 數目仍然不變，但是增加了 window functions 的結果的 column
* Window function syntax
```SQL
SELECT
    <column_1>,
    <column_2>,
    <window_function>(expression) OVER (PARTITION BY <partition_list> ORDER BY <order_list> ROWS frame_clause)
FROM <table_name>
```
* ROWS frame_clause 的用法: `ROWS BETWEEN <starting_row> AND <ending_row>`
  * <starting_row> 和 <ending_row> 可以是下面幾種
    * `UNBOUNDED PRECEDING`: 從表格的第一 row 開始
    * `<some #> PRECEDING`: 從當前 row 往前算 <some #> row
    * `CURRENT ROW`: 當前 row
    * `<some #> FOLLOWING`: 從當前 row 往後算 <some #> row
    * `UNBOUNDED FOLLOWING`: 到表格的最後ㄧ row


* Type of window functions
  * Aggregate: `AVG()`, `MAX()`, `MIN()`, `SUM()`, `COUNT()`
  * Ranking: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `PERCENT_RANK()`, `NTILE()`
    * `RANK()` 和 `DENSE_RANK()` 的差別在於 `RANK()` 有同名次的時候，下一個排名就會跳過，而 `DENSE_RANK()` 不會跳過
    * 例如:

    |Name|Age|Rank|Dense_Rank|
    |:---|:---|:---|:---|
    |John|50|1|1|
    |Bob|50|1|1|
    |Mike|50|1|1|
    |Jerry|40|4|2|
    |Jessica|40|4|2|
    |Katie|30|6|3|
    
  * Value: `LAG()`, `LEAD()`, `FIRST_VALUE()`, `LAST_VALUE()`, `NTH_VALUE()`

### Examples
```SQL
SELECT
    job_title,
    salary,
    AVG(salary) OVER (PARTITION BY job_title) AS "avg_salary"
FROM salary_info
```
```SQL
SELECT
    CustomerId,
    UnitPrice,
    AVG(UnitPrice) OVER (PARTITION BY CustomerId) AS "AvgUnitPrice"
FROM Order
INNER JOIN OrderDetail
ON Order.Id = OrderDetail.OrderId
```
```SQL
SELECT
    CustomerId,
    EmployeeId,
    AVG(UnitPrice) OVER (PARTITION BY CustomerId, EmployeeId) AS "AvgUnitPrice"
FROM Order
INNER JOIN OrderDetail
ON Order.Id = OrderDetail.OrderId
```
```SQL
SELECT
    CustomerId,
    OrderDate,
    UnitPrice,
    ROW_NUMBER() OVER (PARTITION BY CustomerId ORDER BY UnitPrice DESC) AS "UnitRank"
FROM Order
INNER JOIN	OrderDetail
ON Order.Id = OrderDetail.OrderId
```
```SQL
SELECT
    CustomerId,
    OrderDate,
    UnitPrice,
    ROW_NUMBER() OVER (PARTITION BY CustomerId) AS "UnitRank"
FROM Order
INNER JOIN	OrderDetail
ON Order.Id = OrderDetail.OrderId
ORDER BY CustomerId, UnitPrice DESC
```
```SQL
SELECT
    CustomerId,
    OrderDate,
    UnitPrice,
    RANK() OVER (PARTITION BY CustomerId ORDER BY UnitPrice DESC) AS "UnitRank"
FROM Order
INNER JOIN OrderDetail
ON Order.Id = OrderDetail.OrderId
```
```SQL
SELECT
    CustomerId,
    OrderDate,
    UnitPrice,
    DENSE_RANK() OVER (PARTITION BY CustomerId ORDER BY UnitPrice DESC) AS "UnitRank"
FROM Order
INNER JOIN OrderDetail
ON Order.Id = OrderDetail.OrderId
```
```SQL
SELECT
    ProductId,
    OrderDate,
    Quantity,
    LAG(Quantity) OVER (PARTITION BY ProductId ORDER BY OrderDate) AS "LAG"
FROM Order
INNER JOIN OrderDetail
ON Order.Id = OrderDetail.OrderId
```
```SQL
SELECT
    ProductId,
    OrderDate,
    Quantity,
    LEAD(Quantity) OvER (PARTITION BY ProductId ORDER BY OrderDate) AS "LEAD"
FROM Order
INNER JOIN OrderDetail
ON Order.Id = OrderDetail.OrderId
```
```SQL
SELECT
    ProductId,
    OrderDate,
    Quantity,
    FIRST_VALUE(Quantity) OVER (PARTITION BY ProductId ORDER BY OrderDate) AS "FirstValue"
FROM Order
INNER JOIN OrderDetail
ON Order.Id = OrderDetail.OrderId
```


```SQL
SELECT
    CustomerId,
    UnitPrice,
    AVG(UnitPrice) OVER (PARTITION BY CustomerId ORDER BY CustomerId ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS "CumAvg"
FROM Order
INNER JOIN OrderDetail
On Order.Id = OrderDetail.OrderId
```


```SQL
SELECT
    duration_seconds,
    SUM(duration_seconds) OVER (ORDER BY start_time) AS running_total
FROM tutorial.dc_bikeshare_q1_2012
```
```SQL
SELECT
    start_terminal,
    duration_seconds,
    SUM(duration_seconds) OVER (PARTITION BY start_terminal ORDER BY start_time) AS running_total
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
```
```SQL
SELECT
    start_terminal,
    duration_seconds,
    SUM(duration_seconds) OVER (PARTITION BY start_terminal) AS start_terminal_total
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
```
```SQL
SELECT
    start_terminal,
    duration_seconds,
    SUM(duration_seconds) OVER (PARTITION BY start_terminal) AS start_terminal_sum,
    (duration_seconds / SUM(duration_seconds) OVER (PARTITION BY start_terminal)) * 100 AS pct_of_total_time
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
ORDER BY 1, 4 DESC
```
```SQL
SELECT
    start_terminal,
    duration_seconds,
    SUM(duration_seconds) OVER (PARTITION BY start_terminal) AS running_total,
    COUNT(duration_seconds) OVER (PARTITION BY start_terminal) AS running_count,
    AVG(duration_seconds) OVER (PARTITION BY start_terminal) AS running_avg
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
```
```SQL
SELECT
    start_terminal,
    duration_seconds,
    SUM(duration_seconds) OVER (PARTITION BY start_terminal ORDER BY start_time) AS running_total,
    COUNT(duration_seconds) OVER (PARTITION BY start_terminal ORDER BY start_time) AS running_count,
    AVG(duration_seconds) OVER (PARTITION BY start_terminal ORDER BY start_time) AS running_avg
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
```
```SQL
SELECT
    end_terminal,
    duration_seconds,
    SUM(duration_seconds) OVER (PARTITION BY end_terminal ORDER BY duration_seconds DESC) AS running_total
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
```
```SQL
SELECT
    start_terminal,
    start_time,
    duration_seconds,
    ROW_NUMBER() OVER (ORDER BY start_time) AS row_number
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
```
```SQL

SELECT
    start_terminal,
    start_time,
    duration_seconds,
    ROW_NUMBER() OVER (ORDER BY start_time ORDER BY start_time) AS row_number
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
```
```SQL
SELECT
    start_terminal,
    duration_seconds,
    RANK() OVER (PARTITION BY start_terminal ORDER BY start_time) AS rank
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
```
```SQL
SELECT *
FROM (
    SELECT
        start_terminal,
        start_time,
        duration_seconds AS trip_time,
        RANK() OVER (PARTITION BY start_terminal ORDER BY duration_seconds DESC) AS rank
    FROM tutorial.dc_bikeshare_q1_2012
    WHERE start_time < "2012-01-08"
) sub
WHERE sub.rank <= 5
```
```SQL
SELECT
    start_terminal,
    duration_seconds,
    NTILE(4) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS quartile,
    NTILE(5) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS quintile,
    NTILE(100) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS percentile
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
ORDER BY start_terminal, duration_seconds
```
```SQL
SELECT
    duration_seconds,
    NTILE(100) OVER (ORDER BY duration_seconds) AS percentile
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
ORDER BY 1 DESC
```
```SQL
SELECT
    start_terminal,
    duration_seconds,
    LAG(duration_seconds, 1) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS lag,
    LEAD(duration_seconds, 1) OVER (PARTITION BY start_terminal ORDER BY  duration_seconds) AS lead
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
ORDER BY start_terminal, duration_seconds
```
```SQL
SELECT
    start_terminal,
    duration_seconds,
    duration_seconds - LAG(duration_seconds, 1) OVER (PARTITION BY	start_terminal ORDER BY duration_seconds) AS difference
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
ORDER BY start_terminal, duration_seconds
```
```SQL
SELECT *
FROM (
    SELECT
        start_terminal,
        duration_seconds,
        duration_seconds - LAG(duration_seconds, 1) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS difference
    FROM tutorial.dc_bikeshare_q1_2012
    WHERE start_time < "2012-01-08"
    ORDER BY start_terminal, duration_seconds
) sub
WHERE sub.difference IS NOT NULL
```
```SQL
SELECT
    start_terminal,
    duration_seconds,
    NTILE(4) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS quartile,
    NTILE(5) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS quintile,
    NTILE(100) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS percentile
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
ORDER BY start_terminal, duration_seconds
```
```SQL
SELECT
    start_terminal,
    duration_seconds,
    NTILE(4) OVER ntile_window AS quartile,
    NTILE(5) OVER ntile_window AS quintile,
    NTILE(100) OVER ntile_window AS percentile
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < "2012-01-08"
WINDOW ntile_window AS (PARTITION BY start_terminal ORDER BY duration_seconds)
ORDER BY start_terminal, duration_seconds
```



```SQL
SELECT
    o.occurred_at,
    SUM(o.gloss_qty) OVER (ORDER BY o.occurred_at) AS running_gloss_orders
FROM demo.orders o
```
```SQL
SELECT
    o.occurred_at,
    o.account_id,
    SUM(o.gloss_qty) OVER (PARTITION BY o.account_id ORDER BY o.occurred_at) AS running_gloss_orders
FROM demo.orders o
```
```SQL
WITH order_ranks AS (
    SELECT
        o.id,
        o.account_id,
        o.gloss_qty,
        o.gloss_amt_usd,
        ROW_NUMBER() OVER (PARTITION BY o.account_id ORDER BY o.occurred_at DESC) AS acct_order_rank
    FROM demo.orders o
)
SELECT *
FROM order_ranks
WHERE acct_order_rank = 1
```
```SQL
SELECT
    o.id,
    o.account_id,
    DATE_TRUNC("date", o.occurred_at) AS occurred_day,
    o.gloss_qty,
    o.gloss_amt_usd,
    DENSE_RANK() OVER (PARTITION BY o.account_id ORDER BY DATE_TRUNC("day", o.occurred_at)) AS dense_rank
FROM demo.orders o
```
```SQL
SELECT
    o.id,
    o.occurred_at,
    o.gloss_qty,
    LAG(gloss_qty, 1) OVER (ORDER BY o.occurred_at) AS order_lag_1,
    LAG(gloss_qty, 2) OVER (ORDER BY o.occurred_at) AS order_lag_2,
    LAG(gloss_qty, 3) OVER (ORDER BY o.occurred_at) AS order_lag_3
FROM demo.orders o
```
```SQL
SELECT
    o.id,
    o.occurred_at,
    o.gloss_qty,
    MAS(gloss_qty) OVER (ORDER BY o.occurred_at ROWS BETWEEN 5 PRECEDING AND 1 PRECEDING) AS max_order
FROM demo.orders o
```
```SQL
WITH input AS (
    SELECT
        COUNT(1) AS trips,
        DATE_TRUNC("day", start_date) AS date
    FROM modeanalytics.sf_bike_share_trip
    GROUP BY 2
)
SELECT
    data,
    trips,
    AVG(trips) OVER (ORDER BY data ROWS BETWEEN 13 PRECEDING AND CURRENT ROW) AS mvg_avg
FROM input
```




```SQL
SELECT
    first_name,
    last_name,
    salary,
    SUM(salary) OVER () sum_salary
FROM employees;
```
```SQL


SELECT
    Name,
    Age,
    Department,
    Salary,
    AVG(Salary) OVER (PARTITION BY Department ORDER BY Age) AS AvgSalary
FROM employee
```
```SQL
SELECT
    ROW_NUMBER() OVER (PARTITION BY Department ORDER BY Salary DESC) AS emp_row_no,
    Name,
    Department,
    Salary,
    RANK() OVER (PARTITION BY Department ORDER BY Salary DESC) AS emp_rank,
    DENSE_RANK() OVER (PARTITION BY Department ORDER BY Salary DESC) AS emp_dense_rank
FROM employee
```
```SQL
SELECT
    emp_name,
    dealer_id,
    sales,
    AVG(sales) OVER () AS AvgSales
FROM q1_sales;
```
```SQL
SELECT
    emp_name,
    dealer_id,
    sales,
    AVG(sales) OVER (PARTITION BY dealer_id) AS AvgSales
FROM q1_sales
```
```SQL
SELECT
    dealer_id,
    sales,
    emp_name,
    ROW_NUMBER() OVER (PARTITION BY dealer_id ORDER BY sales) AS row,
    AVG(sales) OVER (PARTITION BY dealer_id) AS AvgSales
FROM q1_sales;
```
```SQL
SELECT
    dealer_id,
    sales,
    AVG(sales) OVER (PARTITION BY dealer_id) AS AvgSales
FROM q1_sales;
```
```SQL
SELECT
    dealer_id,
    sales,
    COUNT(*) OVER (ORDER BY dealer_id) AS `count`
FROM q1_sales
```
```SQL
SELECT
    dealer_id,
    sales,
    COUNT(sales) OVER (PARTITION BY dealer_id) AS `count`
FROM q1_sales
```
```SQL
SELECT
    emp_name,
    dealer_id,
    sales,
    MAX(sales) OVER (PARTITION BY dealer_id) AS `max`
FROM q1_sales;
```
```SQL
SELECT
    emp_name,
    dealer_id,
    sales,
    MIN(sales) OVER (PARTITION BY dealer_id) AS `min`
FROM q1_sales
```
```SQL
SELECT
    dealer_id,
    emp_name,
    sales,
    SUM(sales) OVER (PARTITION BY dealer_id) AS `sum`
FROM q1_sales
```
```SQL
SELECT
    dealer_id,
    sales,
    CUME_DIST() OVER (ORDER BY sales) AS cumedist
FROM q1_sales
```
```SQL
SELECT
    dealer_id,
    emp_name,
    sales,
    DENSE_RANK() OVER (ORDER BY sales) AS dense_rank
FROM q1_sales;
```
```SQL
SELECT
    emp_mgr,
    sales,
    NTILE(5) OVER (ORDER BY sales) AS ntile_rank
FROM q1_sales
```
```SQL
SELECT
    emp_mgr,
    dealer_id,
    sales,
    NTILE(3) OVER (PARTITION by dealer_id ORDER by sales) AS ntile_rank
FROM q1_sales;
```
```SQL
SELECT
    dealer_id,
    emp_name,
    sales,
    PERCENT_RANK() OVER (ORDER BY sales) AS per_rank
FROM q1_sales;
```
```SQL
SELECT
    dealer_id,
    emp_name,
    sales,
    RANK() OVER (ORDER BY sales) AS `rank`
FROM q1_sales
```
```SQL
SELECT
    dealer_id,
    emp_name,
    sales,
    ROW_NUMBER() OVER (PARTITION BY dealer_id ORDER BY sales) AS `rownum`
FROM q1_sales;
```
```SQL
SELECT
    cust_id,
    `date`,
    qty_sold,
    LAG(qty_sold, 1) OVER (ORDER BY cust_id, `date`) AS prev_qtysold
FROM sales
WHERE cust_id = 8
ORDER BY cust_id, `date`
```
```SQL
SELECT,
    show_id,
    `date`,
    commission,
    LEAD(commission, 1) OVER (ORDER BY `date`) AS next_comm
FROM commission
WHERE show_id = 172
```
```SQL
SELECT
    emp_name,
    dealer_id,
    sales,
    FIRST_VALUE(sales) OVER (PARTITION BY dealer_id ORDER BY sales) AS dealer_low
FROM q1_sales;
```
```SQL
SELECT
    emp_name,
    dealer_id,
    sales,
    `year`,
    LAST_VALUE(sales) OVER (PARTITION BY emp_name ORDER by `year`) AS last_sale
FROM emp_sales
WHERE `year` = 2013;
```
