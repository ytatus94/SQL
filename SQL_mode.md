# Basic SQL


* 範例表格: us_housing_units

|欄位|型態|
|:---|:---|
|year||
|month||
|month_name||
|south||
|west||
|midwest||
|northeast||


```SQL
SELECT year,
       month,
       west
FROM tutorial.us_housing_units
```

* 用 `*` 代替全部的欄位
```SQL
SELECT *
FROM tutorial.us_housing_units
```

* 也可以把全部欄位名稱列出來
```SQL
SELECT year,
       month,
       month_name,
       south,
       west,
       midwest,
       northeast
FROM tutorial.us_housing_units
```

* 當欄位名稱有空白時，要用雙引號括起來，或者用底線取代空白
```SQL
SELECT west AS "West Region"
FROM tutorial.us_housing_units
```
```SQL
SELECT west AS West_Region,
       south AS South_Region
FROM tutorial.us_housing_units
```

* 預設上，欄位名字會用小寫輸出，如果要大寫的話，要用雙引號括起來
```SQL
SELECT year AS "Year",
       month AS "Month",
       month_name AS "Month Name",
       west AS "West",
       midwest AS "Midwest",
       south AS "South",
       northeast AS "Northeast"
FROM tutorial.us_housing_units
```

* 用 `LIMIT` 限制輸出列數
```SQL
SELECT *
FROM tutorial.us_housing_units
LIMIT 100
```
```SQL
SELECT *
FROM tutorial.us_housing_units
LIMIT 15
```

* 用 `WHERE` 來做 filter
```SQL
SELECT *
FROM tutorial.us_housing_units
WHERE month = 1
```


## Comparison operator on numerical data:
* Equal to `=`
* Not equal to `<>` or `!=`
* Greater than `>`
* Less than `<`
* Greater than or equal to `>=`
* Less than or equal to `<=`

The comparison operators can apply on non-numeric columns as well, they filter based on alphabetical order


### Q: Return only the rows where the West Region produced more than 30,000 housing units. The units in this data table are already in thousands.
```SQL
SELECT *
FROM tutorial.us_housing_units
WHERE west > 30
```

### Q: Did the West Region ever produce more than 50,000 housing units in one month?
```SQL
SELECT *
FROM tutorial.us_housing_units
WHERE west > 50
```

### Q: Did the South Region ever produce 20,000 or fewer housing units in one month?
```SQL
SELECT
FROM tutorial.us_housing_units
WHERE south <= 20
```

## Comparison operators on non-numerical data

### Q: None of the January rows show up.
```SQL
SELECT *
FROM tutorial.us_housing_units
WHERE month_name != "January"
```

* 用字母順序來看，找出比 January 大的
```SQL
SELECT *
FROM tutorial.us_housing_units
WHERE month_name > "January"
```

* 用字母順序來看，找出比 J 大的
    * Ja 比 J 大，所以結果會包含 January
```SQL
SELECT *
FROM tutorial.us_housing_units
WHERE month_name > "J"
```

### Q: Write a query that only shows rows for which the month name is February.
```SQL
SELECT *
FROM tutorial.us_housing_units
WHERE month_name = "February"
```

### Q: Write a query that only shows rows for which the month_name starts with the letter "N" or an earlier letter in the alphabet.
* 要找出 N 開頭或比 N 還要小的字母開頭的，相當於找出字母順序比 o 小的
```SQL
SELECT *
FROM tutorial.us_housing_units
WHERE month_name < "o"
```

* 對欄位進行加減乘除運算
```SQL
SELECT year,
       month,
       west,
       south,
       west + south AS south_plus_west
FROM tutorial.us_housing_units
```

* chain arithmetic functions, including both column names and actual numbers
```SQL
SELECT year,
       month,
       west,
       south,
       west + south - 4 * year AS nonsense_column 
FROM tutorial.us_housing_units
```

### Q: Write a query that calculates the sum of all four regions in a separate column.
```SQL
SELECT year,
       month,
       west,
       south,
       midwest,
       northeast,
       west + south + midwest + northeast AS usa_total,
FROM tutorial.us_housing_units
```

* 四則運算用括號括起來
```SQL
SELECT year,
       month,
       west,
       south,
       (west + south) / 2 AS south_west_avg
FROM tutorial.us_housing_units
```

### Q: Write a query that returns all rows for which more units were produced in the West region than in the Midwest and Northeast combined.
* 把四則運算用括號括起來是好習慣
```SQL
SELECT year,
       month,
       west,
       south,
       midwest,
       northeast
FROM tutorial.us_housing_units
WHERE west > (midwest + northeast)
```

### Q: Write a query that calculates the percentage of all houses completed in the United States represented by each region. Only return results from the year 2000 and later.
```SQL
SELECT year,
       month,
       west / (west + south + midwest + northeast) * 100 AS west_pct,
       south / (west + south + midwest + northeast) * 100 AS south_pct,
       midwest / (west + south + midwest + northeast) * 100 AS midwest_pct,
       northeast / (west + south + midwest + northeast) * 100 AS northeast_pct,
FROM tutorial.us_housing_units
WHERE year >= 2000
```


## SQL logical operators:
* `LIKE`: match similar values rather than exact ones
* `IN`: specify a list of values
* `BETWEEN`: select only rows within a certain range
* `IS NULL`: select rows that contain no data in a give column
* `AND`: select only rows that satisfy two conditions
* `OR`: select rows that satisfy either of two conditions
* `NOT`: select rows that do not match a certain condiation


* 範例表格: billboard_top_100_year_end

|欄位|型態|
|:---|:---|
|year_rank||
|group||
|artist||


```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
```
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
ORDER BY year DESC, year_rank
```

* 使用 wildcard `%` 代替所有字符
    * `Snoop%` 表示所有以 Snoop 開頭的
    * 因為 `GROUP` 是 SQL 關鍵字，所以要加上雙引號來表示用的是 group 欄位
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE "group" LIKE "Snoop%"
```

* `LIKE` 是 case-sensitive，用 `ILIKE` 可以 ignore case
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE "group" ILIKE "snoop%"
```

* 使用 wildcard `_` 表示單一個字符
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE artist ILIKE "dr_ke"
```

### Q: Write a query that returns all rows for which Ludacris was a member of the group.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE "group" ILIKE "%ludacris%"
```

## Q: Write a query that returns all rows for which the first artist listed in the group has a name that begins with "DJ".
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE "group" LIKE "DJ%"
```

* `IN` 要配上 list ，list 用中括號，逗點隔開
    * list 中可以放字串
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE  year_rank IN (1, 2, 3)
```
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE  artist IN ("Taylor Swift", "Usher", "Ludacris")
```

### Q: Write a query that shows all of the entries for Elvis and M.C. Hammer. M.C. Hammer is actually on the list under multiple names, so you may need to first write a query to figure out exactly how M.C. Hammer is listed.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE  "group" IN ("M.C. Hammer", "Hammer", "Elvis Presley")
```

* `BETWEEN` 要和 `AND` 一起用，結果有包含上下邊界
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank BETWEEN 5 AND 10
```

* 和上面的 query 等價
    * 注意有等號，因為要包含上下邊界
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank >= 5 AND year_rank <= 10
```

### Q: Write a query that shows all top 100 songs from January 1, 1985 through December 31, 1990.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year BETWEEN 1985 AND 1990
```

* `IS NULL`: select rows that contain no data in a given column
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE artist IS NULL
```

### Q: Write a query that shows all of the rows for which song_name is null.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE song_name IS NULL
```

* `AND`: selects the rows that satisfy both of two conditions.

* return all rows for top-10 recordings in 2012.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2012 AND year_rank <= 10
```

* 用多個 `AND` 時 all of the requirements are satisfied.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2012
AND year_rank <= 10
AND "group" ILIKE "%feat%"
```

### Q: Write a query that surfaces all rows for top-10 hits for which Ludacris is part of the Group.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank <= 10
AND "group" ILIKE "%ludacris%"
```

### Q: Write a query that surfaces the top-ranked records in 1990, 2000, and 2010.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank = 1
AND year IN (1990, 2000, 2010)
```

### Q: Write a query that lists all songs from the 1960s with "love" in the title.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year BETWEEN 1960 AND 1969
AND song_name ILIKE "%love%"
```

* `OR`: select rows that satisfy either of two conditions
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank = 5 OR artist = "Gotye"
```

* You can combine `AND` with `OR` using parenthesis.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2013
AND ("group" ILIKE "%macklemore%" OR "group" ILIKE "%timberlake%")
```

### Q: Write a query that returns all rows for top-10 songs that featured either Katy Perry or Bon Jovi.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank <= 10
AND ("group" ILIKE "%katy perry%" OR "group" = "%bon jovi%")
```

### Q: Write a query that returns all songs with titles that contain the word "California" in either the 1970s or 1990s.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE song_name ILIKE "%California%"
AND (year BETWEEN 1970 AND 1979 OR year BETWEEN 1990 AND 1999)
```

### Q: Write a query that lists all top-100 recordings that feature Dr. Dre before 2001 or after 2009.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE "group" ILIKE "%dr. dre%"
AND (year <= 2000 OR year >= 2010)
```

* `NOT`: put before any conditional statement to select rows for which that statement is false.
    * `NOT` is commonly used with `LIKE`

* results for which year_rank is equal to 2 or 3 are not included.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2013
AND year_rank NOT BETWEEN 2 AND 3
```
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2013
AND year_rank <= 3
```

* 下面這個 query 的結果不會包含 Macklemore
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2013
AND "group" NOT ILIKE "%macklemore%"
```

* `NOT` is also frequently used to identify non-null rows
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2013
AND artist IS NOT NULL
```

### Q: Write a query that returns all rows for songs that were on the charts in 2013 and do not contain the letter "a".
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2013
AND song_name NOT ILIKE "%a%"
```


* The `ORDER BY` clause allows you to reorder your results based on the data in one or more columns.
    * ascending order, and it's SQL's default.
    * If you'd like your results in the opposite order (referred to as descending order), you need to add the `DESC` operator:
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
ORDER BY artist
```

* `ORDER BY` 用在 numerical column 時，因為 ascending order 結果是由小排到大 (升冪排序)
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2013
ORDER BY year_rank
```

* 用 `DESC` 把結果由大排到小 (降冪排序)
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2013
ORDER BY year_rank DESC
```

### Q: Write a query that returns all rows from 2012, ordered by song title from Z to A.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2012
ORDER BY song_name DESC
```

* order by mutiple columns
    * columns in the `ORDER BY` clause must be separated by commas
    * the `DESC` operator is only applied to the column that precedes it

* 只把 year 由大排到小，year_rank 一樣是保持由小排到大
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank <= 3
ORDER BY year DESC, year_rank
```

* 下面的 query 結果會和上面的不同
    * 因為先把 year_rank 由小排到大後，再把 year 由大排到小
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank <= 3
ORDER BY year_rank, year DESC
```

* substituting numbers for column names in the `ORDER BY` clause.
    * The numbers will correspond to the order in which you list columns in the `SELECT` clause.
    * `ORDER BY` 後面的欄位可以用數字表示，數字的順序是 `SELECT` 中欄位的順序
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank <= 3
ORDER BY 2, 1 DESC
```

### Q: Write a query that returns all rows from 2010 ordered by rank, with artists ordered alphabetically for each song.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2010
ORDER BY year_rank, artist
```

* 用 `--`  來單行註解，用 `/*  */` 來多行註解

* use `--` (two dashes) to comment out everything to the right of them on a given line:
```SQL
SELECT * -- This comment won't affect the way the code runs
FROM tutorial.billboard_top_100_year_end
WHERE year = 2013
```

* leave comments across multiple lines using `/*` to begin the comment and `*/` to close it
```SQL
/* Here's a comment so long and descriptive that
it could only fit on multiple lines. Fortunately,
it, too, will not affect how this code runs. */
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2013
```

### Q: Write a query that shows all rows for which T-Pain was a group member, ordered by rank on the charts, from lowest to highest rank (from 100 to 1).
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE "group" ILIKE "%t-pain%"
ORDER BY year_rank DESC
```

### Q: Write a query that returns songs that ranked between 10 and 20 (inclusive) in 1993, 2003, or 2013. Order the results by year and rank, and leave a comment on each line of the WHERE clause to indicate what that line does.
```SQL
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank BETWEEN 10 AND 20 -- Limit the rank to 10-20
AND year in (1993, 2003, 2013) -- Select the relevant years
ORDER BY year, year_rank
```





#  Intermediate SQL



## Aggregate functions in SQL
* `COUNT()`: counts how many rows are in a particular column.
* `SUM()`: adds together all the values in a particular column.
* `MIN()` and `MAX()`: return the lowest and highest values in a particular column, respectively.
* `AVG()`: calculates the average of a group of selected values.


* Arithmetic operators only perform operations across rows. 
* Aggregate functions are used to perform operations across entire columns
    * Aggregators only aggregate vertically

* 範例表格: aapl_historical_stock_price

|欄位|型態|
|:---|:---|
|year||
|month||
|date||
|open||
|high||
|low||
|close||
|volume||

* Select all rows
    * `COUNT(1)` has the same effect as `COUNT(*)`
```SQL
SELECT COUNT(*)
FROM tutorial.aapl_historical_stock_price
```

* A count of all of rows in which the high column is not null.
    * `COUNT()` 不會把 NULL 的 row 列入計算
```SQL
SELECT COUNT(high)
FROM tutorial.aapl_historical_stock_price
```

### Q: Write a query to count the number of non-null rows in the low column.
```SQL
SELECT COUNT(low) AS low
FROM tutorial.aapl_historical_stock_price
```

* `COUNT()` on non-numerical columns
    * `COUNT()` 只是計算 non-null rows 的數目，不管是不是 distinct value, 所以也可以用在 non-numerical columns 上
```SQL
SELECT COUNT(date)
FROM tutorial.aapl_historical_stock_price
```
* `COUNT()` 輸出的欄位名字就叫 count，可以用 alias 改名
    * aliases using `AS`
```SQL
SELECT COUNT(date) AS count_of_date
FROM tutorial.aapl_historical_stock_price
```

* 有空白用括號括起來
```SQL
SELECT COUNT(date) AS "Count of DAte"
FROM tutorial.aapl_historical_stock_price
```

### Q: Write a query that determines counts of every single column. With these counts, can you tell which column has the most null values?
```SQL
SELECT COUNT(year) AS year,
       COUNT(month) AS month,
       COUNT(open) AS open,
       COUNT(high) AS high,
       COUNT(low) AS low,
       COUNT(close) AS close,
       COUNT(volume) AS volume,
FROM tutorial.aapl_historical_stock_price
```

* you can only use `SUM()` on columns containing numerical values.
    * `SUM()` treats nulls as 0.
```SQL
SELECT SUM(volume)
FROM tutorial.aapl_historical_stock_price
```

### Q: Write a query to calculate the average opening price (hint: you will need to use both `COUNT` and `SUM`, as well as some simple arithmetic.).
```SQL
SELECT SUM(open) / COUNT(open) AS avg_open_price
FROM tutorial.aapl_historical_stock_price
```

* `MIN()` and `MAX()` can be used on non-numerical columns.
    * 如果用在 non-numerical columns 就是依照字母順序來比較大小 (a 最小 z 最大)
```SQL
SELECT MIN(volume) AS min_volume,
       MAX(volume) AS max_volume
FROM tutorial.aapl_historical_stock_price
```

### Q: What was Apple's lowest stock price (at the time of this data collection)?
```SQL
SELECT MIN(low)
FROM tutorial.aapl_historical_stock_price
```

### Q: What was the highest single-day increase in Apple's share value?
```SQL
SELECT MAX(close - open)
FROM tutorial.aapl_historical_stock_price
```

* `AVG()` it can only be used on numerical columns. Second, it ignores nulls completely.
```SQL
SELECT AVG(high)
FROM tutorial.aapl_historical_stock_price
WHERE high IS NOT NULL
```

* 因為 `AVG()` 會忽略掉 NULL 所以下面的 query 和上面的 query 是等價的
```SQL
SELECT AVG(high)
FROM tutorial.aapl_historical_stock_price
```

### Q: Write a query that calculates the average daily trade volume for Apple stock.
```SQL
SELECT AVG(volume) AS avg_volume
FROM tutorial.aapl_historical_stock_price
```

* `GROUP BY` allows you to separate data into groups, which can be aggregated independently of one another.
    * 有用到 `GROUP BY` 的時候 `SELECT` 的欄位一定要有 aggregation function
```SQL
SELECT year,
       COUNT(*) AS count
FROM tutorial.aapl_historical_stock_price
GROUP BY year
```

* `GROUP BY` multiple columns, but you have to separate column names with commas
```SQL
SELECT year,
       month,
       COUNT(*) AS count
FROM tutorial.aapl_historical_stock_price
GROUP BY year, month
```

### Q: Calculate the total number of shares traded each month. Order your results chronologically.
```SQL
SELECT year,
       month,
       SUM(volume) AS volume_sum
FROM tutorial.aapl_historical_stock_price
GROUP BY year, month
ORDER BY year, month
```

* substitute numbers for column names in the `GROUP BY` clause
    * 在 `GROUP BY` 中可以用 `SELECT` 中欄位的順序編號，而不使用欄位名
```SQL
SELECT year,
       month,
       COUNT(*) AS count
FROM tutorial.aapl_historical_stock_price
GROUP BY 1, 2
```

* The order of column names in your `GROUP BY` clause doesn't matter
    * `GROUP BY` 要放在 `ORDER BY` 前面
```SQL
SELECT year,
       month,
       COUNT(*) AS count
FROM tutorial.aapl_historical_stock_price
GROUP BY year, month
ORDER BY month, year
```

* 當 `GROUP BY` 和 `LIMIT` 一起用的時候，SQL 會先跑 `GROUP BY` 算出結果後，再跑 `LIMIT` 來顯示出規定的 row 數目

### Q: Write a query to calculate the average daily price change in Apple stock, grouped by year.
```SQL
SELECT year,
       AVG(close - open) AS avg_daily_change
FROM tutorial.aapl_historical_stock_price
GROUP BY 1
ORDER BY 1
```

### Q: Write a query that calculates the lowest and highest prices that Apple stock achieved each month.
```SQL
SELECT year,
       month,
       MIN(low) AS lowest_price
       MAX(high) AS highest_price
FROM tutorial.aapl_historical_stock_price
GROUP BY 1, 2
ORDER BY 1, 2
```

* 有 Aggregation function 時，條件篩選要用 `HAVING` 不用 `WHERE`
```SQL
SELECT year,
       month,
       MAX(high) AS month_high
FROM tutorial.aapl_historical_stock_price
GROUP BY year, month
HAVING MAX(high) > 400
ORDER BY year, month
```


## Query clause order
1. `SELECT`
2. `FROM`
3. `WHERE`
4. `GROUP BY`
5. `HAVING`
6. `ORDER BY`


## The `CASE` statement is followed by at least one pair of `WHEN` and `THEN` statements
* SQL's equivalent of `IF/THEN`

## Every `CASE` statement must end with the `END` statement. The `ELSE` statement is optional, and provides a way to capture values not specified in the `WHEN/THEN` statements.

## The `CASE` statement always goes in the `SELECT` clause
* `CASE` must include the following components: `WHEN`, `THEN`, and `END`. `ELSE` is an optional component.


* 範例表格: benn.college_football_players

|欄位|型態|
|:---|:---|
|year||
|player_name||
|state||
|weight||
|height||
|position||
|school_name||


```SQL
SELECT player_name,
       year,
       CASE WHEN year = "SR" THEN "yes"
            ELSE NULL END AS is_a_senior -- 條件不滿足的時候，什麼都沒做，保持 NULL
FROM benn.college_football_players
```
```SQL
SELECT player_name,
       year,
       CASE WHEN year = "SR" THEN "yes"
            ELSE "no" END AS is_a_senior -- 條件不滿足的時候，用 no
FROM benn.college_football_players
```

### Q: Write a query that includes a column that is flagged "yes" when a player is from California, and sort the results with those players first.
```SQL
SELECT player_name,
       state,
       CASE WHEN state = "CA" THEN "yes"
       ELSE NULL END AS from_california
FROM benn.college_football_players
ORDER BY 3
```
```SQL
SELECT player_name,
       weight,
       CASE WHEN weight > 250 THEN "over 250"
            WHEN weight > 200 THEN '201-250'
            WHEN weight > 175 THEN '176-200'
            ELSE '175 or under' END AS weight_group
FROM benn.college_football_players
```

* 和上面的 query 等價，但是條件範圍的寫法比較清楚，沒有重複範圍
```SQL
SELECT player_name,
       weight,
       CASE WHEN weight > 250 THEN "over 250"
            WHEN weight > 200 AND weight <= 250 THEN '201-250'
            WHEN weight > 175 AND weight <= 200 THEN '176-200'
            ELSE '175 or under' END AS weight_group
FROM benn.college_football_players
```

### Q: Write a query that includes players' names and a column that classifies them into four categories based on height. Keep in mind that the answer we provide is only one of many possible answers, since you could divide players' heights in many ways.
```SQL
SELECT player_name,
       height,
       CASE WHEN height > 74 THEN "over 74"
            WHEN height > 72 AND height <= 74 THEN '73-74'
            WHEN height > 70 AND height <= 72 THEN '71-72'
            ELSE 'under 70' END AS height_group
FROM benn.college_football_players
```

* `CASE-WHEN-THEN-ELSE-END` 的條件可以加上 `AND` 和 `OR`
```SQL
SELECT player_name,
       CASE WHEN year = "FR" AND position = "WR" THEN 'frosh_wr'
            ELSE NULL END AS sample_case_statement
FROM benn.college_football_players
```

### Q: Write a query that selects all columns from `benn.college_football_players` and adds an additional column that displays the player's name if that player is a junior or senior.
```SQL
SELECT *, -- 題目要求顯示所有欄位
       CASE WHEN year IN ('JR', 'SR') THEN player_name
            ELSE NULL END AS upperclass_player_name
FROM benn.college_football_players
```

* `CASE-WHEN-THEN-ELSE-END` 也可以和 aggregate function 一起用
    * `COUNT()` 會忽略 NULL 所以要用 `CASE` 來把 null 和 non-null 區分開來
```SQL
SELECT CASE WHEN year = 'FR' THEN 'FR'
            ELSE 'Not FR' END AS year_group,
            COUNT(1) AS count
FROM benn.college_football_players
GROUP BY CASE WHEN year = 'FR' THEN 'FR'
              ELSE 'Not FR' END
```

* 上面等價於
```SQL
SELECT COUNT(1) AS fr_count
FROM benn.college_football_players
WHERE year = 'FR'
```

```SQL
SELECT CASE WHEN year = 'FR' THEN 'FR'
            WHEN year = 'SO' THEN 'SO'
            WHEN year = 'JR' THEN 'JR'
            WHEN year = 'SR' THEN 'SR'
            ELSE 'No Year Data' END AS year_group,
            COUNT(1) AS count
FROM benn.college_football_players
GROUP BY 1 -- 用 1 取代整個 CASE 子句
```

* 和上面的 query 等價，只是 `GROUP BY` 的部分用 alias
```SQL
SELECT CASE WHEN year = 'FR' THEN 'FR'
            WHEN year = 'SO' THEN 'SO'
            WHEN year = 'JR' THEN 'JR'
            WHEN year = 'SR' THEN 'SR'
            ELSE 'No Year Data' END AS year_group,
            COUNT(1) AS count
FROM benn.college_football_players
GROUP BY year_group -- 也可以用欄位的 alias
```

* 和上面的 query 等價，只是 `GROUP BY` 的部分用整個 `CASE` 條件判斷句
    * 如果要這樣寫，在 `GROUP BY` 的部分不可以加上 `AS year_group`
```SQL
SELECT CASE WHEN year = 'FR' THEN 'FR'
            WHEN year = 'SO' THEN 'SO'
            WHEN year = 'JR' THEN 'JR'
            WHEN year = 'SR' THEN 'SR'
            ELSE 'No Year Data' END AS year_group,
            COUNT(1) AS count
FROM benn.college_football_players
GROUP BY CASE WHEN year = 'FR' THEN 'FR'
              WHEN year = 'SO' THEN 'SO'
              WHEN year = 'JR' THEN 'JR'
              WHEN year = 'SR' THEN 'SR'
              ELSE 'No Year Data' END 
```

* `CASE` 條件判斷和 aggregate function 混用的時候，先寫 `CASE` 跑看看結果，結果正確了再寫 aggregate function 的部分，這樣比較不會出錯

### Q: Write a query that counts the number of 300lb+ players for each of the following regions: West Coast (CA, OR, WA), Texas, and Other (everywhere else).
```SQL
SELECT CASE WHEN state IN ('CA', 'OR', 'WA') THEN 'West Coast'
            WHEN state = 'TX' THEN "Texas"
            ELSE 'Other' END AS arbitrary_regional_designation,
            COUNT(1) AS players
FROM benn.college_football_players
WHERE weight >= 300
GROUP BY 1
```

### Q: Write a query that calculates the combined weight of all underclass players (FR/SO) in California as well as the combined weight of all upperclass players (JR/SR) in California.
```SQL
SELECT CASE WHEN year IN ('FR', 'SO') THEN 'underclass'
            WHEN year IN ('JR', 'SR') THEN 'upperclass'
            ELSE NULL END AS class_group,
       SUM(weight) AS combined_player_weight
FROM benn.college_football_players
WHERE state = 'CA'
GROUP BY 1
```

* 這個結果是 vertically 的
```SQL
SELECT CASE WHEN year = 'FR' THEN 'FR'
            WHEN year = 'SO' THEN 'SO'
            WHEN year = 'JR' THEN 'JR'
            WHEN year = 'SR' THEN 'SR'
            ELSE 'No Year Data' END AS year_group,
       COUNT(1) AS count
FROM benn.college_football_players
GROUP BY 1
```

## pivot table
* 就是把上面的 query 的結果從 vertically 變成 horizontally
* 用 `COUNT()` 時 NULL 不會被計算，所以只會算有幾個 1

```SQL
SELECT COUNT(CASE WHEN year = 'FR' THEN 1 ELSE NULL END) AS fr_count,
       COUNT(CASE WHEN year = 'SO' THEN 1 ELSE NULL END) AS so_count,
       COUNT(CASE WHEN year = 'JR' THEN 1 ELSE NULL END) AS jr_count,
       COUNT(CASE WHEN year = 'SR' THEN 1 ELSE NULL END) AS sr_count,
FROM benn.college_football_players
```

### Q: Write a query that displays the number of players in each state, with FR, SO, JR, and SR players in separate columns and another column for the total number of players. Order results such that states with the most players come first.
```SQL
SELECT state,
       COUNT(CASE WHEN year = 'FR' THEN 1 ELSE NULL END) AS fr_count,
       COUNT(CASE WHEN year = 'SO' THEN 1 ELSE NULL END) AS so_count,
       COUNT(CASE WHEN year = 'JR' THEN 1 ELSE NULL END) AS jr_count,
       COUNT(CASE WHEN year = 'SR' THEN 1 ELSE NULL END) AS sr_count,
       COUNT(1) AS total_players
FROM benn.college_football_players
GROUP BY state
ORDER BY total_players DESC
```

### Q: Write a query that shows the number of players at schools with names that start with A through M, and the number at schools with names starting with N - Z.
```SQL
SELECT CASE WHEN school_name < 'n' THEN 'A-M'
            WHEN school_name >= 'n' THEN 'N-Z'
            ELSE NULL END AS school_name_group,
       COUNT(1) AS players
FROM benn.college_football_players
GROUP BY 1
```

* 範例表格: aapl_historical_stock_price

|欄位|型態|
|:---|:---|
|year||
|month||

* Use `DISTINCT` for viewing unique values
```SQL
SELECT DISTINCT month
FROM tutorial.aapl_historical_stock_price
```

* 如果兩個或兩個以上的欄位有 `DISTINCT`，那就會找出 unique pairs 的組合

* contain all of the unique pairs of multi-columns
```SQL
SELECT DISTINCT year, month
FROM tutorial.aapl_historical_stock_price
```

### Q: Write a query that returns the unique values in the year column, in chronological order.
```SQL
SELECT DISTINCT year
FROM tutorial.aapl_historical_stock_price
ORDER BY year
```

* `DISTINCT` 可以和 aggregate function 一起用
    * 一般來說都是放在 aggregate function 裏面
    * 不過最常用在 `COUNT()`, 少用在 `SUM()` 和 `AVG()`
    * 不用在 `MIN()` 和 `MAX()` 因為結果會一樣，還會跑得比較慢
```SQL
SELECT COUNT(DISTINCT month) AS unique_months
FROM tutorial.aapl_historical_stock_price
```
```SQL
SELECT month,
       AVG(volume) AS avg_trade_volume
FROM tutorial.aapl_historical_stock_price
GROUP BY month
ORDER BY 2 DESC
```

### Q: Write a query that counts the number of unique values in the month column for each year.
```SQL
SELECT year, 
       COUNT(DISTINCT month) AS months_count
FROM tutorial.aapl_historical_stock_price
GROUP BY year
ORDER BY year
```

### Q: Write a query that separately counts the number of unique values in the month column and the number of unique values in the `year` column.
```SQL
SELECT COUNT(DISTINCT year) AS years_count,
       COUNT(DISTINCT month) AS months_count
FROM tutorial.aapl_historical_stock_price
```

* 範例表格: benn.college_football_players

|欄位|型態|
|:---|:---|
|weight||
|school_name||
|player_name||
|position||


* 範例表格: benn.college_football_teams

|欄位|型態|
|:---|:---|
|conference||
|school_name||
|division||


```SQL
SELECT teams.conference AS conference,
       AVG(players.weight) AS average_weight
FROM benn.college_football_players players
JOIN benn.college_football_teams teams
ON teams.school_name = players.school_name
GROUP BY teams.conference
ORDER BY AVG(players.weight) DESC
```

### Q: write a query that selects the school name, player name, position, and weight for every player in Georgia, ordered by weight (heavisted to lightest). Be sure to make an alias for the table, and to reference all column names in relation to the alias.
```SQL
SELECT players.school_name, 
       players.player_name, 
       players.position, 
       players.weight
FROM benn.college_football_players players
WHERE players.state = 'GA'
ORDER BY players.weight DESC
```

* `SELECT *` 傳回兩個表格的所有 columns 如果只想傳回一個表格的所有 column 可以用例如 `SELECT players.*`
```SQL
SELECT *
FROM benn.college_football_players players
JOIN benn.college_football_teams teams
ON teams.school_name = players.school_name
```
```SQL
SELECT players.school_name AS players_school_name,
       teams.school_name AS teams_school_name,
FROM benn.college_football_players players
JOIN benn.college_football_teams teams
ON team.school_name = players.school_name
```

### Q: Write a query that displays player names, school names and conferences for schools in the 'FBS (Division I-A Teams)' division.
```SQL
SELECT players.player_name,
       players.school_name,
       teams.conference
FROM benn.college_football_players players
JOIN benn.college_football_teams teams
ON team.school_name = players.school_name
WHERE  teams.division = 'FBS (Division I-A Teams)' 
```

## `JOIN`, `INNER JOIN`, `FULL OUTER JOIN`, `LEFT JOIN`, `RIGHT JOIN`
* `JOIN` 等於 `INNER JOIN`
* Outer joins 包含了 `LEFT JOIN`, `RIGHT JOIN`, `FULL OUTER JOIN`
* `LEFT JOIN` 等於 `OUTER LEFT JOIN`
* `RIGHT JOIN` 等於 `OUTER RIGHT JOIN`
* `FULL OUTER JOIN` 等於 `OUTER JOIN`


* 範例表格: crunchbase_companies

|欄位|型態|
|:---|:---|
|permalink||
|name||
|status||

* 範例表格: crunchbase_acquisitions

|欄位|型態|
|:---|:---|
|company_permalink||
|acquired_at||


* 用 `JOIN`
```SQL
SELECT companies.permalink AS companies_permalink,
       companies.name AS companies_name,
       acquisitions.company_permalink AS acquisitions_permalink,
       acquisitions.acquired_at AS acquired_date
FROM tutorial.crunchbase_companies companies
JOIN tutorial.crunchbase_acquisitions as acquisitions
ON companies.permalink = acquisitions.company_permalink
```

* 用 `LEFT JOIN`
```SQL
SELECT companies.permalink AS companies_permalink,
       companies.name AS companies_name,
       acquisitions.company_permalink AS acquisitions_permalink,
       acquisitions.acquired_at AS acquired_date
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_acquisitions acquisitions
ON companies.permalink = acquisitions.company_permalink
```

### Q: Write a query that performs an inner join between the `tutorial.crunchbase_acquisitions` table and the `tutorial.crunchbase_companies` table, but instead of listing individual rows, count the number of non-null rows in each table.
```SQL
SELECT COUNT(companies.permalink) AS companies_rowcount,
       COUNT(acquisitions.company_permalink) AS acquisitions_rowcount
FROM tutorial.crunchbase_companies companies
JOIN tutorial.crunchbase_acquisitions acquisitions
ON companies.permalink = acquisitions.company_permalink
```

### Q: Modify the query above to be a `LEFT JOIN`. Note the difference in results.
```SQL
SELECT COUNT(companies.permalink) AS companies_rowcount,
       COUNT(acquisitions.company_permalink) AS acquisitions_rowcount
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_acquisitions acquisitions
ON companies.permalink = acquisitions.company_permalink
```

### Q: Count the number of unique companies (don't double-count companies) and unique acquired companies by state. Do not include results for which there is no state data, and order by the number of acquired companies form highest to lowest.
```SQL
SELECT companies.state_code,
       COUNT(DISTINCT companies.permalink) AS unique_companies,
       COUNT(DISTINCT acquisitions.company_permalink) AS unique_companies_acquired
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_acquisitions acquisitions
ON companies.permalink = acquisitions.company_permalink
WHERE companies.state_code IS NOT NULL
GROUP BY 1
ORDER BY 3 DESC
```

* `RIGHT JOIN` 比較少用，因為和 `LEFT JOIN` 相似
    * 基本上都使用 `LEFT JOIN` 就可以了
```SQL
SELECT companies.permalink AS companies_permalink,
       companies.name AS companies_name,
       acquisitions.company_permalink AS acquisitions_permalink,
       acquisitions.acquired_at AS acquired_date
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_acquisitions acquisitions
ON companies.permalink = acquisitions.company_permalink
```

* 用 `RIGHT JOIN` 時，只要把 `FROM t1 LEFT JOIN t2` 改成 `FROM t2 RIGHT JOIN t1` 就可以了，下面的 query 結果會和上面的一樣
```SQL
SELECT companies.permalink AS companies_permalink,
       companies.name AS companies_name,
       acquisitions.company_permalink AS acquisitions_permalink,
       acquisitions.acquired_at AS acquired_date
FROM tutorial.crunchbase_acquisitions acquisitions
RIGHT JOIN tutorial.crunchbase_companies companies
ON companies.permalink = acquisitions.company_permalink
```

### Q: Rewrite the previous practice query in which you counted total and acquired companies by state, but with a `RIGHT JOIN` instead of a `LEFT JOIN`. The goal is to produce the exact same results.
```SQL
SELECT companies.state_code,
       COUNT(DISTINCT companies.permalink) AS unique_companies,
       COUNT(DISTINCT acquisitions.company_permalink) AS acquisitions_companies
FROM tutorial.crunchbase_acquisitions acquisitions
RIGHT JOIN tutorial.crunchbase_companies companies
ON companies.permalink = acquisitions.company_permalink
WHERE companies.state_code IS NOT NULL
GROUP BY 1
ORDER BY 3 DESC
```
```SQL
SELECT companies.permalink AS companies_permalink,
       companies.name AS companies_name,
       acquisitions.company_permalink AS acquisitions_permalink,
       acquisitions.acquired_at AS acquired_date
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_acquisitions acquisitions
ON companies.company_permalink = acquisitions.company_permalink
ORDER BY 1
```

* 在 `JOIN` 的時候也可以先篩選再合併
    * `AND` 後面的條件句會先執行篩選，但是只對在條件句中指定的那一個表格的內容做篩選
    * 可以想像成先對 acquisitions 表格做了個 `WHERE` 篩選後，再和 companies 表格做合併
```SQL
SELECT companies.permalink AS companies_permalink,
       companies.name AS companies_name,
       acquisitions.company_permalink AS acquisitions_permalink,
       acquisitions.acquired_at AS acquired_date
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_acquisitions acquisitions
ON companies.permalink = acquisitions.company_permalink
AND acquisitions.company_permalink != '/company/1000memories' -- 會在 join 之前執行，所以就達成篩選的功能
ORDER BY 1
```

* 如果是用 `WHERE` 篩選，那是先合併後再篩選，兩格表格的都會被篩選掉
```SQL
SELECT companies.permalink AS companies_permalink,
       companies.name AS companies_name
       acquisitions.company_permalink AS acquisitions_permalink,
       acquisitions.acquired_at AS acquired_date
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_acquisitions acquisitions
ON companies.permalink = acquisitions.company_permalink
WHERE acquisitions.company_permalink != '/company/1000memoories'
OR acquisitions.company_permalink IS NULL
ORDER BY 1
```

### Q: Write a query that shows a company's name, "status" (found in the companies table), and the number of unique investors in that company. Order by the number of investors from most to fewest. Limit to only companies in the state of New York.
```SQL
SELECT companies.name AS companies_name,
       companies.status,
       COUNT(DISTINCT investments.investor_name) AS unique_investors
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_investments investments
ON companies.permalink = investments.company_permalink
WHERE companies.state = 'NY'
GROUP BY 1, 2
ORDER BY 3 DESC
```

### Q: Write a query that lists investors based on the number of companies in which they are invested. Include a row for companies with no investor, and order from most companies to least.
```SQL
SELECT CASE WHEN investments.investor_name IS NULL THEN 'No Investors'
            ELSE investments.investor_name END AS investor,
       COUNT(DISTINCT companies.permalink) AS companies_invested_in
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_investments investments
ON companies.permalink = investments.company_permalink
GROUP BY 1
ORDER BY 2 DESC
```

* `FULL JOIN` 和 `FULL OUTER JOIN` 等價，而且少用
    * 通常是和 aggregations 一起用，然後來了解兩格表格有多少 overlap 的時候
```SQL
SELECT COUNT(CASE WHEN companies.permalink IS NOT NULL AND acquisitions.company_permalink IS NULL
                  THEN companies.permalink ELSE NULL END) AS companies_only,
       COUNT(CASE WHEN companies.permalink IS NOT NULL AND acquisitions.company_permalink IS NOT NULL
                  THEN companies.permalink ELSE NULL END) AS both_tables,
       COUNT(CASE WHEN companies.permalink IS NULL AND acquisitions.company_permalink IS NOT NULL
                  THEN acquisitions.company_permalink ELSE NULL END) AS acquisitions_only
FROM tutorial.crunchbase_companies companies
FULL JOIN tutorial.crunchbase_acquisitions acquisitions
ON companies.permalink = acquisitions.company_permalink
```

### Q: Write a query that join `tutorial.crunchbase_companies` and `tutorial.crunchbase_investments_part1` using a `FULL JOIN`. Count up the number of rows that are matched/unmatched as in the example above.
```SQL
SELECT COUNT(CASE WHEN companies.permalink IS NOT NULL AND investments.company_permalink IS NULL
                  THEN companies.permalink ELSE NULL END) AS companies_only,
       COUNT(CASE WHEN companies.permalink IS NOT NULL AND investments.company_permalink IS NOT NULL
                  THEN companies.permalink ELSE NULL END) AS both_tables,
       COUNT(CASE WHEN companies.permalink IS NULL AND investments.company_permalink IS NOT NULL
                  THEN investments.company_permalink ELSE NULL END) AS investments_only
FROM tutorial.crunchbase_companies companies
FULL JOIN tutorial.crunchbase_investments_part1 investments
ON companies.permalink = investments.company_permalink
```

* `UNION` 可以把 dataset 上下疊起來
    * 要疊起來的兩個表格要有相同的欄位，而且資料型態要相同
    * `UNION` 只會顯示沒有重複的，如果要允許重複，要用 `UNION ALL`

* `UNION` 不允許重複，就是把兩個表格上下疊起來後，再把重複的 row 刪除
```SQL
SELECT *
FROM tutorial.crunchbase_investments_part1
UNION
SELECT *
FROM tutorial.crunchbase_investments_part2
```

* `UNION ALL` 允許重複，實際上就是把兩個表格上下疊起來而已
```SQL
SELECT *
FROM tutorial.crunchbase_investments_part1
UNION ALL
SELECT *
FROM tutorial.crunchbase_investments_part2
```

### Q: Write a query that appends the two `crunchbase_investments` datasets above (including duplicated values). Filter the first dataset to only companies with names that start with the letter "T", and filter the second to companies with names starting with "M" (both not case-sensitive). Only include the `company_permalink`, `company_name`, and `investor_name` columns.
```SQL
SELECT company_permalink,
       company_name,
       investor_name
FROM tutorial.crunchbase_investments_part1
WHERE company_name ILIKE 'T%'
UNION ALL
SELECT company_permalink,
       company_name,
       investor_name
FROM tutorial.crunchbase_investments_part2
WHERE company_name ILIKE 'M%'
```

### Q: Write a query that shows 3 columns. The first indicates which dataset (part1 or 2) the data comes from, the second shows company status, and the third is a count of the number of investors. You will have to use the `tutorial.crunchbase_companies` table as well as the investment tables. And you'll want to group by status and dataset.
```SQL
SELECT 'investment_part1' AS dataset_name,
       companies.status,
       COUNT(DISTINCT investments.investor_permalink) AS investor
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_investments_part1 investments
ON companies.permalink = investments.company_permalink
GROUP BY 1, 2
UNION ALL
SELECT 'investments_part2' AS dataset_name,
       companies.tatus,
       COUNT(DISTINCT investments.investor_permalink) AS investors
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_investments_part2 investments
ON companies.permalink = investments.company_permalink
GROUP BY 1, 2
```

* `JOIN` 可以在 `ON` 裡面加入條件判斷，滿足條件的才會拿來 `JOIN`
    * `ON` 裡面有 `AND` 條件句或是 `OR` 條件句的話，就是先執行條件句再合併表格
```SQL
SELECT companies.permalink,
       companies.name,
       companies.status,
       COUNT(investments.investor_permalink) AS investors
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_investments_part1 investments
ON companies.permalink = investments.company_permalink
AND investments.funded_year > companies.funded_year + 5
GROUP BY 1, 2, 3
```

* 如果是用 `WHERE`, 那就是先 `JOIN` 再篩選
```SQL
SELECT companies.permalink,
       companies.name,
       companies.status,
       COUNT(investments.investor_permalink) AS investors
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_investments_part1 investments
ON companies.permalink = investments.company_permalink
WHERE investments.funded_year > companies.funded_year + 5
GROUP BY 1, 2, 3
```

* 可以在 `JOIN` 時用多個 keys 來合併表格
    * 在 `ON` 中用 `AND` 條件句篩選時，也可以加快速度
```SQL
SELECT companies.permalink,
       companies.name,
       investments.company_name,
       investments.company_permalink
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_investments_part1 investments
ON companies.permalink = investments.company_permalink
AND companies.name = investments.company_name
```

* Self join 就是自己和自己做 `JOIN`
```SQL
SELECT DISTINCT japan_investments.company_name,
       japan_investments.company_permalink
FROM tutorial.crunchbase_investments_part1 japan_investments
JOIN tutorial.crunchbase_investments_part1 gb_investments
ON japan_investments.company_name = gb_investments.compny_name
AND gb_investments.investor_country_code = 'GBR'
AND gb_investments.funded_at > japan_investments.fundd_at
WHERE japan_investments.investor_country_code = 'JPN'
ORDER BY 1
```





#  Advanced SQL



* 當資料的型態不是所需要的型態時，可以用下面兩種方式來轉換型態
    1. `CAST(column_name AS 新型態)`
    2. `column_name::新型態`

* 常見的資料型態
    * string 型態是 `VARCHAR`
    * Date/Time 型態是 `TIMESTAMP`
    * Number 型態是存成 double precision
    * Boolean 型態是 `BOOLEAN`

### Q: Convert the `funding_total_usd` and `funded_at_clean` columns in the `tutorial.crunchbase_companies_clean_date` table to strings (varchar format) using a different formatting function for each one.
```SQL
SELECT CAST(funding_total_usd AS varchar) AS funding_total_usd_string,
       funded_at_clean::varchar AS funded_at_string
FROM tutorial.crunchbase_companies_clean_date
```

* `funded_at` 是沒有經過處理的 date 欄位，是 string 型態，用 `ORDER BY` 時會出現奇怪的順序
```SQL
SELECT permalink,
       founded_at
FROM tutorial.crunchbase_companies_clean_date
ORDER BY founded_at
```

* `funded_at_clean` 是經過處理的 date 欄位，已經是 date 型態了，所以用 `ORDER BY` 時會顯示正確的排序
```SQL
SELECT permalink,
       founded_at,
       founded_at_clean
FROM tutorial.crunchbase_companies_clean_date
ORDER BY founded_at_clean
```

* 原始資料中 `companies.funded_at_clean` 欄位是以字串型態 string 儲存的，所以在計算之前要先把欄位的型態轉換成 timestamp
    * 沒有被併購的公司就不會有 `funded_at_clean` 所以要先篩選掉
    * 對 date 型態的欄位做四則運算，得到的新欄位的型態會被儲存成 interval 型態
```SQL
SELECT companies.permalink,
       companies.founded_at_clean,
       acquisitions.acquired_at_cleaned,
       acquisitions.acquired_at_cleaned - companies.founded_at_clean::timestamp AS time_to_acquisition 
FROM tutorial.crunchbase_companies_clean_date companies
JOIN tutorial.crunchbase_acquisitions_clean_date acquisitions
ON acquisitions.company_permalink = companies.permalink
WHERE founded_at_clean IS NOT NULL
```

* 用 `INTERVAL`，參數是 plain English

* 把 date 型態的欄位和 `INTERVAL` 型態的欄位做計算，得到的新欄位型態仍然是 date
```SQL
SELECT companies.permalink,
       companies.founded_at_clean,
       companies.founded_at_clean::timestamp + INTERVAL '1 week' AS plus_one_week
FROM tutorial.crunchbase_companies_clean_date companies
WHERE founded_at_clean IS NOT NULL
```

* `NOW()`: current time
```SQL
SELECT companies.permalink,
       companies.founded_at_clean,
       NOW() - companies.founded_at_clean::timestamp AS founded_time_ago
FROM tutorial.crunchbase_companies_clean_date companies
WHERE founded_at_clean IS NOT NULL
```

### Q: Write a query that counts the number of companies acquired within 3 years, 5 years, and 10 years of being founded (in 3 separate columns). Include a column for total companies acquired as well. Group by category and limit to only rows with a founding date.
```SQL
SELECT companies.category_code,
       COUNT(CASE WHEN acquisitions.acquired_at_cleaned <= companies.founded_at_clean::timestamp + INTERVAL '3 years'
                  THEN 1 ELSE NULL END) AS acquired_3_yrs,
       COUNT(CASE WHEN acquisitions.acquired_at_cleaned <= companies.founded_at_clean::timestamp + INTERVAL '5 years'
                  THEN 1 ELSE NULL END) AS acquired_5_yrs,
       COUNT(CASE WHEN acquisitions.acquired_at_cleaned <= companies.founded_at_clean::timestamp + INTERVAL '10 years'
                  THEN 1 ELSE NULL END) AS acquired_10_yrs,
       COUNT(1) AS total
FROM tutorial.crunchbase_companies_clean_date companies
JOIN tutorial.crunchbase_acquisitions_clean_date acquisitions
ON acquisitions.company_permalink = companies.permalink
WHERE founded_at_clean IS NOT NULL
GROUP BY 1
ORDER BY 5 DESC
```


## Data Munging / data wrangling
* Data munging or data wrangling is loosely the process of manually converting or mapping data from one raw form into another format that allows for more convenient consumption of the data with the help of semi-automated tools. In other words, data wrangling (or munging) is the process of programmatically transforming data into a format that makes it easier to work with.


* 範例表格: sf_crime_incidents_2014_01

|欄位|型態|
|:---|:---|
|location||
|lat||
|lon||


```SQL
SELECT *
FROM tutorial.sf_crime_incidents_2014_01
```


* `LEFT(string, number_of_characters)`
    * 從 string 中由左邊開始取出 number_of_characters 個字元
    * 如果原本的欄位不是 string 型態 (例如是 date 型態) 那結果也會被當成 string 型態
        * 例如: YYYY-MM-DD hh:mm:ss 是 date 型態，`LEFT(date, 10)` 的結果 YYYY-MM-DD 是 string 型態
```SQL
SELECT incidnt_num,
       date,
       LEFT(date, 10) AS cleaned_date
FROM tutorial.sf_crime_incidents_2014_01
```

* `RIGHT()` 和 `LEFT()` 相似，只是由右邊算起
```SQL
SELECT incidnt_num,
       date,
       LEFT(date, 10) AS cleaned_date,
       RIGHT(date, 17) AS cleaned_time
FROM tutorial.sf_crime_incidents_2014_01
```

* `LENGTH()` 傳回字串的長度
    * 在 functions 裡面使用其他的 functions 時，最裡面的那個 function 會先被執行
```SQL
SELECT incidnt_num,
       date,
       LEFT(date, 10) AS cleaned_date,
       RIGHT(date, LENGTH(date) - 11) AS cleaned_time
FROM tutorial.sf_crime_incidents_2014_01
```

* `TRIM (從哪邊刪除 要刪除的字元用單引號刮起來 從哪個欄位移除)` 用來刪除字元
    * 三個參數之間並沒有逗點
    * "從哪邊刪除" 有三種: 從頭刪除用 `leading`, 從尾巴刪除用 `trailing`, 從兩邊刪除用 `both`
```SQL
SELECT location,
       TRIM(both '()' FROM location)
FROM tutorial.sf_crime_incidents_2014_01
```

* `POSITION()` 傳回 substring 在原本的 string 中出現的位置
    * POSITION 是 case-sensitive 的
```SQL
SELECT incidnt_num,
       descript,
       POSITION('A' IN descript) AS a_position
FROM tutorial.sf_crime_incidents_2014_01
```

* `STRPOS(原字串, 要找的字串)`
    * `STRPOS()` 和 `POSITION()` 功能一樣，只是語法不同
    * `STRPOS()` 也是 case-sensitive 的
```SQL
SELECT incidnt_num,
       descript,
       STRPOS(descript, 'A') AS a_position
FROM tutorial.sf_crime_incidents_2014_01
```

* `SUBSTR(string, 從哪邊開始取, 取幾個)`
    * `SUBSTR()` 和 `LEFT()` 與 `RIGHT()` 一樣都使取出 substring，只是 `LEFT()` 從左邊取，`RIGHT()` 從右邊取，`SUBSTR()` 可以指定從哪裡開始取
```SQL
SELECT incidnt_num,
       date,
       SUBSTR(date, 4, 2) AS day
FROM tutorial.sf_crime_incidents_2014_01
```

### Q: Write a query that separates the `location` field into separate fields for laitude and longitude. You can compare your results against the actual `lat` and `lon` fields in the table.
```SQL
SELECT location,
       TRIM(leading '(' FROM LEFT(location, POSITION(',' IN location) - 1)) AS latitude,
       TRIM(trailing ')' FROM RIGHT(location, LENGTH(location) - POSITION(',' IN location))) AS longitude
FROM tutorial.sf_crime_incidents_2014_01
```

* `CONCAT()` 把字串合併
    * 要合併的欄位用逗號隔開，如果是 hard-coded 的字串就要用單引號括起來
```SQL
SELECT incidnt_num,
       day_of_week,
       LEFT(date, 10) AS cleaned_date,
       CONCAT(day_of_week, ', ', LEFT(date, 10)) AS day_and_date
FROM tutorial.sf_crime_incidents_2014_01
```

### Q: Concatenate the lat and lon fields to form a field that is equivalentto the location field. (Note that the answer will have a different decimal precision).
```SQL
SELECT CONCAT('(', lat, ', ', lon, ')') AS concat_location,
       location
FROM tutorial.sf_crime_incidents_2014_01
```

* 也可以用兩個 pipe 字元(`||`) 做 concatenation
```SQL
SELECT incidnt_num,
       day_of_week,
       LEFT(date, 10) AS cleaned_date,
       day_of_week || ', ' || LEFT(date, 10) AS day_and_date
FROM tutorial.sf_crime_incidents_2014_01
```

### Q: Create the same concatenated location field, but using the `||` syntax instead of `CONCAT`.
```SQL
SELECT '(' || lat || ',' || lon || ')' AS concat_location,
       location
FROM tutorial.sf_crime_incidents_2014_01
```

### Q: Write a query that creates a date column formatted YYYY-MM-DD.
```SQL
SELECT incidnt_num,
       date,
       SUBSTR(date, 7, 4) || '-' || LEFT(date, 2) || '-' || SUBSTR(date, 4, 2) AS cleaned_date
FROM tutorial.sf_crime_incidents_2014_01
```

* `UPPER()` 把所有字元改大寫，`LOWER()` 把所有字元改小寫
```SQL
SELECT incidnt_num,
       address,
       UPPER(address) AS address_upper,
       LOWER(address) AS address_lower
FROM tutorial.sf_crime_incidents_2014_01
```

### Q: Write a query that returns the `category` field, but with the first letter capitalized and the rest of the letters in lower-case.
```SQL
SELECT incidnt_num,
       category,
       UPPER(LEFT(category, 1)) || LOWER(RIGHT(category, LENGTH(category) - 1)) AS category_cleaned
FROM tutorial.sf_crime_incidents_2014_01
```

* 用字串處理，把欄位顯示成 YYYY-MM-DD 並且格式轉換成 date 型態
    * 因為只有用字串處理時，雖然顯示 YYYY-MM-DD 但型態仍然是 string 型態
```SQL
SELECT incidnt_num,
       date,
       (SUBSTR(date, 7, 4) || '-' || LEFT(date, 2) || '-' || SUBSTR(date, 4, 2))::date AS cleaned_date
FROM tutorial.sf_crime_incidents_2014_01
```

### Q: Write a query that creates an accurate timestamp using the date and time columns in `tutorial.sf_crime_incidents_2014_01`. Include a field that is exactly 1 week later as well.
```SQL
SELECT incidnt_num,
       (SUBSTR(date, 7, 4) || '-' || LEFT(date, 2) || '-' || SUBSTR(date, 4, 2) || ' ' || time || ':00')::timestamp AS timestamp,
       (SUBSTR(date, 7, 4) || '-' || LEFT(date, 2) || '-' || SUBSTR(date, 4, 2) || ' ' || time || ':00')::timestamp + INTERVAL '1 week' AS timestamp_plus_interval
FROM tutorial.sf_crime_incidents_2014_01 
```

* `EXTRACT()` 可以用來抽出 date 型態的資訊
```SQL
SELECT cleaned_date,
       EXTRACT('year' FROM cleaned_date) AS year,
       EXTRACT('month' FROM cleaned_date) AS month,
       EXTRACT('day' FROM cleaned_date) AS day,
       EXTRACT('hour' FROM cleaned_date) AS hour,
       EXTRACT('minute' FROM cleaned_date) AS minute,
       EXTRACT('second' FROM cleaned_date) AS second,
       EXTRACT('decade' FROM cleaned_date) AS decade,
       EXTRACT('dow' FROM cleaned_date) AS day_of_week
FROM tutorial.sf_crime_incidents_2014_01
```

* `DATE_TRUNC()` 可以把 date 型態的值做 round up
```SQL
SELECT cleaned_date,
       DATE_TRUNC('year', cleaned_date) AS year,
       DATE_TRUNC('month', cleaned_date) AS month,
       DATE_TRUNC('week', cleaned_date) AS week,
       DATE_TRUNC('day', cleaned_date) AS day,
       DATE_TRUNC('hour', cleaned_date) AS hour,
       DATE_TRUNC('minute', cleaned_date) AS minute,
       DATE_TRUNC('second', cleaned_date) AS second,
       DATE_TRUNC('decade', cleaned_date) AS decade
FROM tutorial.sf_crime_incidents_2014_01
```

### Q: Write q query that counts the number of incidents reported by week. Cast the week as a date to get ride of the hours/minutes/seconds.
```SQL
SELECT DATE_TRUNC('week', cleaned_date)::date AS week_beginning,
       COUNT(*) AS incidents
FROM tutorial.sf_crime_incidents_2014_01
GROUP BY 1
ORDER BY 1
```

* 想要知道現在的時間可以用下面的函數，使用這些函數時可以不加上 `FROM` 子句
    * `CURRENT_DATE`, `CURRENT_TIME`, `CURRENT_TIMESTAMP`, `LOCALTIME`, `LOCALTIMESTAMP`, `NOW()`
```SQL
SELECT CURRENT_DATE AS date,
       CURRENT_TIME AS time,
       CURRENT_TIMESTAMP AS timestamp,
       LOCALTIME AS localtime,
       LOCALTIMESTAMP AS localtimestamp
       NOW() AS now
```

* 用 `AT TIME ZONE 時區` 來指名時間是用哪個時區的時間
```SQL
SELECT CURRENT_TIME AS time,
       CURRENT_TIME AT TIME ZONE 'PST' AS time_pst
```

### Q: Write a query that shows exactly how long ago each indicent was reported. Assume that the dataset is in Pacific Standard Time (UTC-8).
```SQL
SELECT incidnt_num,
       cleaned_date,
       NOW() AT TIME ZONE 'PST' AS now,
       NOW() AT TIME ZONE 'PST' - cleaned_date AS ago
FROM tutorial.sf_crime_incidents_2014_01
```

* `COALESCE()` 可以指定用來取代 NULL 時的值
    * `COALESCE(某欄位, 當某欄位是 NULL 時要用什麼東西取代)`
```SQL
SELECT incidnt_num,
       descript,
       COALESCE(descript, 'No Description')
FROM tutorial.sf_crime_incidents_2014_01
ORDER BY descript DESC
```

* subquery = inner query = nested query
```SQL
SELECT sub.*
FROM (
    SELECT *
    FROM tutorial.sf_crime_incidents_2014_01
    WHERE day_of_week = 'Friday'
) sub
WHERE sub.resolution = 'NONE'
```

### Q: Write a query that selects all Warrant Arrests from the `tutorial.sf_crime_incidents_2014_01` dataset, then wrap it in an outer query that only displays unresolved incidents.
```SQL
SELECT sub.*
FROM (
    SELECT *
    FROM tutorial.sf_crime_incidents_2014_01
    WHERE descript = 'WARRANT ARREST'
) sub
WHERE sub.resolution = 'NONE'
```

* 想知道平均每天事件發生了幾次
    1. 先寫 inner query 統計每天事件發生的次數
    2. 在 outer query 中計算每個 day of week 的平均值
```SQL
SELECT LEFT(sub.date, 2) AS cleaned_month,
       sub.day_of_week,
       AVG(sub.incidents) AS average_incidents
FROM (
    SELECT day_of_week,
           date,
           COUNT(incidnt_num) AS incidents
    FROM tutorial.sf_crime_incidents_2014_01
    GROUP BY 1, 2
) sub
GROUP BY 1, 2
ORDER BY 1, 2
```

### Q: Write a query that displays the average number of monthly incidents for each category. Hint: use `tutorial.sf_crime_incidents_cleandate` to make your life a litte easier.
    1. 先寫個 inner query 計算每個月每個類別發生的次數
    2. 在 outer query 中計算每個類別的平均數
```SQL
SELECT sub.category,
       AVG(sub.incidents) AS avg_incidents_per_month
FROM (
    SELECT EXTRACT('month' FROM cleaned_date) AS month,
           category,
           COUNT(1) AS incidents
    FROM tutorial.sf_crime_incidents_2014_01
    GROUP BY 1, 2
) sub
GROUP BY 1
```

* subqueries 可以用在 conditional logic 裡面
    * 就是說可以用在 `WHERE`, `JOIN`/`ON`, `CASE` 子句裡
    & subqueries 寫在 conditional logic 裡面的時候，不需要加上 alias

* 找出資料集中，第一天的所有欄位
```SQL
SELECT *
FROM tutorial.sf_crime_incidents_2014_01
WHERE Date = (
    SELECT MIN(date)
    FROM tutorial.sf_crime_incidents_2014_01
)
```

* 大部分的 subqueries 寫在 conditional logic 裡面的時候，只傳回一個結果，但是如果是寫在 `IN` 裡面，那會傳回很多結果 (回傳的結果是一個 list)
```SQL
SELECT *
FROM tutorial.sf_crime_incidents_2014_01
WHERE Date IN (
    SELECT date
    FROM tutorial.sf_crime_incidents_2014_01
    ORDER BY date
    LIMIT 5
)
```

* 和上面的結果一樣，不過這次是把 subquery 寫在 `JOIN`/`ON` 裡面
```SQL
SELECT *
FROM tutorial.sf_crime_incidents_2014_01
JOIN (
    SELECT date
    FROM tutorial.sf_crime_incidents_2014_01,
    ORDER BY date
    LIMIT 5
) sub
ON incidents.date = sub.date
```

1. subquery 計算每天有多少事件發生
2. outer query 中把事件按照發生次數的多寡降冪排序
```SQL
SELECT incidents.*,
       sub.incidents AS incidents_that_day
FROM tutorial.sf_crime_incidents_2014_01
JOIN (
    SELECT date,
    COUNT(incidnt_num) AS incidents
    FROM tutorial.sf_crime_incidents_2014_01
    GROUP BY 1
) sub
ON incidents.date = sub.date
ORDER BY sub.incidents DESC, time
```

### Q: Write a query that displays all rows from the three categories with the fewest incidents reported.
    1. subquery 先計算每個 category 發生的次數，然後依照發生次數升冪排序，只選出最低發生次數的前三個
    2. outer query 中把最低發生次數的前三個 category 的全部結果顯示出來
```SQL
SELECT incidents.*
FROM sub.sf_crime_incidents_2014_01
JOIN (
    SELECT category,
           COUNT(*) AS count
    FROM tutorial.sf_crime_incidents_2014_01
    GROUP BY 1
    ORDER BY 2
    LIMIT 3
) sub
ON sub.category = incidents.category
```

* 用 aggregation function 計算每個月收到投資的公司和被併購的公司
    * 不用 subquery 時會比較慢
        * 因為 acquisitions 一個月中每一天會和 investments 中的每天做 join，產生 30x30 的新結果 (如果一個月是 30 天) 表格會變很大，跑 `COUNT(DISTINCT)` 的時候就很花時間
```SQL
SELECT COALESCE(acquisitions.acquired_month, investments.funded_month) AS month,
       COUNT(DISTINCT acquisitions.company_permalink) AS companies_acquired,
       COUNT(DISTINCT investments.company_permalink) AS investments
FROM tutorial.crunchbase_acquisitions acquisitions
FULL JOIN tutorial.crunchbase_investments investments
ON acquisitions.acquired_month = investments.funded_month
GROUP BY 1
```

* 下面的 query 有 7,414 rows
```SQL
SELECT COUNT(*)
FROM tutorial.crunchbase_acquisitions
```

* 下面的 query 有 83,893 rows
```SQL
SELECT COUNT(*)
FROM tutorial.crunchbase_investments
```

* 用 `FULL JOIN` 的話產生 6,237,396 rows，所以表格就變很大
    * 如果還要對這個表格做 `COUNT(DISTINCT)` 就很花時間
```SQL
SELECT COUNT(*)
FROM tutorial.crunchbase_acquisitions acquisitions
FULL JOIN tutorial.crunchbase_investments investments
ON acquisitions.acquired_month = investments.funded_month
```

* 可以先分別 aggregate 兩個表格，然後再 join 再一起，這樣跑得比較快
    * 用 `FULL JOIN` 是因為有可能一個 table 在某一個月有資料，另一個 table 在同一個月沒資料
    * 用 `COALESCE` 是因為沒有被併購的話 acquisition 就不會有該月的資料
    * 因為 `COUNT(DISTINCT)` 是先分別對兩個 table 做，所以跑起來會比較快
```SQL
SELECT COALESCE(acquisitions.month, investments.month) AS month,
       acquisitions.companies_acquired,
       investments.companies_rec_investment
FROM (
    SELECT acquired_month AS month,
           COUNT(DISTINCT company_permalink) AS companies_acquired
    FROM tutorial.crunchbase_acquisitions
    GROUP BY 1
) acquisitions
FULL JOIN (
    SELECT funded_month AS month,
           COUNT(DISTINCT company_permalink) AS companies_rec_investment
    FROM tutorial.crunchbase_investments
    GROUP BY 1
) investments
ON acquisitions.month = investments.month
ORDER BY 1 DESC
```

### Q: Write a query that counts the number of companies founded and acquired by quarter starting in Q2012. Create the aggregations in two separate queries, then join them.
```SQL
SELECT COALESCE(companies.quarter, acquisitions.quarter) AS quarter,
       companies.companies_founded,
       acquisitions.companies_acquired
FROM (
    SELECT founded_quarter AS quarter,
           COUNT(permalink) AS companies_founded
    FROM tutorial.crunchbase_companies
    WHERE founded_year >= 2012
    GROUP BY 1
) companies
LEFT JOIN (
    SELECT acquired_quarter AS quarter,
           COUNT(DISTINCT company_permalink) AS companies_acquired
    FROM tutorial.crunchbase_acquisitions
    WHERE acquired_year >= 2012
    GROUP BY 1
) acquisitions
ON companies.quarter = acquisitions.quarter
ORDER BY 1
```

* 用 `UNION ALL` 先把一個被分成兩個部分的表格合而為一
```SQL
SELECT *
FROM tutorial.crunchbase_investments_part1
UNION ALL
SELECT *
FROM tutorial.crunchbase_investments_part2
```

* 把合併寫在 subquery 中，然後對合併後的表格做 query
```SQL
SELECT COUNT(*) AS total_rows
FROM (
    SELECT *
    FROM tutorial.crunchbase_investments_part1
    UNION ALL
    SELECT *
    FROM tutorial.crunchbase_investments_part2
) sub
```

### Q: Write a query that ranks investors from the combined dataset above by the total number of investments they have made.
```SQL
SELECT investor_name,
       COUNT(*) AS investments
FROM (
    SELECT *
    FROM tutorial.crunchbase_investments_part1
    UNION ALL
    SELECT *
    FROM tutorial.crunchbase_investments_part2
) sub
GROUP BY 1
ORDER BY 2 DESC
```

### Q: Write a query that does the same thing as in the previous problem, except only for companies that are still operating. Hint: operating status is in `tutorial.crunchbase_companies`.
```SQL
SELECT investments.investor_name,
       COUNT(investments.*) AS investments
FROM tutorial.crunchbase_companies companies
JOIN (
    SELECT *
    FROM tutorial.crunchbase_investments_part1
    UNION ALL
    SELECT *
    FROM tutorial.crunchbase_investments_part2
) investments
ON investments.company_permalink = companies.permalink
WHERE companies.status = 'operating'
GROUP BY 1
ORDER BY 2 DESC
```

## Window functions
* A window function performs a calculation across a set of table rows that are somehow related to the current row. This is comparable to the type of calculation that can be done with an aggregate function. But unlike regular agreegate functions, use of a window function does not cause rows to become grouped into a single output row - the rows retain their separate identities. Behind the scenes, the window function is able to access more than just the current row of the query result.


* 用 window function 來計算 running total
    * 用 `OVER` 表示這是一個 window function
    * 把整個 dataset 的資料，依照 start_time 排序後，對 duration_seconds 做加總
        * 第一 row 的 `SUM(duration_seconds)` = 1st row 的 duration_seconds
        * 第二 row 的 `SUM(duration_seconds)` = 1st row 的 duration_seconds + 2nd row 的 duration_seconds
        * 第 N row 的 `SUM(duration_seconds)` = \sum_{i=1}^{N} duration_seconds
```SQL
SELECT duration_seconds,
       SUM(duration_seconds) OVER (ORDER BY start_time) AS running_total
FROM tutorial.dc_bikeshare_q1_2012
```

* 如果是要針對區間做 window functionn 就用 `PARTITION BY`
    1. 先把資料依照 start_terminal 做區分並排序
    2. 相同的 start_terminal 就依照 start_time 排序
    3. `SUM(duration_seconds)` 是在同一個 start_terminal 區域內，從先前的 row 開始加總到 current row
    4. 每一次 start_terminal 改變時，running_total 就會從新開始計算
```SQL
SELECT start_terminal,
       duration_seconds,
       SUM(duration_seconds) OVER (PARTITION BY start_terminal ORDER BY start_time) AS running_total
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
```

* 如果沒有 `ORDER BY` 結果會不同
```SQL
SELECT start_terminal,
       duration_seconds,
       SUM(duration_seconds) OVER (PARTITION BY start_terminal) AS start_terminal_total
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
```

* 不能再同一個 query 中同時使用 window function 和 aggregation
* 不能在 `GROUP BY` 子句中使用  window function

### Q: Write a query modification of the above example query that shows the duration of each ride as a percentage of the total time accrued by riders from each start_terminal.
```SQL
SELECT start_terminal,
       duration_seconds,
       SUM(duration_seconds) OVER (PARTITION BY start_terminal) AS start_terminal_sum,
       (duration_seconds / SUM(duration_seconds) OVER (PARTITION BY  start_terminal)) * 100 AS pct_of_total_time
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
ORDER BY 1, 4 DESC
```

### 在 window functions 中可以用 `SUM`, `COUNT`, `AVG`
```SQL
SELECT start_terminal,
       duration_seconds,
       SUM(duration_seconds) OVER (PARTITION BY start_terminal) AS running_total,
       COUNT(duration_seconds) OVER (PARTITION BY start_terminal) AS running_count,
       AVG(duration_seconds) OVER (PARTITION BY start_terminal) AS running_avg
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
```

* 上面的 query 也可以加上 `ORDER BY`
```SQL
SELECT start_terminal,
       duration_seconds,
       SUM(duration_seconds) OVER (PARTITION BY start_terminal ORDER BY start_time) AS running_total,
       COUNT(duration_seconds) OVER (PARTITION BY start_terminal ORDER BY start_time) AS running_count,
       AVG(duration_seconds) OVER (PARTITION BY start_terminal ORDER BY start_time) AS running_avg
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
```

### Q: Write a query that shows a running total of the duration of bike rides (similar to the last example), but grouped by end_terminal, and with ride duration sorted in descending order.
```SQL
SELECT end_terminal,
       duration_seconds,
       SUM(duration_seconds) OVER (PARTITION BY end_terminal ORDER BY duration_seconds DESC) AS running_total
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
```

* `ROW_NUMBER()` 把 row 加上編號，從 1 開始，不需要參數
    * 只有 `ORDER BY` 那就是用整個資料集來對每個 row 加上編號
```SQL
SELECT start_terminal,
       start_time,
       duration_seconds,
       ROW_NUMBER() OVER (ORDER BY start_time) AS row_number
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
```

* 加上 `PARTITION BY` 會對每個 partition 作編號
    * 每一個新的 partition 就重新編號
```SQL
SELECT start_terminal,
       start_time,
       duration_seconds,
       ROW_NUMBER() OVER (PARTITION BY start_terminal ORDER BY start_time) AS row_number
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
```

* `RANK()` 同樣的 start_time 會有相同的 rank, 下一個 start_time 的 rank 就會跳到下一個數
    * 所以 `RANK()` 的編號不是連續的，例如 1,2,2,2,5,6 (跳過 3,4)
```SQL
SELECT start_terminal,
       duration_seconds,
       RANK() OVER (PARTITION BY start_terminal ORDER BY start_time) AS rank
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
```

* `DENSE_RANK()` 和 `RANK` 一樣，差別是 rank 的編號是連續的
    * 例如 1,2,2,2,3,4

### Q: Write a query that shows the 5 longest rides from each starting terminal, ordered by terminal, and longest to shortest rides within each terminal. Limit to rides that occurred before Jan. 8, 2012.
```SQL  
SELECT *
FROM (
    SELECT start_terminal,
           start_time,
           duration_seconds AS trip_time,
           RANK() OVER (PARTITION BY start_terminal ORDER BY duration_seconds DESC) AS rank
    FROM tutorial.dc_bikeshare_q1_2012
    WHERE start_time < '2012-01-08'
) sub
WHERE sub.rank <= 5
```

* `NTILE(切成幾個 tiles)` 可以知道每個 row 屬於哪個 percentile (or quartile)
    * `ORDER BY` 用來指定依照哪個欄位來決定 quartiles
    * 如果 row 的數目比要切的 tile 數目小，會有問題
```SQL 
SELECT start_terminal,
       duration_seconds,
       NTILE(4) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS quartile,
       NTILE(5) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS quintile,
       NTILE(100) (PARTITION BY start_terminal ORDER BY duration_seconds) AS percentile
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
ORDER BY start_terminal, duration_seconds
```

### Q: Write a query that shows only the duration of the trip and the percentile into which that duration falls (across the entire dataset - not partitioned by terminal).
```SQL 
SELECT duration_seconds,
       NTILE(100) OVER (ORDER BY duration_seconds) AS percentile
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
ORDER BY 1 DESC
```

* `LAG(欄位, N)` 從前面的 N row 抓結果，`LEAD(欄位, N)` 從後面的 N row 抓結果
```SQL
SELECT start_terminal,
       duration_seconds,
       LAG(duration_seconds, 1) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS lag,
       LEAD(duration_seconds, 1) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS lead
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
ORDER BY start_terminal, duration_seconds
```

* `LAG()` 和 `LEAD()` 可以用來計算兩個 row 之間的差別
    * 用 `LAG()` 的話第一 row 會有 null, 因為 1st row 沒有前一個 row 可以抓結果
    * 用 `LEAD()` 的話最後一 row 會有 null, 因為最後一 row 沒有後面的 row 可以抓結果
```SQL
SELECT start_terminal,
       duration_seconds,
       duration_seconds - LAG(duration_seconds, 1) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS difference
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
ORDER BY start_terminal, duration_seconds
```

* 可以藉由寫在 subquery 的方式把 null 移除
```SQL
SELECT *
FROM (
    SELECT start_terminal,
           duration_seconds,
           duration_seconds - LAG(duration_seconds, 1) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS difference
    FROM tutorial.dc_bikeshare_q1_2012
    WHERE start_time < '2012-01-08'
    ORDER BY start_terminal, duration_seconds
) sub
WHERE sub.difference IS NOT NULL
```

* 可以對 window functions 建立 alias
* 例如下面這個 query 中 `(PARTITION BY start_terminal ORDER BY duration_seconds)` 可以建立 alias 然後改寫成更短一些
```SQL
SELECT start_terminal,
       duration_seconds,
       NTILE(4) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS quartile,
       NTILE(5) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS quintile,
       NTILE(100) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS percentile
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
ORDER BY start_terminal, duration_seconds
```

* 上面的 query 可以改寫成這樣
    * 用 `WINDOW` 子句來幫 window function 建立 alias
    * `WINDOW` 子句要放在 `WHERE` 子句的後面
```SQL
SELECT start_terminal,
       duration_seconds,
       NTILE(4) OVER ntile_window AS quartile,
       NTILE(5) OVER ntile_window AS quintile,
       NTILE(100) OVER ntile_window AS percentile
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
WINDOW ntile_window AS (PARTITION BY start_terminal ORDER BY duration_seconds)
```

* time series data 如果先篩選出時間範圍，可以加快 query 的速度
```SQL
SELECT *
FROM benn.sample_event_table
WHERE event_date >= '2014-03-01'
AND event_date < '2014-04-01'
```

* `LIMIT` 可以加快速度，但是對 aggregation 用 `LIMIT` 沒有辦法加速
    * 因為 aggregation 會先對整個表格做，然後才是 `LIMIT` 輸出
```SQL
SELECT COUNT(*)
FROM benn.sample_event_table
LIMIT 100
```

* 可以在 subquery 中用 `LIMIT` 加速，但是跑出來的結果就會不一樣
    * 通常只是用來讓結果跑快一點，來看看 query 寫得對不對，但是不是用來產生正確答案
```SQL
SELECT COUNT(*)
FROM (
    SELECT *
    FROM benn.sample_event_table
    LIMIT 100
) sub
ORDER BY start_terminal, duration_seconds
```

* `JOIN` 也會花費大量的時間
```SQL
SELECT teams.conference AS conference,
       players.school_name,
       COUNT(1) AS players
FROM benn.college_football_players players
JOIN benn.college_football_teams teams
ON teams.school_name = players.school_name
GROUP BY 1, 2
```

* 可以考慮先對 players 表格做 aggregation
```SQL
SELECT players.school_name
       COUNT(*) AS players
FROM benn.college_football_players
GROUP BY 1
```

* 把上面的寫在 subquery 中，再做 `JOIN`
```SQL
SELECT teams.conference,
       sub.*
FROM (
    SELECT players.school_name,
           COUNT(*) AS players
    FROM benn.college_football_players players
    GROUP BY 1
) sub
JOIN benn.college_football_teams teams
ON teams.school_name = sub.school_name
```

* `EXPLAIN` 放在 query 的最開頭，可以顯示執行 query 花了多久的時間
```SQL
EXPLAIN
SELECT *
FROM benn.sample_event_table
WHERE event_date >= '2014-03-01'
AND event_date < '2014-04-01'
LIMIT 100
```

#### 這邊開始是 Pivoting rows to columns

* aggregate data 來看 number of players of each year in each conference
```SQL
SELECT teams.conference AS conference,
       players.year,
       COUNT(1) AS players
FROM benn.college_football_players players
JOIN benn.college_football_teams teams
ON teams.school_name = players.school_name
GROUP BY 1, 2
ORDER BY 1, 2
```

* 把上面的 query 寫到 subquery 裡面
```SQL
SELECT *
FROM (
    SELECT teams.conference AS conference,
           players.year,
           COUNT(1) AS players
    FROM benn.college_football_players players
    JOIN benn.college_football_teams teams
    ON teams.school_name = players.school_name
    GROUP BY 1, 2
) sub
```

* 再來是把上面的 query 的結果變成不同年份在不同欄位，這樣就能把結果 pivot 了
    * 這個結果是依照 conference 的字母順序排列
```SQL
SELECT conference,
       SUM(CASE WHEN year = 'FR' THEN players ELSE NULL END) AS fr,
       SUM(CASE WHEN year = 'SO' THEN players ELSE NULL END) AS so,
       SUM(CASE WHEN year = 'JR' THEN players ELSE NULL END) AS jr,
       SUM(CASE WHEN year = 'SR' THEN players ELSE NULL END) AS sr
FROM (
    SELECT teams.conference AS conference,
           players.year,
           COUNT(1) AS players
    FROM benn.college_football_players players
    JOIN benn.college_football_teams teams
    ON teams.school_name = players.school_name
    GROUP BY 1, 2
) sub
GROUP BY 1
ORDER BY 1
```

* 再把上面的 query 修改一下，使結果依照 total player 順序由大到小排列
```SQL
SELECT conference,
       SUM(players) AS total_players,
       SUM(CASE WHEN year = 'FR' THEN players ELSE NULL END) AS fr,
       SUM(CASE WHEN year = 'SO' THEN players ELSE NULL END) AS so,
       SUM(CASE WHEN year = 'JR' THEN players ELSE NULL END) AS jr,
       SUM(CASE WHEN year = 'SR' THEN players ELSE NULL END) AS sr
FROM (
    SELECT teams.conference AS conference,
           players.year,
           COUNT(1) AS players
    FROM benn.college_football_players players
    JOIN benn.college_football_teams teams
    ON teams.school_name = players.school_name
    GROUP BY 1, 2
) sub
GROUP BY 1
ORDER BY 2 DESC
```

#### 這邊開始是 Pivoting columns to rows
```SQL
SELECT *
FROM tutorial.worldwide_eqrthquakes
```

* 建立一個新的表格，上一個 query 中的 column 在新表格中變成 row
```SQL
SELECT year
FROM (VALUES (2000),(2001),(2002),(2003),(2004),(2005),(2006),
             (2007),(2008),(2009),(2010),(2011),(2012)) v(year)
```

* 把兩個表格 `JOIN` 在一起
    * 但是這樣子每一個 worldwide_earthquakes 會重複 13 次
```SQL
SELECT years.*,
       earthquakes.*
FROM tutorial.worldwide_eqrthquakes earthquakes
CROSS JOIN (
    SELECT year
    FROM (VALUES (2000),(2001),(2002),(2003),(2004),(2005),(2006),
                 (2007),(2008),(2009),(2010),(2011),(2012)) v(year)
) years
```

* 用 `CASE` 取出正確的結果
```SQL
SELECT years.*,
       earthquakes.magnitude,
       CASE year
           WHEN 2000 THEN year_2000
           WHEN 2001 THEN year_2001
           WHEN 2002 THEN year_2002
           WHEN 2003 THEN year_2003
           WHEN 2004 THEN year_2004
           WHEN 2005 THEN year_2005
           WHEN 2006 THEN year_2006
           WHEN 2007 THEN year_2007
           WHEN 2008 THEN year_2008
           WHEN 2009 THEN year_2009
           WHEN 2010 THEN year_2010
           WHEN 2011 THEN year_2011
           WHEN 2012 THEN year_2012
           ELSE NULL
       END AS number_of_eqrthquakes
FROM tutorial.worldwide_eqrthquakes earthquakes
CROSS JOIN (
    SELECT year
    FROM (VALUES (2000),(2001),(2002),(2003),(2004),(2005),(2006),
                 (2007),(2008),(2009),(2010),(2011),(2012)) v(year)
) years
```
