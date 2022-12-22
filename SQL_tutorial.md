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

* `INSERT` 插入一個或多個 row
  * 如果不寫 (column1, column2, ...) 那 (value1, value2, ...) 的順序就會照表格中 column 的順序
  * 如果漏寫某個 column, 那 value 就會用 default value
```SQL
INSERT INTO table1 (column1, column2, ...)
VALUES (value1, value2, ...);
```
* 插入新的資料並顯示結果
```SQL
INSERT INTO dependents (first_name, last_name, relationship, employee_id)
VALUES ('Dustin', 'Johnson', 'Child', 178);

SELECT *
FROM dependents
WHERE employee_id = 178;
```
* 插入多 rows
```SQL
INSERT INTO table1
VALUES (value1, value2, ...),
       (value1, value2, ...),
       (value1, value2, ...),
       ...;
```
* 插入新的資料並顯示結果
```SQL
INSERT INTO dependents (first_name, last_name, relationship, employee_id)
VALUES ('Carmeron', 'Bell', 'Child', 192),
       ('Michelle', 'Bell', 'Child', 192);

SELECT *
FROM dependents
WHERE employee_id = 192;
```
* 從別的表格中抓資料來插入
```SQL
INSERT table1 (column1, column2)
SELECT column1, column2
FROM table2
WHERE condition1;
```
* 從 dependents 表格中插入新的資料到 dependents_archive 表格並顯示結果
```SQL
INSERT INTO dependents_archive
SELECT *
FROM dependents

SELECT *
FROM dependents_archive;
```

* `UPDATE` 用來更新資料
  * `WHERE` 是選用的，用來決定哪些 rows 的資料會被更新
```SQL
UPDATE table_name
SET column1 = value1,
    column2 = value2
WHERE condition;
```
* 修改 employee_id 是 192 的 last name 並顯示結果
```SQL
UPDATE employees
SET last_name = 'Lopez'
WHERE employee_id = 192

SELECT employee_id, first_name, last_name
FROM employees
WHERE employee_id = 192;

UPDATE dependents
SET last_name = 'Lopez'
WHERE employee_id = 192;

SELECT *
FROM dependents
WHERE employee_id = 192
```
* 使用 subquery 來更新所有 last_name 的資料
```SQL
UPDATE dependents
SET last_name = (
    SELECT last_name
    FROM employees
    WHERE employee_id = dependents.employee_id
);
```

* `DELETE` 刪除表格內一個或多個 rows
  * `WHERE` 指定了哪些 rows 會被刪除，沒有 `WHERE` 的話就會刪除全部的 rows
```SQL
DELETE FROM table_name
WHERE condition;
```
```SQL
DELETE FROM dependents
WHERE dependent_id = 16;

SELECT COUNT(*)
FROM dependents
WHERE dependent_id = 16
```
```SQL
DELETE FROM dependents
WHERE employee_id IN (100, 101, 102);
```
```SQL
DELETE FROM employees
WHERE employee_id = 192;

DELETE FROM dependents
WHERE employee_id = 192;
```

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
* `PRIMARY KEY`
  * 是唯一能 identify 每一個 row 的欄位
  * 由兩個或以上的欄位組成的 primary key 叫做 composite primary key
    * 由兩個或以上的欄位組成的 primary key 時，建立表格時就用 table constraint 
  * 每個表格只有一組 primary key 且不可以是 NULL 或重複的值
```SQL
CREATE TABLE projects (
    project_id INT PRIMARY KEY,
    project_name VARCHAR(255),
    start_date DATE NOT NULL,
    end_date DATE NOT NULL
);
```
```SQL
CREATE TABLE projects (
    project_id INT,
    project_name VARCHAR(255),
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    CONSTRAINT pk_id PRIMARY KEY (project_id)
);
```
```SQL
CREATE TABLE project_assignments (
    project_id INT,
    employee_id INT,
    join_date DATE NOT NULL,
    CONSTRAINT pk_assgn PRIMARY KEY (project_id, employee_id)
);
```
* 先建立沒有 primary key 的表格，在用 `ALTER TABLE` 加入 primary key
```SQL
CREATE TABLE project_milestones (
    milestone_id INT,
    project_id INT,
    milestone_name VARCHAR(100)
);

ALTER TABLE project_milestones
ADD CONSTRAINT pk_milestone_id PRIMARY KEY (milestone_id);
```
* 也可以簡化成
```SQL
ALTER TABLE project_milestones
ADD PRIMARY KEY (milestone_id);
```
* 刪除 primary key
```SQL
ALTER TABLE table_name
DROP CONSTRAINT primary_key_constraint;
```
```SQL
ALTER TABLE table_name
DROP PRIMARY KEY
```
```SQL
ALTER TABLE project_milestones
DROP CONSTRAINT pk_milestone_id
```

* `FOREIGN KEY`
  * 一個表格的 primary key 在另一個表格中就變成了 foreign key
```SQL
CREATE TABLE projects (
    project_id INT AUTO_INCREMENT PRIMARY KEY,
    project_name VARCHAR(255),
    start_date DATE NOT NULL,
    end_date DATE NOT NULL
);

CREATE TABLE project_milestones (
    milestond_id INT AUTO_INCREMENT PRIMARY KEY,
    project_id INT,
    milestone_name VARCHAR(100)
);
```
* 也可以在建立表格時加上 `FOREIGN KEY` table constraint
```SQL
CREATE TABLE project_milestones (
    milestond_id INT AUTO_INCREMENT PRIMARY KEY,
    project_id INT,
    milestone_name VARCHAR(100),
    FOREIGN KEY (project_id) REFERENCES projects (project_id)
);
```
* 幫 `FOREIGN KEY` constraint 加上名字
```SQL
CREATE TABLE project_milestones (
    milestond_id INT AUTO_INCREMENT PRIMARY KEY,
    project_id INT,
    milestone_name VARCHAR(100),
    CONSTRAINT fk_project FOREIGN KEY (project_id) REFERENCES projects (project_id)
);
```
* 用 `ALTER TABLE` 對已經存在的表格加入 `FOREIGN KEY` constraint
```SQL
ALTER TABLE table_1
ADD CONSTRAINT fk_name FOREIGN KEY (fk_key_column) REFERENCES table_2 (pk_key_column)
```
```SQL
ALTER TABLE project_milestones
ADD CONSTRAINT fk_project FOREIGN KEY (project_id) REFERENCES projects (project_id)
```
* 用 `ALTER TABLE` 刪除 `FOREIGN KEY` constraint
```SQL
ALTER TABLE table_name
DROP CONSTRAIN fk_name;
```
```SQL
ALTER TABLE table_name
DROP FOREIGN KEY fk_name;
```
```SQL
ALTER TABLE project_milestones
DROP CONSTRAIN fk_project;
```
* `UNIQUE` constraint 可以防止重複
  * 一個表格中可以有很多 `UNIQUE`
  * 允許 `NULL`
```SQL
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL
);
```
```SQL
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) NOT NULL,
    password VARCHAR(255) NOT NULL,
    CONSTRAINT uc_username UNIQUE (username)
);
```
```SQL
ALTER TABLE users
ADD CONSTRAINT uc_username UNIQUE (username);
```
＊ 加入一個新的欄位，並且有 `UNIQUE` constraint
```SQL
ALTER TABLE users
ADD new_column data_type UNIQUE;
```
```SQL
ALTER TABLE users
ADD email VARCHAR(255) UNIQUE;
```
* 刪除 `UNIQUE` constraint
```SQL
ALTER TABLE users
DROP CONSTRAINT unique_constraint_name;
```
```SQL
ALTER TABLE users
DROP CONSTRAINT uc_username;
```
