---
author: cosin
pubDatetime: 2023-05-21T10:18:07+08:00 
title: postgres学习记录
postSlug: ""
featured: false
draft: false
tags:
  - color-schemes
  - docs
ogImage: ""
description:
  记录学习postgres过程
---
# severless postgres
为了简化学习步骤，不用本地或docker部署吗，使用neon.tech的postgres。使用旧版本psql连接出现问题，解决方法如下[Connect from old clients](https://neon.tech/docs/connect/connectivity-issues)。
[Debian安装新版本方法](https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart)
```shell
sudo apt update
sudo apt install postgresql postgresql-contrib
```
# posgres 指令
```shell
# help
\h
# quit
\q
# exec sql
\i basics.sql
# 查看psql命令列表。
\？
# 列出所有数据库。
\l
# 连接其他数据库。
\c [database_name]
# 列出当前数据库的所有表格。
\d
# 列出某一张表格的结构。
\d [table_name]
# 列出所有用户。
\du
# 打开文本编辑器。
\e
# 列出当前数据库和连接的信息。
\conninfo
```

# 创建table和注释
```shell
CREATE TABLE weather (
    city            varchar(80),
    temp_lo         int,           -- low temperature
    temp_hi         int,           -- high temperature
    prcp            real,          -- precipitation
    date            date
);
```
"--"代表注释

# SELECT
WHERE 的內容是一個布林（truth value）表示式，而只有在其運算值為真（true）時，該列才會被回傳。
```sql
SELECT DISTINCT city
    FROM weather
    ORDER BY city;
```

# 自我交叉查詢
```sql
SELECT W1.city, W1.temp_lo AS low, W1.temp_hi AS high,
    W2.city, W2.temp_lo AS low, W2.temp_hi AS high
    FROM weather W1, weather W2
    WHERE W1.temp_lo < W2.temp_lo
    AND W1.temp_hi > W2.temp_hi;
```

# 子查询
```sql
SELECT city FROM weather
    WHERE temp_lo = (SELECT max(temp_lo) FROM weather);
```

# where 和 having 过滤
差异在：WHERE 會在合併和彙總計算之前進行選擇資料的動作（也就是它控制著，哪些資料需要被彙總計算）；而 HAVING 是在合併及彙整計算之後，才進行過濾資料的動作。所以，在 WHERE 條件式當中，絕不可以使用彙整運算式（max, min）；另一方面，HAVING 條件式總是使用彙整運算式。（嚴格來說，你也可以不在 HAVING 條件式中使用彙整運算式，但很少人這樣使用，通常就會改寫到 WHERE 條式件當中，那
```sql
SELECT city, max(temp_lo)
    FROM weather
    WHERE city LIKE 'S%'     
    GROUP BY city
    HAVING max(temp_lo) < 40;
```

# update
```sql
UPDATE weather
    SET temp_hi = temp_hi - 2,  temp_lo = temp_lo - 2
    WHERE date > '1994-11-28';
```
# delete
```sql
DELETE FROM weather WHERE city = 'Hayward';
```
删除整张表
```sql
DELETE FROM tablename;
```

# view 视图
```sql
CREATE VIEW myview AS
    SELECT city, temp_lo, temp_hi, prcp, date, location
        FROM weather, cities
        WHERE city = name;

SELECT * FROM myview;
```

# 外键约束
当你想要确保在weather表中的每一行数据的city都是cities中的city时，使用外键就能很好处理此问题。
```sql
CREATE TABLE cities (
        city     varchar(80) primary key,
        location point
);

CREATE TABLE weather (
        city      varchar(80) references cities(city),
        temp_lo   int,
        temp_hi   int,
        prcp      real,
        date      date
);
```
```sql
INSERT INTO weather VALUES ('Berkeley', 45, 53, 0.0, '1994-11-28');
```
# Transaction 事务
savepoint 存档点，可以使用rollback局部回滚。
```sql
BEGIN;
UPDATE accounts SET balance = balance - 100.00
    WHERE name = 'Alice';
SAVEPOINT my_savepoint;
UPDATE accounts SET balance = balance + 100.00
    WHERE name = 'Bob';
-- oops ... forget that and use Wally's account
ROLLBACK TO my_savepoint;
UPDATE accounts SET balance = balance + 100.00
    WHERE name = 'Wally';
COMMIT;
```

# window 窗函数
```sql
CREATE TABLE empsalary (
  depname   varchar(20),
  empno     int,
  salary    int
);
```
```sql
INSERT INTO empsalary VALUES ('develop', 11, 5200);
INSERT INTO empsalary VALUES ('develop', 7, 4200);
INSERT INTO empsalary VALUES ('develop', 9, 4500);
INSERT INTO empsalary VALUES ('develop', 8, 6000);
INSERT INTO empsalary VALUES ('develop', 10, 5200);
INSERT INTO empsalary VALUES ('personnel', 5, 3500);
INSERT INTO empsalary VALUES ('personnel', 2, 3900);
INSERT INTO empsalary VALUES ('sales', 3, 4800);
INSERT INTO empsalary VALUES ('sales', 1, 5000);
INSERT INTO empsalary VALUES ('sales', 4, 4800);
```
```sql
SELECT depname, empno, salary, avg(salary) OVER (PARTITION BY depname) FROM empsalary;
-- 不等价
SELECT depname, empno, salary, avg(salary) FROM empsalary GROUP BY depname, empno, salary;
```

# 表继承
```sql
CREATE TABLE cities (
  name       text,
  population real,
  altitude   int     -- (in ft)
);

CREATE TABLE capitals (
  state      char(2)
) INHERITS (cities);
```
```sql
-- cities & capitals
SELECT name, altitude
  FROM cities
  WHERE altitude > 500;
-- only cities
SELECT name, altitude
    FROM ONLY cities
    WHERE altitude 
>
 500;
```
