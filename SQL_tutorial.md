## Section 1. Introduction to sQL
## Section 2. Querying Data
## Section 3. Sorting Data
## Section 4. Filtering Data
## Section 5. Conditional Expressions
## Section 6. Joining Multiple Tables
## Section 7. Aggregate Functions
## Section 8. Grouping Data
## Section 9. SET Operators
## Section 10. Subquery

## Section 11. Modifying data

插入一個 row
INSERT INTO table1 (column1, column2, ...)
VALUES (value1, value2, ...);

如果不寫 (column1, column2, ...) 那 (value1, value2, ...) 的順序就會照表格中 column 的順序
如果漏寫某個 column, 那 value 就會用 default value

插入多 rows
INSERT INTO table1
VALUES (value1, value2, ...),
       (value1, value2, ...),
       (value1, value2, ...),
       ...;




## Section 12. Working with table structures

* 用 `CREATE TABLE` 建立表格
    * column_constraint 像是 `NOT NULL`, `UNIQUE`, `PRIMARY KEY` 之類
    * 好幾個 column 有相同的 column_constraint 那就可以寫在 table_constraint 裡面
```SQL
CREATE TABLE table_name(
    column_name_1 data_type default_value column_constraint,
    column_name_2 data_type default_value column_constraint,
    ...,
    table_constraint
);
```

* `AUTO_INCREMENT` 表示當沒有提供數值的時候，新加入資料時 course_id 就會自動加一
```SQL
CREATE TABLE courses (
    course_id INT AUTO_INCREMENT PRIMARY KEY,
    course_name VARCHAR(50) NOT NULL
);
```

* employee_id, course_id 都要設為 `PRIMARY KEY` 時就可以寫在 table_constraint 裡面
```SQL
CREATE TABLE trainings (
    employee_id INT,
    course_id INT,
    taken_date DATE,
    PRIMARY KEY (employee_id, course_id)
);

* 用 `ALTER TABLE` 改變表格的結構
    * `ADD` 增加新欄位
    * `MODIFY` 修改欄位的屬性，像是 default value 或是 constraint 之類的
       * 要對沒有資料的表格做 `MODIFY`，如果對有資料的表格做 `MODIFY` 那可能會造成失去資料
    * `DROP` 刪除欄位
    * `AFTER` 表示新的欄位要加在哪個欄位後面，預設是最後的一個欄位後面
```SQL
ALTER TABLE table_name
ADD new_column data_type column_constraint [AFTER existing_column];
```
```SQL
ALTER TABLE courses ADD credit_hours INT NOT NULL;
```
```SQL
ALTER TABLE courses
ADD fee NUMERIC (10, 2) AFTER course_name,
ADD max_limit INT AFTER course_name;
```
```SQL
ALTER TABLE table_name
MODIFY column_definition;
```
```SQL
ALTER TABLE courses
MODIFY fee NUMERIC (10, 2) NOT NULL;
```
```SQL
ALTER TABLE table_name
DROP column_name_1,
DROP column_name_2,
...
```

* `DROP COLUMN` 可以刪除多個欄位
```SQL
ALTER TABLE courses DROP COLUMN fee;
```
```SQL
ALTER TABLE courses
DROP COLUMN max_limit,
DROP COLUMN credit_hours;
```

* 用 `DROP TABLE` 來刪除表格
    * 要刪除不存在的表格時，加上 `IF EXISTS` 可以防止報錯
```SQL
DROP TABLE [IF EXISTS] table_name;
```
```SQL
CREATE TABLE emergency_contacts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    relationship VARCHAR(50) NOT NULL,
    employee_id INT NOT NULL
);
DROP TABLE emergency_contacts;
```
```SQL
DROP TABLE table_name1,table_name2,...;
```

* 用 `DELETE` 刪除表格內的資料，但是當資料很大的時候，用 `TRUNCATE TABLE` 會比較快
```SQL
TRUNCATE TABLE table_name;
```
* MySQL, PostgreSQL 可以省略 `TABLE` 關鍵字
```SQL
TRUNCATE table_name;
```
```SQL
TRUNCATE TABLE table_name1, table_name2, ...;
```
```SQL
CREATE TABLE big_table (
    id INT AUTO_INCREMENT PRIMARY KEY,
    val INT
);

INSERT INTO big_table (val)
VALUES (RAND(100000));

TRUNCATE TABLE big_table;
```
```SQL
DELIMITER $$
CREATE PROCEDURE load_big_table_data(IN num int)
BEGIN
  DECLARE counter int default 0;

  WHILE counter < num DO
    INSERT INTO big_table(val)
    VALUES (RAND(100000));
  END WHILE
END$$

CALL load_big_table_data(10000);

TRUNCATE TABLE big_table;
```

## Section 13. Constraints
```SQL
```
```SQL
```
```SQL
```

