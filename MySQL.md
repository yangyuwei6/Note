# Sql图形化界面工具DataGrip

# MySQL基础架构

![img](https://raw.githubusercontent.com/yangyuwei6/photo/main/image/202407191406430.png)

MySQL 主要由下面几部分构成：

- **连接器：** 身份认证和权限相关(登录 MySQL 的时候)。
- **查询缓存：** 执行查询语句的时候，会先查询缓存（MySQL 8.0 版本后移除，因为这个功能不太实用）。
- **分析器：** 没有命中缓存的话，SQL 语句就会经过分析器，分析器说白了就是要先看你的 SQL 语句要干嘛，再检查你的 SQL 语句语法是否正确。
- **优化器：** 按照 MySQL 认为最优的方案去执行。
- **执行器：** 执行语句，然后从存储引擎返回数据。 执行语句之前会先判断是否有权限，如果没有权限的话，就会报错。
- **插件式存储引擎**：主要负责数据的存储和读取，采用的是插件式架构，支持 InnoDB、MyISAM、Memory 等多种存储引擎。

# MySQL基础

## 数据库概念

数据库：DataBase  存储数据的仓库，数据是有组织的存储

数据库管理系统：DataBase management system 操纵和管理数据库的软件

SQL ：操纵关系型数据库的编程语言，定义了一套操纵关系型数据库的统一标准

## MySQL启动与停止

法一：WIN+R 输入service.msc 找到MySQL80 鼠标右键启动与停止

法二：命令行cmd 以管理员身份运行

启动：net start mysql80

停止：net stop mysql80

默认mysql是开机自动启动的

## 客户端连接

数据库的登陆操作

法一：MySQL提供的客户端命令行工具

MySQL 8.0 Command Line Client 输入password：123456（下载时已经设置）

法二：系统自带的命令行执行工具执行指令（cmd）

```
mysql -u 用户名 -p
```

示例：

```
//空格不要省略
mysql -U root -p 
```

使用这种方式需要设置path环境变量：

1.打开mysql下载的bin文件，复制地址，在path中编辑环境变量中新建 粘贴

## 会话

**定义：** 会话是指客户端应用程序与数据库服务器之间建立的连接。在数据库中，会话是客户端与数据库服务器之间进行交互和通信的上下文。

**作用：** 会话包括了一个或多个事务，并提供了执行和管理事务的环境。一个会话可以执行多个事务，或者在不同的时间段内执行一个事务。

**生命周期：** 会话在客户端连接到数据库服务器时开始，并在客户端断开连接或者显式关闭会话时结束。在会话期间，可以通过执行 SQL 语句来启动、提交、回滚事务，并进行其他数据库操作。

## 事务

**定义：** 事务是一组数据库操作，这些操作要么全部成功提交（Commit），要么全部失败回滚（Rollback）。它们被视为一个不可分割的工作单元，要么全部执行成功，要么全部回滚，保证数据的一致性和完整性。

**特性：** 事务具有 ACID 属性，即原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）、持久性（Durability）。

**管理：** 事务由数据库管理系统（DBMS）负责管理和执行，确保在并发操作中多个事务能够正确地互相隔离和并发执行。

在MySQL中，`trx_id` 是指事务的唯一标识符或事务ID（Transaction ID）。它用来标识在数据库中运行的每个事务，每个事务都会被分配一个唯一的 `trx_id`。

主要特点和用途：

1. **唯一性：** 每个事务都有一个唯一的 `trx_id`，它是事务管理和识别的关键标识符。
2. **事务追踪：** `trx_id` 可以用于跟踪和识别正在运行或已完成的事务。通过查询数据库的相关系统表，可以使用 `trx_id` 来获取有关事务的详细信息，如事务的开始时间、提交时间、涉及的数据等。
3. **并发控制：** 在数据库的并发控制机制中，`trx_id` 用于管理和比较事务的提交顺序、锁管理等，以确保事务的隔离性和一致性。

使用示例：

- 查询当前正在执行的事务的 `trx_id`：

  ```sql
  SELECT trx_id FROM information_schema.innodb_trx WHERE trx_state = 'RUNNING';
  ```

- 查询已提交或已回滚事务的 `trx_id` 和相关信息：

  ```sql
  SELECT trx_id, trx_started, trx_state FROM information_schema.innodb_trx WHERE trx_state = 'COMMITTED' OR trx_state = 'ROLLBACK';
  ```

相关注意事项：

- **事务生命周期：** 事务在开始时分配一个 `trx_id`，直到事务提交或回滚后，`trx_id` 才会释放。
- **事务状态：** `trx_id` 通常与事务的状态（运行中、已提交、已回滚等）一起使用，帮助管理事务的状态和并发控制。

## Sql通用语法和分类

### 通用语法

1.Sql语句可以单行或多行书写，以分号结尾

2.Sql语句可以使用空格和缩进提高语句可读性

3.Mysql数据库的sql语句不区分大小写，关键字建议使用大写

4.注释

单行注释：-- 注释内容 或#注释内容  -- 和注释内容之间的空格不可以省略

多行注释：/* 注释内容 */

### 分类

1.DDL：data definition language  数据定义语言，用来定义数据库对象（数据库，表，字段）

2.DML：data manipulation language 数据操作语言，用来对数据库表中的数据进行增删改

3.DQL：data query language 数据查询语言，用来查询数据库中表的记录

4.DCL：data control language 数据控制语言，用来创建数据库用户，控制数据库的访问权限

#### DDL

data definition language  数据定义语言，用来定义数据库对象（数据库，表，字段）

##### DDL数据库操作

###### 查询

查询所有数据库

```sql
SHOW DATABASES;
```

查询当前数据库

```SQL
SELECT DATABASE();
```

###### 创建

```sql
-- 方框中是可选项      
-- 不建议字符集使用utf8，可以utf8mb4
CREATE DATABASE [IF NOT EXISTS] 数据库名 [DEFAULT CHARSET 字符集] [COLLATE 排序规则];
```

###### 删除

```sql
DROP DATABASE [IF EXISTS] 数据库名;
```

###### 使用

```SQL
USE 数据库名;
```

##### DDL表操作-查询

###### 查询当前数据库所有表

```sql
SHOW TABLES;
```

###### 查询表结构

```sql
DESC 表名;
```

###### 查询指定表的建表语句

```sql
SHOW CREATE TABLE 表名;
```

##### DDL表操作-创建

```sql
CREATE TABLE table_name (
    column1 datatype [constraints],
    column2 datatype [constraints],
    ...
    table_constraints
);
```

`table_name`: 表的名称。

`column1`, `column2`, ...: 表中的列名。

`datatype`: 指定列的数据类型，比如`INT`, `VARCHAR`, `DATE`等。

`[constraints]`: 可选项，用于指定列的约束条件，比如`NOT NULL`, `UNIQUE`, `DEFAULT`等。

`table_constraints`: 可选项，用于指定表级的约束条件，比如主键、外键等。

##### DDL表操作-数据类型

有无符号用unsigned修饰

```sql
age int unsigned ;
```

###### 整数类型

| tinyint   | 用1个字节存储整数 |
| --------- | ----------------- |
| smallint  | 用2个字节存储整数 |
| mediumint | 用3个字节存储整数 |
| int       | 用4个字节存储整数 |
| bigint    | 用8个字节存储整数 |

###### 小数类型

浮点型

浮点型超过精度范围会自动四舍五入

| 类型   | 所占字节 | 精度  |
| ------ | -------- | ----- |
| float  | 4        | 6-7   |
| double | 8        | 14-15 |

精度可以指定整数部分和小数部分

```
age float（总长度，小数部分长度）;
```

定点型

decimal  存储模式不是固定长度

```
age decimal(有效数位，小数部分数位);
```

###### 字符串类型

**CHAR**: 它是一种定长字符串类型，你需要在创建表时指定它的长度。CHAR类型在存储时会根据指定的长度右侧填充空格，因此在存储时会浪费一些空间。

**VARCHAR**: 这是一种可变长度的字符串类型，你同样需要指定它的最大长度。与CHAR不同，VARCHAR类型存储实际使用的长度，因此比CHAR更加节省空间。

**TEXT**: TEXT类型用于存储较长的字符串数据，最大长度是65,535个字符。TEXT类型可以有不同的子类型，如TINYTEXT、TEXT、MEDIUMTEXT和LONGTEXT，它们的存储容量逐渐增加。

**BLOB**: BLOB类型用于存储二进制数据，如图像、音频或视频文件。它也有不同的子类型，如TINYBLOB、BLOB、MEDIUMBLOB和LONGBLOB，它们的存储容量逐渐增加。

###### 时间日期类型

**DATE**: 用于存储日期值，格式为'YYYY-MM-DD'

**TIME**: 用于存储时间值，格式为'HH:MM

**DATETIME**: 用于存储日期和时间值，格式为'YYYY-MM-DD HH:MM

**TIMESTAMP**: 类似于DATETIME，但是会自动更新为当前的时间戳，通常用于记录数据的创建和修改时间。

**YEAR**: 用于存储年份值，格式为'YYYY'

##### DDL表操作-修改

###### 添加字段

```sql
ALTER TABLE 表名 ADD 字段名 类型（长度）[COMMENT 注释] [约束];
```

###### 修改字段

修改数据类型

```sql
ALTER TABLE 表名 MODIFY 字段名 新数据类型（长度）;
```

修改字段名和字段类型

```sql
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型（长度）[COMMENT 注释] [约束];
```

删除字段

```sql
ALTER TABLE 表名 DROP 字段名;
```

###### 修改表名

```sql
ALTER TABLE 表名 RENAME TO 新表名
```

##### DDL表操作-删除

###### 删除表













```sql
DROP table [if exists] 表名;
```

###### 删除指定表并重新创建该表

```sql
TRUNCATE TABLE 表名;
```



#### DML

Data Manipulation language,对数据库中表的的数据记录进行增删改操作

##### DML 添加数据

注意：

插入数据时，指定的字段顺序要和值的顺序是一一对应的

**字符串和日期型数据应该包含在引号中**

插入的数据大小应该在字段的规定范围内

###### 语法

```sql
INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...);
```

假设你有一个名为`users`的表，有`id`、`name`和`age`列，你要向表中插入一条新的记录：

```sql
INSERT INTO users (name, age) VALUES ('John', 30);
```

这将向`users`表中插入一条名为"John"，年龄为30的记录。如果你省略列名，那么你需要提供与表中列的顺序相对应的值：

```sql
INSERT INTO users VALUES (1, 'John', 30);
```

你也可以一次插入多条记录，只需在`VALUES`子句中提供多个值组的列表即可

```sql
INSERT INTO users (name, age) VALUES ('Alice', 25), ('Bob', 35), ('Charlie', 40);
```

##### DML 修改数据

修改语句的条件可以有，也可以没有，如果没有条件，会修改整个表的数据（即指定字段那一列都会被修改）

最后一个无逗号

```sql
UPDATE 表名 SET 字段1=值1，字段2=值2,...[WHERE 条件];
```

##### DML 删除数据

DELETE语句没有设条件，会删除整张表的所有数据

DELETE语句不能删除某一个字段的值（使用UPDATE 设成NULL），用于删除行

```sql
DELETE FROM 表名 [WHERE 条件]
```



#### DQL

data query language 数据查询语言，用来查询数据库中表的记录

##### DQL基本查询

###### 查询多个字段

返回指定字段

```sql
SELECT 字段1，字段2，字段3... FROM 表名;
```

返回全部字段，使用通配符‘*’

```sql
SELECT * FROM 表名;
```

###### 设置别名

```sql
SELECT 字段1[AS 别名1],字段2[AS 别名2],...FROM 表名;
```

demo

```sql
SELECT name AS user_name, age AS user_age
FROM users;
```

这样查询结果中的列名将会是`user_name`和`user_age`，而不是原始的字段名。

###### 去除重复记录

**distinct关键字应用于所有列而不只是前置它的列**

```sql
SELECT DISTINCT column1, column2 FROM table_name;
```

##### DQL条件查询

```sql
SELECT 字段列表 FROM 表名 WHERE 条件列表;
```

###### 条件列表

**基本条件**：使用比较运算符（如`=`, `>`, `<`, `>=`, `<=`, `!=`）来比较字段和值。

```sql
SELECT *
FROM table_name
WHERE column_name = value;
```

**AND条件**：使用`AND`关键字将多个条件组合在一起，所有条件都必须为真才会返回结果。

**注意AND的运算优先级是高于OR的，所以书写该类语句 建议使用括号防止出错**

```sql
SELECT *
FROM table_name
WHERE column1 = value1 AND column2 = value2;
```

**OR条件**：使用`OR`关键字将多个条件组合在一起，只要有一个条件为真就会返回结果。

```sql
SELECT *
FROM table_name
WHERE column1 = value1 OR column2 = value2;
```

**IN条件**：使用`IN`关键字匹配一系列可能的值。

```sql
SELECT *
FROM table_name
WHERE column_name IN (value1, value2, value3);
```

**LIKE条件**：使用`LIKE`关键字进行模糊匹配，通常与通配符（`%`和`_`）一起使用。

```sql
SELECT *
FROM table_name
WHERE column_name LIKE 'prefix%'; --以xx为前缀
WHERE column_name LIKE '%suffix'; --以xx为后缀
```

**NULL条件**：使用`IS NULL`和`IS NOT NULL`来检查字段是否为NULL。

```sql
SELECT *
FROM table_name
WHERE column_name IS NULL;
```



##### DQL聚合函数

语法

```sql
SELECT 聚合函数（字段列表）FROM 表名 [where 条件];
```

**COUNT()**: 计算指定列中的行数。

COUNT()函数有两种使用方式。

使用COUNT(*)对表中行的数目进行计数，不管表列中包含的是空值（NULL）还是非空值。

使用COUNT(column)对特定列中具有值的行进行计数，忽略NULL值。

```sql
SELECT COUNT(*) FROM table_name;
```

**SUM()**: 对指定列中的值求和。

```sql
SELECT SUM(column_name) FROM table_name;
```

**AVG()**: 计算指定列中值的平均值。

AVG()函数忽略列值为NULL的行

```sql
SELECT AVG(column_name) FROM table_name;
```

**MIN()**: 返回指定列中的最小值。

MIN()函数忽略列值为NULL的行。

```sql
SELECT MIN(column_name) FROM table_name;
```

**MAX()**: 返回指定列中的最大值。

MAX()函数忽略列值为NULL的行。

```sql
SELECT MAX(column_name) FROM table_name;
```



##### DQL分组查询

```sql
SELECT column1, aggregate_function(column2), ...
FROM table_name
GROUP BY column1;
```

`column1`: 用于分组的列名。

`aggregate_function`: 聚合函数，用于对分组后的数据进行计算，比如`COUNT`, `SUM`, `AVG`, `MAX`, `MIN`等。

`table_name`: 要查询的表名。

**demo**

假设你有一个名为`orders`的表，其中包含`product_id`和`quantity`字段，你想要按照产品ID进行分组，并计算每个产品的总数量，可以这样做：

```sql
SELECT product_id, SUM(quantity) AS total_quantity
FROM orders
GROUP BY product_id;
```



```sql
SELECT 字段列表 FROM 表名 [WHERE 条件] GROUP BY 分组字段名 [HAVING 分组后过滤条件];
```

**demo**

假设你有一个名为`orders`的表，其中包含`product_id`和`quantity`字段，你想要找出每个产品的总数量超过100的情况，可以这样做：

```sql
SELECT product_id, SUM(quantity) AS total_quantity
FROM orders
GROUP BY product_id
HAVING SUM(quantity) > 100;
```

这条SQL语句首先按照`product_id`对`orders`表进行分组，并计算每个产品的总数量。然后，`HAVING`子句筛选出总数量大于100的分组结果。

###### where与having区别

执行时机不同：**where是分组之前进行过滤**，不满足where条件，不参与分组。**having是分组之后对结果进行过滤**，**`HAVING`子句只能在使用了`GROUP BY`子句的查询中使用**

判断条件不同：**where不能对聚合函数进行判断，而having可以**

###### 注意

执行顺序：where>聚合函数>having

分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义



##### DQL排序查询

在MySQL中，你可以使用`ORDER BY`子句对结果进行排序。`ORDER BY`子句可以根据一个或多个列对结果进行排序，可以按升序（ASC）或降序（DESC）排序。

例如，假设你有一个名为`users`的表，其中包含`id`、`name`和`age`列，你可以按照`age`列对结果进行升序排序，如下所示：

```sql
SELECT * FROM users ORDER BY age ASC;
```

或者按照`name`列进行降序排序，如下所示：

```sql
SELECT * FROM users ORDER BY name DESC;
```

如果想要按照多个列进行排序，你可以在`ORDER BY`子句中指定多个列名，例如：

如果你按照多个列排序，那么输出将按照第一个列指定的顺序进行排序，如果第一个列有相同的值，则会按照第二个列指定的顺序进行排序，依此类推。

```sql
SELECT * FROM users ORDER BY age ASC, name DESC;
```



##### DQL分页查询

###### 语法

```sql
SELECT * FORM TABLE_NAME LIMIT 起始索引,查询记录数;
```

起始索引=（页码-1）*每页记录数



##### DQL执行顺序

在 SQL 查询中，通常遵循以下执行顺序：

1. **FROM**: 在执行查询之前，首先会确定要从哪个表中检索数据。这可能涉及到一个或多个表的连接操作。
2. **WHERE**: 在`WHERE`子句中指定的条件将应用于`FROM`步骤中选择的行，以过滤出满足条件的行。
3. **GROUP BY**: 如果查询包含`GROUP BY`子句，则结果将按照指定的列进行分组。在分组之后，聚合函数将对每个组应用，并且可能会使用`HAVING`子句来进一步筛选分组。
4. **HAVING**: 如果查询包含`GROUP BY`子句，则`HAVING`子句用于筛选分组。它类似于`WHERE`子句，但是它在分组之后进行过滤，因此可以使用聚合函数。
5. **SELECT**: 在`SELECT`子句中指定的列将从`FROM`步骤中选择的行中检索出来。这是最终返回的列。
6. **ORDER BY**: 最后，如果查询包含`ORDER BY`子句，则结果将按照指定的列进行排序。这一步发生在所有其他步骤之后，返回结果之前。

在实际执行过程中，优化器可能会对执行顺序进行调整以提高性能，但通常情况下，这是查询的一般执行顺序。



#### DCL

数据控制语言（Data Control Language）的缩写，用于数据库管理中。这些语言包括授权（GRANT）和撤销授权（REVOKE）等命令，用于管理数据库用户的访问权限。

##### DCL用户管理

###### 查询用户

```sql
SELECT * FROM USER;
```

###### 创建用户





























































































































































































































































































































































































































































































































































































































































































































































```SQL
CREATE USER 'username'@'hostname' IDENTIFIED BY 'password';
```

###### 修改用户密码

```sql
ALTER USER 'username'@'hostname' IDENTIFIED BY 'new_password';
```

###### 删除用户

```sql
DROP USER 'username'@'hostname';
```



##### DCL权限控制

###### 查询权限

```sql
SHOW GRANTS FOR 'username'@'hostname';
```

###### 授予权限

授予用户对特定数据库的所有权限：

```sql
GRANT ALL PRIVILEGES ON database_name.* TO 'username'@'hostname';
```

授予用户对特定数据库中特定表的特定权限：

```sql
GRANT SELECT, INSERT ON database_name.table_name TO 'username'@'hostname';
```

授予用户对所有数据库的特定权限：

```sql
GRANT SELECT, INSERT ON *.* TO 'username'@'hostname';
```

在这些语句中：

- `ALL PRIVILEGES`表示所有权限，你也可以指定具体的权限，如`SELECT`、`INSERT`、`UPDATE`等。
- `database_name`是数据库的名称，`table_name`是表的名称。
- `'username'@'hostname'`是用户和主机名的组合，表示你要授予权限的用户和他们的登录主机。可以使用通配符来表示所有主机（例如，**`'username'@'%'`表示在任何主机上都可以登录）**。

请注意，授予权限需要具有`GRANT OPTION`权限的用户或超级用户权限。

###### 撤销权限

撤销用户对特定数据库的所有权限：

```sql
REVOKE ALL PRIVILEGES ON database_name.* FROM 'username'@'hostname';
```

撤销用户对特定数据库中特定表的特定权限：

```sql
REVOKE SELECT, INSERT ON database_name.table_name FROM 'username'@'hostname';
```

撤销用户对所有数据库的特定权限：

```sql
REVOKE SELECT, INSERT ON *.* FROM 'username'@'hostname';
```

在这些语句中：

- `ALL PRIVILEGES`表示所有权限，你也可以指定具体的权限，如`SELECT`、`INSERT`、`UPDATE`等。
- `database_name`是数据库的名称，`table_name`是表的名称。
- `'username'@'hostname'`是用户和主机名的组合，表示你要撤销权限的用户和他们的登录主机。可以使用通配符来表示所有主机（例如，`'username'@'%'`表示在任何主机上都可以登录）。

请注意，撤销权限需要具有`GRANT OPTION`权限的用户或超级用户权限。

###### 权限种类

在MySQL中，有多种类型的权限可以授予用户。以下是一些常见的权限类型：

1. **SELECT**: 允许用户查询（检索）表中的数据。
2. **INSERT**: 允许用户向表中插入新数据。
3. **UPDATE**: 允许用户更新表中的现有数据。
4. **DELETE**: 允许用户从表中删除数据。
5. **CREATE**: 允许用户创建新表或数据库。
6. **DROP**: 允许用户删除表或数据库。
7. **ALTER**: 允许用户修改数据库结构，如添加、删除或修改表的列。
8. **GRANT OPTION**: 允许用户将自己拥有的权限授予其他用户。
9. **ALL PRIVILEGES**: 允许用户执行所有操作，相当于拥有全部权限。
10. **CREATE TEMPORARY TABLES**: 允许用户创建临时表。
11. **SHOW DATABASES**: 允许用户查看数据库列表。
12. **LOCK TABLES**: 允许用户锁定表，以防止其他用户访问。
13. **RELOAD**: 允许用户重新加载服务端配置文件，例如重新加载日志文件。
14. **SHUTDOWN**: 允许用户关闭数据库服务。
15. **PROCESS**: 允许用户查看正在运行的进程。
16. **FILE**: 允许用户执行文件操作，如读取、写入、删除文件。
17. **REFERENCES**: 允许用户创建具有外键约束的表。
18. **REPLICATION CLIENT**: 允许用户连接到主服务器以检索二进制日志事件。
19. **REPLICATION SLAVE**: 允许用户作为从服务器连接到主服务器。
20. **SUPER**: 允许用户执行一些特殊的系统级操作，如修改全局系统参数或关闭服务器。

## 常用函数

### 字符串函数

**CONCAT(str1, str2, ...)**: 连接两个或多个字符串。

```sql
SELECT CONCAT('Hello', ' ', 'World'); -- 输出：Hello World
```

**SUBSTRING(str, start, length)**: 返回字符串的子串，从指定位置开始到指定长度的字符。

```sql
SELECT SUBSTRING('Hello World', 7, 5); -- 输出：World
```

**UPPER(str)** 和 **LOWER(str)**: 将字符串转换为大写或小写。

```sql
SELECT UPPER('hello'); -- 输出：HELLO
SELECT LOWER('WORLD'); -- 输出：world
```

**LENGTH(str)**: 返回字符串的长度。

```sql
SELECT LENGTH('Hello World'); -- 输出：11
```

**TRIM([BOTH | LEADING | TRAILING] [remstr FROM] str)**: 去除字符串两端或指定的字符。

```sql
SELECT TRIM('  hello  '); -- 输出：hello
SELECT TRIM(LEADING '0' FROM '000123'); -- 输出：123
```

`BOTH`：去除字符串两端的指定字符，默认行为。

`LEADING`：只去除字符串开头的指定字符。

`TRAILING`：只去除字符串末尾的指定字符。

`remstr`：**可选参数，指定要移除的字符，默认为去除空格。**

`str`：要处理的字符串。

**REPLACE(str, from_str, to_str)**: 替换字符串中的子串。

```sql
SELECT REPLACE('Hello World', 'World', 'Universe'); -- 输出：Hello Universe
```

**INSTR(str, substr)**: 返回子串在字符串中第一次出现的位置。

```sql
SELECT INSTR('hello world', 'world'); -- 输出：7
```

**LOCATE(substr, str[, pos])**: 在字符串中查找子串，并返回其位置，可指定起始位置。

```sql
SELECT LOCATE('world', 'hello world'); -- 输出：7
```

### 数值函数

**ABS(x)**: 返回一个数的绝对值。

```sql
SELECT ABS(-10); -- 输出：10
```

**ROUND(x[, d])**: 将数四舍五入为指定的小数位数。如果省略了小数位数，则默认为 0。

```sql
SELECT ROUND(3.14159, 2); -- 输出：3.14
```

**CEILING(x)** 或 **CEIL(x)**: 返回不小于 x 的最小整数值。

```sql
SELECT CEILING(5.3); -- 输出：6
```

**FLOOR(x)**: 返回不大于 x 的最大整数值。

```sql
SELECT FLOOR(5.9); -- 输出：5
```

**POWER(x, y)**: 返回 x 的 y 次幂。

```sql
SELECT POWER(2, 3); -- 输出：8
```

**SQRT(x)**: 返回一个数的平方根。

```sql
SELECT SQRT(16); -- 输出：4
```

**MOD(x, y)**: 返回 x 除以 y 的余数。

```sql
SELECT MOD(10, 3); -- 输出：1
```

**RAND()**: 返回一个 0 到 1 之间的随机浮点数。

```sql
SELECT RAND(); -- 输出：随机浮点数
```

### 日期函数

`%Y`：年份，四位数字。

`%m`：月份，两位数字（01 到 12）。

`%d`：日期，两位数字（01 到 31）。

`%H`：小时（00 到 23）。

`%i`：分钟（00 到 59）。

`%s`：秒数（00 到 59）。

`%W`：星期名称（完整的星期名称）。

`%M`：月份名称（完整的月份名称）。

`%b`：月份名称（缩写）。

`%a`：星期名称（缩写）。

**NOW()**: 返回当前日期和时间。

```sql
SELECT NOW(); -- 返回当前日期和时间
```

**CURDATE()**: 返回当前日期。

```sql
SELECT CURDATE(); -- 返回当前日期，格式为'YYYY-MM-DD'
```

**CURTIME()**: 返回当前时间。

```sql
SELECT CURTIME(); -- 返回当前时间，格式为'HH:MM:SS'
```

**DATE_FORMAT(date, format)**: 将日期格式化为指定的格式。

```sql
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d'); -- 格式化当前日期为'YYYY-MM-DD'
```

**DATE_ADD(date, INTERVAL expr unit)** 和 **DATE_SUB(date, INTERVAL expr unit)**: 分别用于添加和减去日期或时间间隔。

```sql
SELECT DATE_ADD('2024-06-05', INTERVAL 1 DAY); -- 添加一天
SELECT DATE_SUB(NOW(), INTERVAL 1 MONTH); -- 减去一个月
```

**DATEDIFF(date1, date2)**: 返回两个日期之间的天数差。

```sql
SELECT DATEDIFF('2024-06-10', '2024-06-05'); -- 返回5
```

**DATE_FORMAT(date, format)**: 将日期格式化为指定的格式。

```sql
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d'); -- 格式化当前日期为'YYYY-MM-DD'
```

**DAYNAME(date)** 和 **MONTHNAME(date)**: 返回日期对应的星期几和月份名称。

```sql
SELECT DAYNAME('2024-06-05'); -- 返回星期二
SELECT MONTHNAME('2024-06-05'); -- 返回六月
```

**无论你什么时候指定一个日期，不管是插入或更新表值还是用WHERE子句进行过滤，日期必须为格式yyyy-mm-dd**

### 流程函数

**IF(condition, expr1, expr2)**: 如果条件为真，则返回 expr1；否则返回 expr2。

```sql
SELECT IF(5 > 3, 'Yes', 'No'); -- 返回 'Yes'
```

**CASE value WHEN compare_value THEN result [WHEN ...] [ELSE result] END**: 类似于 switch 语句，根据条件进行分支处理。

```sql
select
    name,
    case area
        when 'changsha' then '1'
        when 'beijing' then'2'
        when 'guangxi' then '3'
        else '4'
    end as from_area
from person;
```

**CASE WHEN condition THEN result [WHEN ...] [ELSE result] END**: 另一种形式的 CASE 语句，根据条件进行分支处理。

```sql
SELECT 
    CASE 
        WHEN column_name1 = value1 AND column_name2 = value2 THEN result1 
        WHEN column_name1 = value3 AND column_name2 = value4 THEN result2 
        ELSE default_result 
    END
FROM table_name;
```

在MySQL中，使用`CASE`语句时，必须以`END`结束`CASE`块，但不一定需要使用`AS`关键字来为`CASE`表达式指定别名。**`AS`关键字通常用于为整个`CASE`表达式指定一个别名，以便在查询结果中标识该列。**

**IFNULL(expr1, expr2)**: 如果 expr1 不为 NULL，则返回 expr1；否则返回 expr2。

```sql
SELECT IFNULL(NULL, 'No value'); -- 返回 'No value'
```

**COALESCE(expr1, expr2, ...)**: 返回参数列表中第一个非 NULL 表达式的值。 

```sql
SELECT COALESCE(NULL, 0, 'No value'); -- 返回 0
```

## 约束

约束用于确保数据库中的数据符合特定的规则或条件

常见的约束包括：

### 主键约束（PRIMARY KEY）

用于唯一标识表中的每一行数据，确保每个值都是唯一的。一个表只能有一个主键，不允许NULL值。

表中的任意列都可以作为主键，只要它满足以下条件：

任意两行都不具有相同的主键值

每个行都必须具有一个主键值（主键列不允许NULL值）

主键约束示例：

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    student_name VARCHAR(50),
    age INT
);
```

这个示例创建了一个名为`students`的表，其中`student_id`列被定义为主键。主键约束将确保每个`student_id`值都是唯一的，因此无法插入具有相同`student_id`值的行。

#### 复合主键

复合主键（Composite Primary Key）的语法如下：

```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    ...
    PRIMARY KEY (column1, column2, ...)
);
```

在上述语法中：

- `CREATE TABLE table_name` 是创建表的语句，`table_name` 是表的名称。
- `column1`, `column2`, ... 是表中的列名，`datatype` 是列的数据类型。
- `PRIMARY KEY (column1, column2, ...)` 定义了一个复合主键，其中 `(column1, column2, ...)` 是由多个列组成的主键。

假设我们要创建一个名为 `enrollments` 的表，记录学生选课的情况，可以使用以下语句定义复合主键：

```sql
CREATE TABLE enrollments (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    PRIMARY KEY (student_id, course_id)
);
```

在上面的示例中：

- `enrollments` 是表的名称。
- `student_id` 和 `course_id` 是两个列，分别用来存储学生的ID和课程的ID。
- `PRIMARY KEY (student_id, course_id)` 定义了一个由 `student_id` 和 `course_id` 组成的复合主键。

这样设计的表结构可以确保每个学生在每门课程上的选课记录是唯一的，避免了重复记录的问题，并且为这两列建立了一个复合索引，以便数据库系统能够高效地管理和查询这些数据。

### 外键约束（FOREIGN KEY）

外键一般会绑定到另一张表的主键或唯一键上，以确保参照完整性（Referential Integrity）。这意味着外键列中的值必须在关联表的主键或唯一键列中存在，否则将无法插入或更新数据，从而防止出现无效的引用。

用于建立表与表之间的关联关系，确保引用表中的值在被引用表中存在。

外键约束示例：

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    product_id INT,
    quantity INT,
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

这个示例创建了一个名为`orders`的表，其中`product_id`列被定义为外键，它引用了另一个表`products`中的`product_id`列。**外键约束确保在`orders`表中插入的`product_id`值必须存在于`products`表中的`product_id`列中。**

### 唯一约束（UNIQUE）

确保某个列或一组列中的值是唯一的，但允许NULL值。

唯一约束示例：

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    department VARCHAR(50)
);
```

这个示例创建了一个名为`employees`的表，其中`email`列被定义为唯一约束。唯一约束将确保每个`email`值都是唯一的，因此无法插入具有相同`email`值的多个行。

### 检查约束（CHECK）

在标准的 SQL 中，`CHECK` 约束通常用于 `CREATE TABLE` 或 `ALTER TABLE` 语句中，用于在表级别定义数据完整性约束。它可以确保表中的数据满足指定的条件。

定义某个列中允许的值的范围或条件。

检查约束示例：

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    price DECIMAL(10,2),
    quantity INT,
    CHECK (price > 0 AND quantity >= 0)
);
```

这个示例创建了一个名为`products`的表，其中`price`列和`quantity`列都定义了检查约束。检查约束确保`price`列的值大于0，且`quantity`列的值大于或等于0。因此，无法插入价格为负值或数量为负值的行。

### 默认约束（DEFAULT）

默认约束示例：

保存数据时，若未指定数据的值，则采用默认值

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    salary DECIMAL(10,2) DEFAULT 50000
);
```

在这个示例中，`salary`列被设置为DECIMAL类型，宽度为10，小数位数为2。DEFAULT关键字用于指定默认值为50000。当你向`employees`表**插入**新的员工信息时，如果没有指定`salary`，它将被自动设置为50000。

### 非空约束（NOT NULL）

限制该列的数据值不能为null

非空约束示例：

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255)
);
```

在这个示例中，`name`列被设置为`NOT NULL`，这意味着在插入新的行时，`name`列的值不能为NULL。这样做可以确保在数据库中每个客户都有一个名字。

**这些约束可以在创建表时使用`CREATE TABLE`语句来定义，也可以在已存在的表上使用`ALTER TABLE`语句来添加。使用约束可以提高数据库的数据完整性和一致性。**

### unique区别primary key

在数据库中，`UNIQUE` 和 `PRIMARY KEY` 都用于确保数据表中某列的值的唯一性，但它们在用途上有一些区别。

1. **PRIMARY KEY（主键）**:
   - 主键是表中唯一标识每个记录的列或列组合。
   - 每个表只能有一个主键。
   - 主键列不允许包含空值（NULL）。
   - 主键可以自动递增（auto-increment），一般用于整数类型的列。
2. **UNIQUE（唯一约束）**:
   - `UNIQUE` 约束确保了列中的所有值都是唯一的，但允许空值（NULL）。
   - 一个表可以有多个唯一约束。
   - `UNIQUE` 可以用于任何数据类型的列。

所以，当你需要确保一列中的值在整个表中是唯一的且不允许为空时，你应该使用 `PRIMARY KEY`。而当你需要确保一列中的值在整个表中是唯一的，但允许空值时，你应该使用 `UNIQUE` 约束。

## 多表查询 

**若采取select * from student（表中4行），class（表中6行） 此种方式形成笛卡尔积，产生24种结果。而多表查询通过某种方式消除笛卡尔积的影响**

### 多表关系

#### 一对一关系

- 在这种关系中，一个记录在一个表中只对应一个记录在另一个表中，并且反之亦然。这种关系通常用于将某些实体的可选信息存储在单独的表中，以减少主表的冗余信息。

  示例：

  假设我们有两个表：一个是`users`表，另一个是`user_profiles`表。`users`表包含用户的基本信息，如ID、用户名和密码，而`user_profiles`表包含用户的更多详细信息，如电子邮件地址、电话号码等。

  **users表**：

  ```lua
  | ID | Username | Password |
  |----|----------|----------|
  | 1  | Alice    | password1|
  | 2  | Bob      | password2|
  | 3  | Charlie  | password3|
  ```

  **user_profiles表**：

  ```sql
  | ID | User_ID | Email             | Phone         |
  |----|---------|-------------------|---------------|
  | 1  | 1       | alice@example.com | 123-456-7890  |
  | 2  | 2       | bob@example.com   | 456-789-1230  |
  ```

  在这个例子中，`users`表存储用户的基本信息，每个用户有一个唯一的ID，而`user_profiles`表存储用户的详细信息，并通过`User_ID`字段与`users`表进行关联，`User_ID`是`users`表中的外键。

  首先，我们创建`users`表：

  ```sql
  CREATE TABLE users (
      ID INT AUTO_INCREMENT PRIMARY KEY,
      Username VARCHAR(50) NOT NULL,
      Password VARCHAR(50) NOT NULL
  );
  ```

  然后，我们创建`user_profiles`表：

  ```sql
  CREATE TABLE user_profiles (
      ID INT AUTO_INCREMENT PRIMARY KEY,
      User_ID INT UNIQUE,
      Email VARCHAR(100),
      Phone VARCHAR(20),
      FOREIGN KEY (User_ID) REFERENCES users(ID)
  );
  ```

  在上面的`user_profiles`表中，`User_ID`字段被设置为UNIQUE，这是因为我们要确保每个用户只有一个用户资料。外键约束指定了`User_ID`字段与`users`表中的`ID`字段之间的关系。

  现在，这两个表已经创建好了，并且之间建立了一对一关系。**当向`user_profiles`表中插入数据时，要确保`User_ID`字段的值存在于`users`表的`ID`字段中，以满足外键约束。**

#### 一对多关系

在MySQL中，一对多关系是指一个表中的一条记录对应另一个表中的多条记录。**这通常通过在“多”端表中使用外键来实现**。以下是一个简单的例子，说明了如何在MySQL中创建和处理一对多关系：

假设我们有两个表：`orders`（订单）和`order_items`（订单项）。每个订单可以有多个订单项。

首先，我们创建这两个表：

```sql
CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_name VARCHAR(100),
    order_date DATE
);

CREATE TABLE order_items (
    item_id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    item_name VARCHAR(100),
    quantity INT,
    price DECIMAL(10, 2),
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
);
```

在这个例子中，`order_items`表包含一个`order_id`列，它是对`orders`表的外键引用。这样，`order_items`表中的每个订单项都关联到`orders`表中的一个订单。

接下来，我们可以向这两个表中插入一些数据：

```sql
INSERT INTO orders (customer_name, order_date) VALUES ('John Doe', '2024-06-01');
INSERT INTO orders (customer_name, order_date) VALUES ('Jane Smith', '2024-06-02');

INSERT INTO order_items (order_id, item_name, quantity, price) VALUES (1, 'Product A', 2, 10.99);
INSERT INTO order_items (order_id, item_name, quantity, price) VALUES (1, 'Product B', 1, 24.99);
INSERT INTO order_items (order_id, item_name, quantity, price) VALUES (2, 'Product C', 3, 14.50);
```

现在，我们可以使用JOIN操作来检索与订单相关联的订单项：

```sql
SELECT orders.order_id, orders.customer_name, order_items.item_name, order_items.quantity, order_items.price
FROM orders
INNER JOIN order_items ON orders.order_id = order_items.order_id;
```

这将返回订单和订单项的相关信息，以便我们可以看到每个订单中包含的订单项。

#### 多对多关系

- 在这种关系中，一个记录在一个表中可以对应多个记录在另一个表中，并且反之亦然。

  在 MySQL 中处理多对多关系**通常需要使用一个中间表来表示两个实体之间的关联**。举个例子，假设我们有两个实体：学生和课程，其中一个学生可以选择多门课程，同时一门课程也可以有多个学生选择。这种情况下，我们需要创建一个中间表来表示学生和课程之间的关联关系。

  以下是如何在 MySQL 中实现多对多关系的一般步骤：

  **创建学生表和课程表：**

  ```sql
  CREATE TABLE students (
      student_id INT AUTO_INCREMENT PRIMARY KEY,
      student_name VARCHAR(100)
  );
  
  CREATE TABLE courses (
      course_id INT AUTO_INCREMENT PRIMARY KEY,
      course_name VARCHAR(100)
  );
  ```

  **创建中间表来表示学生和课程之间的关联关系：**

  ```sql
  CREATE TABLE student_courses (
      student_id INT,
      course_id INT,
      PRIMARY KEY (student_id, course_id),
      FOREIGN KEY (student_id) REFERENCES students(student_id),
      FOREIGN KEY (course_id) REFERENCES courses(course_id)
  );
  ```

  在这个中间表中，`student_id` 和 `course_id` 组成了复合主键，并且分别作为外键引用了学生表和课程表的主键。

  **向学生表和课程表中插入数据**：

  ```sql
  INSERT INTO students (student_name) VALUES ('Alice');
  INSERT INTO students (student_name) VALUES ('Bob');
  
  INSERT INTO courses (course_name) VALUES ('Math');
  INSERT INTO courses (course_name) VALUES ('Science');
  ```

  **创建学生和课程的关联关系：**

  ```sql
  INSERT INTO student_courses (student_id, course_id) VALUES (1, 1); -- Alice选择了Math
  INSERT INTO student_courses (student_id, course_id) VALUES (1, 2); -- Alice选择了Science
  INSERT INTO student_courses (student_id, course_id) VALUES (2, 2); -- Bob选择了Science
  ```

  **查询学生选择的课程以及课程的学生：**

  ```sql
  -- 查询Alice选择的课程
  SELECT courses.course_name
  FROM courses
  INNER JOIN student_courses ON courses.course_id = student_courses.course_id
  WHERE student_courses.student_id = 1;
  
  -- 查询选择了Science课程的学生
  SELECT students.student_name
  FROM students
  INNER JOIN student_courses ON students.student_id = student_courses.student_id
  WHERE student_courses.course_id = 2;
  ```

  通过这种方式，可以处理多对多关系，学生可以选择多门课程，同时一门课程也可以有多个学生选择。

### 查询分类

#### 连接查询

**内外连接区别**

内连接和外连接是SQL中连接表格的两种主要方式，它们的**区别主要体现在连接时对不匹配行的处理方式**。

1. **内连接（INNER JOIN）**:
   - 内连接是基于两个表之间的共同值来连接数据的。
   - 内连接返回的结果集中仅包含两个表中匹配的行，即只返回符合连接条件的行。
   - 如果两个表中没有匹配的行，则不会返回任何结果。

2. **外连接（OUTER JOIN）**:
   - 外连接是基于连接条件连接数据的，但是它允许返回某个表中没有匹配的行。
   - 外连接分为左外连接（LEFT JOIN）和右外连接（RIGHT JOIN）两种形式。
   - 左外连接返回左表的所有行以及右表中与之匹配的行，如果右表中没有匹配的行，则用 NULL 填充。
   - 右外连接返回右表的所有行以及左表中与之匹配的行，如果左表中没有匹配的行，则用 NULL 填充。

总之，内连接仅返回匹配的行，而外连接则可以返回一个表中的所有行（左表或右表），即使在另一个表中没有匹配的行。外连接提供了更灵活的方式来处理数据，特别是在需要保留一个表中所有行的情况下。

##### 内连接查询

内连接是一种用于在数据库中检索相关数据的方法。它基于两个或多个表格之间的共同值，将它们连接起来，以便只返回满足连接条件的行。内连接的结果是两个表格中匹配行的交集。

###### 显式内连接

由于连接关系和连接条件明确可见，因此更易于维护和修改。如果需要添加或修改连接条件，可以直接在`JOIN`语句中进行修改。

```sql
SELECT columnlist from table1 inner join table2 on 条件;
```

###### 隐式内连接

连接条件隐藏在`WHERE`子句中，可能会使得维护和修改变得困难，特别是在复杂查询中可能会容易出错。

```sql
SELECT columnlist from table1,table2 where 条件;
```

##### 外连接查询

###### 左右表判断

在外连接中，左表和右表的区分也是根据查询的语法来确定的。通常情况下，左表是在 `LEFT JOIN` 或 `LEFT OUTER JOIN` 关键字之前指定的表，而右表则是在 `ON` 子句之后指定的表。

下面是一个简单的例子，演示了如何判断左表和右表：

```sql
SELECT *
FROM table1
LEFT JOIN table2 ON table1.id = table2.id;
```

在这个查询中，`table1` 是左表，`table2` 是右表。因为 `table1` 出现在 `LEFT JOIN` 关键字之前，而 `table2` 出现在 `ON` 子句之后。

同样地，在右外连接（RIGHT JOIN 或 RIGHT OUTER JOIN）中，右表是在 `RIGHT JOIN` 或 `RIGHT OUTER JOIN` 关键字之前指定的表，而左表则是在 `ON` 子句之后指定的表。

总之，在 SQL 中，左表和右表的区分取决于它们在连接语句中的出现顺序。

外连接是一种数据库查询方法，它允许检索一个表格中的数据，并且会包括另一个表格中的相关数据，即使在另一个表格中没有匹配的行。

###### 左外连接

左外连接返回左边表格中的所有行，以及右边表格中与左边表格中的行匹配的行。如果右边表格中没有与左边表格中的行匹配的行，那么将返回`NULL`值

假设我们有两个表，一个是 `students`，包含学生的基本信息，另一个是 `grades`，包含学生的成绩信息。我们将使用左外连接来检索所有学生及其成绩（如果有）

首先，我们创建这两个表，并插入一些示例数据：

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    student_name VARCHAR(50)
);

CREATE TABLE grades (
    student_id INT,
    subject VARCHAR(50),
    grade INT,
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);

INSERT INTO students (student_id, student_name) VALUES
(1, 'Alice'),
(2, 'Bob'),
(3, 'Charlie');

INSERT INTO grades (student_id, subject, grade) VALUES
(1, 'Math', 85),
(2, 'Math', 90),
(2, 'Science', 88);
```

现在，我们可以使用左外连接来检索所有学生及其成绩（如果有的话）：

```sql
SELECT students.student_id, students.student_name, grades.subject, grades.grade
FROM students
LEFT JOIN grades ON students.student_id = grades.student_id;
```

这将返回一个结果集，其中包含每个学生及其对应的成绩。如果某个学生没有成绩记录，他们的成绩列将显示为 NULL。

###### 右外连接

右外连接返回右边表格中的所有行，以及左边表格中与右边表格中的行匹配的行。如果左边表格中没有与右边表格中的行匹配的行，那么将返回`NULL`值。

下面是一个 MySQL 中右外连接（RIGHT JOIN）的示例。我们将使用两个表：`departments` 和 `employees`，并假设 `departments` 表是主表， `employees` 表是外部表。

首先，让我们创建这两个表并插入一些示例数据：

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50)
);

CREATE TABLE employees (
    employee_id INT AUTO_INCREMENT PRIMARY KEY,
    employee_name VARCHAR(50),
    department_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);

INSERT INTO departments (department_id, department_name) VALUES
(1, 'Engineering'),
(2, 'Sales'),
(3, 'Marketing');

INSERT INTO employees (employee_name, department_id) VALUES
('John', 1),
('Alice', 2),
('Bob', 1),
('Emily', NULL);
```

现在，我们使用右外连接来检索所有部门及其员工的信息，即使某些部门没有员工也要显示：

```sql
SELECT departments.department_name, employees.employee_name
FROM employees
RIGHT JOIN departments ON employees.department_id = departments.department_id;
```

在此查询中，`departments` 表是主表，`employees` 表是外部表。右外连接将返回右表中的所有行，以及左表中满足连接条件的行。如果左表中没有匹配的行，则对应的结果将包含 NULL 值。

##### 自连接查询

自连接是指在同一表中进行连接操作，通常用于处理具有层次结构的数据或者需要与自身进行比较的情况。下面是一个简单的 MySQL 自连接示例：

假设我们有一个名为 `employees` 的表，包含员工的 ID、姓名和经理的 ID。我们想要查询每个员工的姓名以及他们的经理的姓名。这时就可以使用自连接。

首先，我们创建并插入示例数据：

```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    manager_id INT
);

INSERT INTO employees VALUES
    (1, 'Alice', NULL),
    (2, 'Bob', 1),
    (3, 'Charlie', 1),
    (4, 'David', 2),
    (5, 'Emma', 2);
```

在这个示例中，如果 `manager_id` 字段为 NULL，表示该员工没有经理。现在，我们可以使用自连接来查询每个员工的姓名以及他们的经理的姓名：

```sql
SELECT e1.name AS employee, e2.name AS manager
--**在 SQL 查询中，通常在 FROM 子句后面使用空格来为表指定别名。这是一种常见的做法，有助于提高查询语句的可读性和可维护性。**
FROM employees e1
INNER JOIN employees e2 ON e1.manager_id = e2.id;
```

在这个查询中，我们对 `employees` 表进行了自连接。我们使用了两个别名 `e1` 和 `e2` 分别代表了员工和他们的经理。然后，我们使用 LEFT JOIN 将这两个表连接起来，连接条件是员工的 `manager_id` 等于经理的 `id`。最后，我们选择了每个员工的姓名以及他们的经理的姓名。

查询结果如下：

```sql
+----------+---------+
| employee | manager |
+----------+---------+
| Alice    | NULL    |
| Bob      | Alice   |
| Charlie  | Alice   |
| David    | Bob     |
| Emma     | Bob     |
+----------+---------+
```

#### 联合查询

联合查询（UNION）用于合并两个或多个 SELECT 语句的结果集。它的基本语法如下：

```sql
SELECT column1, column2, ...
FROM table1
UNION
SELECT column1, column2, ...
FROM table2;
```

这将返回两个 SELECT 语句的结果的合并，并且会**自动去重**，即重复的行只会返回一次。

举个例子，假设我们有两个表 `employees` 和 `customers`，分别存储了员工和客户的信息。我们想要合并这两个表的姓名列，并且去除重复的姓名：

```sql
SELECT name
FROM employees
UNION
SELECT name
FROM customers;
```

在这个查询中，`UNION` 将 `employees` 表和 `customers` 表的姓名列合并，并且自动去重。

如果你希望**保留重复的行**，可以使用 `UNION ALL`：

```sql
SELECT name
FROM employees
UNION ALL
SELECT name
FROM customers;
```

这样会保留所有的行，包括重复的行。

需要注意的是，**联合查询要求两个查询的列数和数据类型必须相同**，否则会导致语法错误。此外，联合查询也可以用于多个表的合并，不限于两个表。

#### 子查询

**据子查询返回的结果集的形式来分类**

子查询是指在一个 SQL 查询中嵌套另一个完整的 SELECT 查询。子**查询可以出现在 SELECT、FROM、WHERE 或 HAVING 子句中，**通常用于根据内部查询的结果来执行外部查询。

子查询可以用于许多场景，包括但不限于：

1. **过滤数据**：根据子查询的结果来过滤外部查询的数据。
2. **作为计算字段**：将子查询的结果作为外部查询的一个计算字段。
3. **用于连接**：在连接查询中使用子查询来获取关联表中的相关信息。
4. **嵌套多个子查询**：可以嵌套多个子查询，以便根据更复杂的逻辑执行查询。

需要注意的是，子查询可以影响查询的性能，尤其是在处理大量数据时。因此，应谨慎使用子查询，确保它们是必要的，并且能够通过优化查询来提高性能。

**按功能分类**

标量子查询返回一个值

列子查询返回一列

行子查询返回一行

表子查询返回多行多列

##### 标量子查询

标量子查询是一种特殊类型的子查询，**它返回单个值**而不是一个结果集。标量子查询通常用于 WHERE 子句、SELECT 子句或者 HAVING 子句中，作为一个条件或者一个计算字段的值。

以下是一个基本的标量子查询语法：

```sql
SELECT (SELECT column_name FROM table_name WHERE condition) AS alias_name;
```

接下来，让我们通过一个示例来说明标量子查询的用法。假设我们有一个名为 `orders` 的表，存储了订单信息，其中包括订单金额。我们想要查询出订单金额大于平均订单金额的订单信息。

```sql
SELECT order_id, order_amount
FROM orders
WHERE order_amount > (SELECT AVG(order_amount) FROM orders);
```

在这个查询中，子查询 `(SELECT AVG(order_amount) FROM orders)` 返回了订单金额的平均值。这个子查询嵌套在了外部查询的 WHERE 子句中，并将返回的平均值作为一个条件，用来筛选出订单金额大于平均订单金额的订单信息。

这种用法使得我们能够根据子查询的结果动态地调整查询的条件，并根据需要筛选出符合条件的数据。

##### 列子查询

MySQL 中列子查询（也称为多行子查询）用于返回**多行结果**的子查询。它通常出现在 SELECT 语句的列列表中，**作为一个计算字段**。

以下是列子查询的基本语法：

```sql
SELECT column1, column2, ...,
       (SELECT subquery_column
        FROM table_name
        WHERE condition) AS alias_name
FROM table_name;
```

接下来，让我们通过一个示例来说明列子查询的用法。假设我们有一个名为 `products` 的表，存储了产品的信息，包括产品价格。我们想要查询出每个产品的价格以及高于该价格的产品数量。

```sql
SELECT product_id, product_name, product_price,
       (SELECT COUNT(*)
        FROM products
        WHERE product_price > p.product_price) AS higher_price_count
FROM products p;
```

在这个查询中，子查询 `(SELECT COUNT(*) FROM products WHERE product_price > p.product_price)` 返回了每个产品价格高于当前产品价格的产品数量。这个子查询嵌套在了外部查询的 SELECT 语句中，并将返回的数量作为一个计算字段 `higher_price_count` 加入到了结果集中。

这种用法使得我们能够根据查询中的其他列值来动态地计算新的列值，并将其包含在查询的结果中。

##### 行子查询

行子查询是一种子查询，返回的结果集通常包含多个行和多个列。这种子查询可以嵌套在另一个查询的 FROM 子句中，**作为一个虚拟的表来使用**。

以下是行子查询的基本语法：

```sql
SELECT column1, column2, ...
FROM (
    SELECT subquery_columns
    FROM table_name
    WHERE condition
) AS subquery_alias;
```

在这个语法中，**子查询被包裹在圆括号中，并作为一个虚拟的表出现在外部查询的 FROM 子句中**。子查询的结果集可以包含多个行和多个列，并且可以使用别名来引用。

下面是一个示例，假设我们有两个表 `orders` 和 `customers`，其中 `orders` 存储了订单信息，而 `customers` 存储了客户信息。我们想要查询出每个客户的订单数量以及客户的其他信息。

```sql
SELECT c.customer_id, c.customer_name, o.order_count
FROM customers c
JOIN (
    SELECT customer_id, COUNT(*) AS order_count
    FROM orders
    GROUP BY customer_id
) AS order_summary
ON c.customer_id = order_summary.customer_id;
```

在这个查询中，行子查询 `(SELECT customer_id, COUNT(*) AS order_count FROM orders GROUP BY customer_id)` 返回了每个客户的订单数量。这个子查询嵌套在了外部查询的 FROM 子句中，并被命名为 `order_summary`。然后，外部查询将客户表和派生表 `order_summary` 进行了连接，以获取每个客户的订单数量及其他信息。

这种用法使得我们能够在查询中使用复杂的逻辑和聚合函数来获取子查询的结果集，并将其作为一个虚拟的表来使用。

##### 表子查询

MySQL 中的表子查询是**指一个 SELECT 语句作为另一个 SELECT 语句的 FROM 子句中的表**。也就是说，表子查询返回的是一个结果集，而不是单个值。这使得我们能够在一个查询中使用另一个查询的结果集作为表来进行进一步的操作。

以下是一个基本的表子查询语法：

```sql
SELECT column1, column2, ...
FROM (
    SELECT subquery_columns
    FROM table_name
    WHERE condition
) AS subquery_alias;
```

在这个语法中，子查询被包裹在圆括号中，并作为一个虚拟的表出现在外部查询的 FROM 子句中。子查询的结果集可以包含多个行和多个列，并且可以使用别名来引用。

接下来，让我们通过一个示例来说明表子查询的用法。假设我们有两个表 `orders` 和 `customers`，其中 `orders` 存储了订单信息，而 `customers` 存储了客户信息。我们想要查询出每个客户的订单数量以及客户的其他信息。

```sql
SELECT c.customer_id, c.customer_name, o.order_count
FROM customers c
JOIN (
    SELECT customer_id, COUNT(*) AS order_count
    FROM orders
    GROUP BY customer_id
) AS order_summary
ON c.customer_id = order_summary.customer_id;
```

在这个查询中，子查询 `(SELECT customer_id, COUNT(*) AS order_count FROM orders GROUP BY customer_id)` 返回了每个客户的订单数量。这个子查询嵌套在了外部查询的 FROM 子句中，并被命名为 `order_summary`。然后，外部查询将客户表和派生表 `order_summary` 进行了连接，以获取每个客户的订单数量及其他信息。

这种用法使得我们能够在查询中使用复杂的逻辑和聚合函数来获取子查询的结果集，并将其作为一个虚拟的表来使用。

## 事务

在 MySQL 中，事务（Transaction）是一组 SQL 查询语句，它们被视为一个不可分割的工作单元。事务具有以下特性：

1. **原子性（Atomicity）**：事务中的所有操作要么全部执行成功，要么全部失败回滚。如果事务中的任何一部分操作失败，则整个事务将被回滚到初始状态，不会造成数据损坏或不一致。
2. **一致性（Consistency）**：事务执行前后，数据库的完整性约束必须得到保持。即使在事务执行过程中发生了错误，数据库也必须保持一致性。
3. **隔离性（Isolation）**：事务的执行是独立于其他事务的。在一个事务执行过程中所做的修改在提交之前对其他事务是不可见的。
4. **持久性（Durability）**：一旦事务提交，其所做的修改将永久保存在数据库中，并且即使发生系统故障，修改也不会丢失。

在 MySQL 中，通过使用 BEGIN、COMMIT 和 ROLLBACK 语句来控制事务的开始、提交和回滚：

- BEGIN：开始一个事务。
- COMMIT：提交事务，使之生效。
- ROLLBACK：回滚事务，取消事务中的所有修改。

在 MySQL 中，`@@autocommit` 是一个系统变量，用于控制自动提交（autocommit）模式的开启或关闭。当 `@@autocommit` 的值为 1 时，表示自动提交模式开启；当 `@@autocommit` 的值为 0 时，表示自动提交模式关闭。

你可以使用 `SET` 语句来设置 `@@autocommit` 的值：

```sql
SET @@autocommit = 0; -- 关闭自动提交模式
```

或者：

```sql
SET @@autocommit = 1; -- 开启自动提交模式
```

需要注意的是，`@@autocommit` 的默认值是 1，即自动提交模式默认是开启的。**当自动提交模式开启时，每个 SQL 语句都会自动成为一个独立的事务**，并立即提交。**如果你关闭了自动提交模式，那么需要显式地调用 `COMMIT` 或 `ROLLBACK` 来提交或回滚事务。**

### 并发事务问题

并发事务问题是指当多个事务同时对数据库进行读写操作时可能出现的一系列问题，包括脏读、不可重复读和幻读。

1. **脏读（Dirty Read）**：一个事务读取到了另一个事务未提交的数据。如果一个事务在读取数据的同时，另一个事务对该数据进行了修改但尚未提交，那么第一个事务读取到的数据就是“脏”的，可能会导致不正确的结果。

2. **不可重复读（Non-Repeatable Read）**：一个事务内的两次读取操作之间，另一个事务对**相同数据**进行了修改并提交，导致第一个事务两次读取到的数据不一致。这种情况下，事务无法重复读取到一致的数据。

3. **幻读（Phantom Read）**：一个事务内的两次查询操作之间，另一个事务插入了**符合相同条件的新数据**，并提交了事务，导致第一个事务两次查询到的数据集不一致。这种情况下，事务无法保证在同一时间点内查询到一致的数据。

   **不可重复读是针对单行数据的修改，而幻读则是针对数据集合的变化。**

为了解决并发事务问题，数据库系统通常采用以下方法：

- 锁机制：数据库系统会对被访问的数据进行加锁，以保证在同一时间只有一个事务能够对数据进行读写操作，从而避免脏读、不可重复读和幻读等问题。
- 事务隔离级别：数据库系统提供了多种事务隔离级别，如读未提交、读提交、可重复读和串行化等级别，用于控制事务之间的隔离程度，以满足不同应用场景的需求。
- 乐观并发控制（Optimistic Concurrency Control）：数据库系统使用版本号或时间戳等机制来检测并发操作冲突，并在提交事务时检查数据是否被其他事务修改过，从而保证事务的一致性。

通过合理设计数据库架构、选择合适的事务隔离级别和使用合适的并发控制机制，可以有效地解决并发事务问题，并确保数据库系统的数据一致性和可靠性。

### 事务隔离级别

事务隔离级别是数据库管理系统中用来控制并发事务之间相互影响程度的一种机制。SQL 标准定义了四种事务隔离级别，包括：

#### 隔离级别分类

1. **读未提交（Read Uncommitted）**：最低的隔离级别，事务可以读取到其他**未提交**事务的数据修改。这种隔离级别可能会导致脏读、不可重复读和幻读等并发问题。
2. **读提交（Read Committed）**：事务只能读取到已提交的数据修改，避免了脏读问题。但是在同一个事务内的两次查询之间，其他事务提交的数据修改仍然可以被读取到，可能会导致不可重复读和幻读问题。
3. **可重复读（Repeatable Read）**：事务可以读取到其他事务**提交的**数据修改，但是在同一个事务内的多次查询操作之间，其他事务提交的数据修改不会被读取到，避免了不可重复读问题。但是仍然可能发生幻读问题。
4. **串行化（Serializable）**：最高的隔离级别，事务可以读取到其他事务提交的数据修改，同时在同一个事务内的多次查询操作之间，其他事务提交的数据修改也不会被读取到，避免了不可重复读和幻读问题。但是串行化隔离级别可能会导致并发性能下降，因为事务需要串行执行。

| 隔离级别 | 导致脏读 | 导致不可重复读 | 导致幻读 |
| :------: | :------: | :------------: | :------: |
| 读未提交 |    √     |       √        |    √     |
|  读提交  |    ×     |       √        |    √     |
| 可重复读 |    ×     |       ×        |    √     |
|  串行化  |    ×     |       ×        |    ×     |

#### 隔离级别查询

```sql
SELECT @@TRANSACTION_ISOLATION;
```

#### 隔离级别设置

```sql
SET SESSION TRANSACTION ISOLATION LEVEL <isolation_level>;
```

其中 `<isolation_level>` 可以是下列值之一：

- `READ UNCOMMITTED`：读未提交
- `READ COMMITTED`：读提交
- `REPEATABLE READ`：可重复读
- `SERIALIZABLE`：串行化

# MySQL进阶

## 存储引擎

###  引擎简介

在创建表时，指定存储引擎

```sql
CREATE TABLE 表名(
   字段1 字段类型1
   ...
)ENGINE=INNODB;
```

查看当前数据库支持的存储引擎

```sql
SHOW ENGINES;
```

### innoDB

#### 介绍

InnoDB 是 MySQL 中最常用的存储引擎之一，它提供了一系列的特性和优势，适用于大多数的应用场景。下面是一些 InnoDB 存储引擎的主要特点：

1. **事务支持**：InnoDB 支持事务，具有 ACID（原子性、一致性、隔离性、持久性）特性，可以确保数据的完整性和一致性。
2. **行级锁定**：InnoDB 使用行级锁定（Row-Level Locking）来提高并发性能，允许多个事务同时对同一表中的不同行进行读写操作，而不会发生阻塞。
3. **外键约束**：InnoDB 支持外键约束，可以在表之间建立引用完整性约束，确保数据的一致性。
4. **MVCC（多版本并发控制）**：InnoDB 使用 MVCC 来实现事务的隔离性，允许读取已提交的数据，而不会被正在进行的事务所阻塞。
5. **热备份**：InnoDB 支持在线热备份，可以在数据库运行时备份数据，无需停机。
6. **自动崩溃恢复**：InnoDB 支持自动崩溃恢复，当数据库发生异常情况（如崩溃或断电）时，可以自动恢复到最近一次的一致状态。
7. **自动增长**：InnoDB 支持自动增长列，可以在插入数据时自动递增。
8. **行级别的 MVCC**：InnoDB 使用行级别的 MVCC 实现读取一致性快照，从而在事务并发访问时提供一致性和隔离性。

#### 文件

xxx.ibd: xxx代表的是表名，innoDB引擎的每张表都会对应一个这样的表空间文件，存储该表的表结构（frm，sdi），数据和索引

参数：innodb_file_per_table

`innodb_file_per_table` 是 MySQL 中的一个配置选项，用于控制 InnoDB 存储引擎在创建表时是否将表数据和索引存储在单独的文件中。

默认情况下，当 `innodb_file_per_table` 被设置为 `OFF` 时，InnoDB 存储引擎会将所有用户表的数据和索引都存储在共享的系统表空间文件（通常是 `ibdata1`）中。这意味着所有表的数据和索引共享同一个文件。

而当 `innodb_file_per_table` 被设置为 `ON` 时，每个用户表的数据和索引将会存储在单独的 `.ibd` 文件中，每个文件对应一个表。这样的话，每个表都有自己的独立的存储文件。

#### 逻辑存储结构

InnoDB 存储引擎的逻辑存储结构包括了多个层次，从最高层的表空间到最底层的数据页，每个层次都有其特定的作用和组成：

1. **表空间（Tablespaces）**：
   - 表空间是 InnoDB 存储引擎中最高层的逻辑结构单元。
   - InnoDB 可以有多个表空间，每个表空间可以包含一个或多个段。
   - 表空间的作用是管理磁盘空间，用于存储表的数据和索引。
2. **段（Segments）**：
   - 段是表空间的下一层逻辑结构，每个段对应一个表或一个索引。
   - 段分为数据段和索引段，分别用于存储表的数据和索引数据。
   - 段由多个区组成。
3. **区（Extent）**：
   - 区是段的下一层逻辑结构，用于管理连续的数据页空间。
   - 每个区包含多个连续的数据页，通常为 1MB 大小。
4. **页（Pages）**：
   - 页是 InnoDB 存储引擎中的最小存储单位，用于存储表的数据和索引。
   - 默认情况下，每个页的大小为 16KB。
   - InnoDB 通过 **B+** 树来组织数据页，以实现高效的数据访问。
5. **行（Rows）**：
   - 行是存储在数据页中的实际数据记录。
   - 每行包含表的一个记录或索引的一个键值。
   - InnoDB 存储引擎以行级别来管理数据，支持高并发和事务操作。

### MyISM

#### 介绍

MyISAM 是 MySQL 中的一种存储引擎，它主要用于处理读取密集型的应用场景。

MyISAM 存储引擎在 MySQL 中有以下特点：

1. **读取性能优秀：** MyISAM 存储引擎对于读取操作（SELECT）具有很高的性能，特别是在面对大量读取请求的情况下。
2. **表级锁定：** MyISAM 使用表级锁定机制，这意味着在进行写入操作（INSERT、UPDATE、DELETE）时会锁定整个表，这样可以简化锁管理并提高读取操作的并发性。
3. **全文搜索索引支持：** MyISAM 支持全文搜索索引，使得在文本数据上进行高效的全文搜索成为可能。
4. **简单：** MyISAM 存储引擎相对较简单，易于理解和管理，因此在某些情况下，特别是对于小型或者简单的应用来说，它是一个不错的选择。
5. **存储空间效率：** MyISAM 存储引擎在一些情况下可能比 InnoDB 存储引擎更加节省存储空间，尤其是在存储非常大的表时。

#### 文件

xxx.sdi:存储表结构信息 system deployment image  系统部署映像

xxx.MYD:存储数据

xxx.MYI:存储索引

### Memory

#### 介绍

`MEMORY` 存储引擎（也称为 `HEAP`）是 MySQL 中的一种特殊存储引擎，它将表中的数据存储在内存中而不是磁盘上。这使得对于临时数据的快速访问成为可能，因为内存访问通常比磁盘访问要快得多。

以下是 `MEMORY` 存储引擎的一些特点：

1. **快速访问：** 数据存储在内存中，因此读取和写入操作非常快速。对于临时表或者缓存数据来说，这种存储引擎是一个理想的选择。
2. **易于使用：** 与其他存储引擎相比，`MEMORY` 存储引擎非常简单易用，因为它不需要关注磁盘管理和IO性能等方面的问题。
3. **临时数据存储：** `MEMORY` 存储引擎**通常用于存储临时数据**，例如临时表、查询结果集或者缓存数据等。

尽管 `MEMORY` 存储引擎具有快速访问和简单易用的特点，但它也有一些限制：

1. **数据大小受限：** `MEMORY` 存储引擎将数据存储在内存中，因此受到可用内存大小的限制。如果数据量过大，可能会导致内存不足或者性能下降。
2. **数据持久性：** 由于数据存储在内存中，数据库重启或者服务重启时，数据将会丢失。因此，`MEMORY` 存储引擎不适合存储需要持久性的数据。
3. **表级锁定：** `MEMORY` 存储引擎使用表级锁定，这意味着在进行写入操作时会锁定整个表，可能会影响并发性能。

综上所述，`MEMORY` 存储引擎适合用于临时数据存储或者对性能要求较高的场景，但需要注意其内存消耗和数据持久性等方面的限制。

#### 文件

xxx.sdi:存储表结构信息

### 存储引擎选择

选择存储引擎（如InnoDB、MyISAM和MEMORY）取决于你的具体需求和应用场景。以下是它们的一些特点和适用情况：

1. **InnoDB**：
   - **事务支持**：InnoDB是MySQL的默认存储引擎，支持事务和外键约束，适合需要数据完整性和事务支持的应用程序，比如金融系统或者电子商务平台。
   - **行级锁定**：InnoDB使用行级锁定，可以最大程度地减少并发操作时的冲突，适用于高并发的读写场景。
   - **崩溃恢复**：具有崩溃恢复能力，数据库在异常退出后可以通过日志文件进行恢复。

2. **MyISAM**：
   - **全文搜索**：MyISAM支持全文索引，适用于需要频繁进行全文搜索的应用场景，比如博客系统或者文档管理系统。
   - **高性能读取**：对于以读取为主的应用，MyISAM可以提供较好的性能表现。
   - **表级锁定**：MyISAM使用表级锁定，可能在并发写入场景下性能不如InnoDB。

3. **MEMORY**：
   - **内存存储**：数据存储在内存中，因此读取和写入速度非常快，适合用于临时数据存储或者缓存。
   - **不支持事务和崩溃恢复**：MEMORY存储引擎不支持事务和崩溃恢复，因此不适合需要数据持久性和完整性的应用场景。
   - **易丢失数据**：由于数据存储在内存中，系统关闭或者崩溃时，数据会丢失。

在做出选择时，需要权衡考虑数据库的性能需求、数据完整性、并发性要求以及应用程序的特性。通常情况下，建议优先选择InnoDB，因为它提供了最全面的功能和性能特性，除非应用场景对其他存储引擎的特性有特定需求。

## 索引

### 常见索引结构

MySQL 中常见的索引结构主要包括：

1. **B树索引（B-tree Index）**：B树索引是MySQL中最常见的索引类型。它是一种多路搜索树，能够高效地支持等值查找、范围查找和排序操作。B树索引适用于各种查询情况，并且在数据的插入和删除时能够自我平衡，保持树的平衡性能。

2. **哈希索引（Hash Index）**：哈希索引基于哈希表实现，能够实现快速的等值查找。但是，哈希索引并不支持范围查找和排序操作，并且在某些情况下可能存在哈希冲突问题，导致性能下降。

3. **全文索引（Full-text Index）**：全文索引用于对文本字段进行全文搜索，它可以快速地匹配文本中的关键词，并支持复杂的搜索操作，如布尔搜索和自然语言搜索。

4. **空间索引（Spatial Index）**：空间索引用于对空间数据进行快速的地理位置查询，如点、线、多边形等空间对象的搜索和分析。是myisam引擎的一种特殊索引类型

5. **前缀索引（Prefix Index）**：前缀索引是对字段值的前缀进行索引，而不是整个字段值。它可以减少索引的存储空间，并提高查询性能，但可能会导致索引选择不当的问题。

6. **复合索引（Composite Index）**：复合索引是对多个字段组合而成的索引，能够支持多列的等值查找和范围查找。合理设计复合索引可以提高查询性能，并减少索引的数量和存储空间。

7. **唯一索引（Unique Index）**：唯一索引要求索引列的值在整个表中唯一，用于保证数据的完整性和一致性。

这些不同类型的索引结构在不同的场景下具有不同的优势和适用性，合理选择和设计索引是提高数据库性能的关键因素之一。

#### B-Tree&B+ Tree

**B 树& B+树两者有何异同呢？**

- B 树的所有节点既存放键(key) 也存放数据(data)，而 B+树只有叶子节点存放 key 和 data，其他内节点只存放 key。
- B 树的叶子节点都是独立的;B+树的叶子节点有一条引用链指向与它相邻的叶子节点。
- B 树的检索的过程相当于对范围内的每个节点的关键字做二分查找，可能还没有到达叶子节点，检索就结束了。而 B+树的检索效率就很稳定了，任何查找都是从根节点到叶子节点的过程，叶子节点的顺序检索很明显。
- 在 B 树中进行范围查询时，首先找到要查找的下限，然后对 B 树进行中序遍历，直到找到查找的上限；而 B+树的范围查询，只需要对链表进行遍历即可。

MyISAM 引擎中，B+Tree 叶节点的 data 域存放的是数据记录的地址。在索引检索的时候，首先按照 B+Tree 搜索算法搜索索引，如果指定的 Key 存在，则取出其 data 域的值，然后以 data 域的值为地址读取相应的数据记录。这被称为“**非聚簇索引（非聚集索引）**”。

InnoDB 引擎中，其数据文件本身就是索引文件。相比 MyISAM，索引文件和数据文件是分离的，其表数据文件本身就是按 B+Tree 组织的一个索引结构，树的叶节点 data 域保存了完整的数据记录。这个索引的 key 是数据表的主键，因此 InnoDB 表数据文件本身就是主索引。这被称为“**聚簇索引（聚集索引）**”，而其余的索引都作为 **辅助索引** ，辅助索引的 data 域存储相应记录主键的值而不是地址，这也是和 MyISAM 不同的地方。在根据主索引搜索时，直接找到 key 所在的节点即可取出数据；在根据辅助索引查找时，则需要先取出主键的值，再走一遍主索引。 因此，在设计表的时候，不建议使用过长的字段作为主键，也不建议使用非单调的字段作为主键，这样会造成主索引频繁分裂

#### hash索引

哈希索引是一种基于哈希表实现的索引结构，用于加快数据库中数据的检索速度。与传统的B树索引相比，哈希索引的主要优势在于其快速的等值查找操作。

在哈希索引中，数据库系统会根据索引字段的值通过哈希函数计算出一个唯一的哈希值，然后将该哈希值映射到存储数据的位置。因此，在执行查询时，数据库系统只需要计算出待查询值的哈希值，然后直接通过哈希表查找对应的数据，而无需进行复杂的树状结构搜索。

哈希索引的优势包括：

1. **快速的等值查找：** 由于哈希索引直接映射到数据存储位置，因此在执行等值查找操作时速度非常快，时间复杂度为 O(1)。
2. **适用于大数据量：** 哈希索引适用于大数据量的情况，因为它可以通过哈希函数将数据分散到存储空间的不同位置，减少了对内存和磁盘的访问压力。
3. **内存效率高：** 哈希索引通常比B树索引占用更少的内存空间，因为它不需要存储额外的树状结构信息。

然而，哈希索引也存在一些限制：

1. **不支持范围查询和排序操作：** 由于哈希索引是基于哈希函数的等值查找，所以不支持范围查询和排序操作。
2. **哈希冲突：** 哈希函数的映射不一定是唯一的，可能会出现多个不同的键值映射到同一个哈希值的情况，这称为哈希冲突。为了解决冲突，通常需要使用哈希碰撞解决方法，如链地址法或开放地址法。

在**InnoDB**中，索引按照其存储形式可以分为以下两种主要类型：

1. **聚集索引（Clustered Index）：**

   - InnoDB 中的主键索引就是聚集索引。
   - 聚集索引的**叶子节点**存储了整个行的数据，而不仅仅是索引列的值。
   - 聚集索引决定了数据在磁盘上的物理存储顺序。数据行在聚集索引中的顺序取决于主键的值。

   聚集索引选取规则：

   1.如果存在主键，主键索引就是聚集索引

   2.如果不存在主键，将使用第一个唯一索引作为聚集索引

   3.如果表没有主键，或没有合适的唯一索引，则innoDB会自动生成一个rowid作为隐藏的聚集索引

   在使用自增长列作为主键时，MySQL会隐式地为表添加一个名为"ROWID"的概念。这个ROWID并不是一个实际存在的列，而是指代自增长列所生成的唯一标识符。通过自增长列，MySQL能够快速定位到每一行的物理存储位置。

2. **辅助索引（Secondary Index）：**

   - 辅助索引也称为非聚集索引。
   - 辅助索引的**叶子节点存储了索引列的值和指向对应数据行的聚集索引键**。
   - 辅助索引的顺序与索引列的值有关，而不是与数据行的物理存储顺序相同。

在InnoDB中，每个表可以有一个聚集索引（主键索引），而可以有多个辅助索引（非聚集索引）。**这种分类基于索引在磁盘上的物理存储形式**，以及索引在查询优化中的不同作用。

##### 主键索引（聚集索引）

主键索引是根据表的主键（Primary Key）字段创建的索引。它的特点包括：

- **数据存储**：在主键索引中，索引的每一个节点都包含整个数据行的数据。也就是说，主键索引的索引节点（B+树的叶子节点）中存储了整个数据行的内容，而不仅仅是主键值。这样设计的目的是为了能够通过主键索引直接访问到所需的数据行，而不需要再次访问数据表本身。

- **物理存储顺序**：主键索引通常也被称为聚集索引（Clustered Index），因为数据行的物理存储顺序（即磁盘上的布局顺序）与主键索引的逻辑顺序一致。这使得通过主键索引进行范围查询或者顺序扫描更加高效。

  ![主键索引](C:\Users\Administrator\Desktop\主键索引.png)

##### 辅助索引（非聚集索引）

辅助索引是针对除主键之外的其他列或列组合创建的索引。它的特点包括：

- **索引键值**：辅助索引的索引节点中存储的是索引列的值，而不是整个数据行的数据。换句话说，辅助索引的索引节点包含了索引列的值以及指向对应数据行的指针（或主键值），而不是实际的数据行。
- **指向主键值**：辅助索引的数据域（或称为索引项）存储的是主键值或者主键的引用，而不是整个数据行的完整内容。这样设计的目的是通过辅助索引快速定位到主键值，然后再通过主键索引获取完整的数据行。

总结起来，主键索引的数据域确实存储的是整个数据行的数据，而辅助索引的数据域存储的是主键值或者主键的引用。

![辅助索引](C:\Users\Administrator\Desktop\辅助索引.png)

InnoDB 存储引擎在设计辅助索引（Secondary Index）时，将叶子节点存储主键值而不是完整的数据行，是基于一些数据库设计和性能优化的考虑：

1. **减少存储空间消耗**：
   - 如果辅助索引的叶子节点存储完整的数据行，将会占用大量的存储空间，特别是在表包含大量列或大量数据的情况下。通过仅存储主键值，可以大大减少索引的物理存储空间需求，节约存储成本。
2. **提高索引效率**：
   - 辅助索引的主要目的是加速数据检索，尤其是在涉及到非主键列的查询时。存储主键值而不是整个数据行，可以减少每个索引节点的大小，使得索引结构更加紧凑，提高了索引的查询效率。
3. **避免冗余数据存储**：
   - 数据行通常会在主键索引中存储，如果辅助索引叶子节点也存储完整的数据行，会导致数据的冗余存储。通过存储主键值，可以避免这种冗余，同时保证索引能够有效地定位和访问数据行。
4. **支持覆盖索引**：
   - 存储主键值使得辅助索引能够支持覆盖索引（Covering Index）的优化技术。覆盖索引指的是当查询的结果可以完全通过索引来返回，而不需要访问表的实际数据行。这种优化可以显著减少磁盘 I/O 和内存使用，提高查询性能。
5. **更快的索引扫描**：
   - 当使用辅助索引进行查询时，数据库引擎可以通过主键值直接访问主键索引，从而获取完整的数据行。这样的设计可以降低额外的索引遍历和查找开销，进一步提高查询性能。

**唯一索引，普通索引，前缀索引等索引都属于二级索引。**

**唯一索引(Unique Key)**:唯一索引也是一种约束。唯一索引的属性列不能出现重复的数据，但是允许数据为 NULL，一张表允许创建多个唯一索引。 建立唯一索引的目的大部分时候都是为了该属性列的数据的唯一性，而不是为了查询效率。

**普通索引(Index)**:普通索引的唯一作用就是为了快速查询数据，一张表允许创建多个普通索引，并允许数据重复和 NULL。

**前缀索引(Prefix)**:前缀索引只适用于字符串类型的数据。前缀索引是对文本的前几个字符创建索引，相比普通索引建立的数据更小，因为只取前几个字符。

### 索引语法

#### 创建索引

##### 创建单列索引

```sql
CREATE INDEX index_name ON table_name (column_name);
```

其中，`index_name` 是索引的名称，`table_name` 是要创建索引的表名，`column_name` 是要创建索引的列名。

##### 创建唯一索引

```sql
CREATE UNIQUE INDEX index_name ON table_name (column_name);
```

这个语法与创建单列索引的语法相似，只是在索引名称后面添加了 `UNIQUE` 关键字，表示创建的索引是唯一的。

##### 创建复合索引

```sql
CREATE INDEX index_name ON table_name (column1_name, column2_name, ...);
```

在括号内列出要创建索引的所有列名，这样就创建了一个包含多个列的复合索引。

##### 创建全文索引

全文索引用于在文本列上进行全文搜索。在MySQL中，只有MyISAM和InnoDB存储引擎支持全文索引。创建全文索引的语法如下：

```sql
CREATE FULLTEXT INDEX index_name ON table_name (column_name);
```

##### 建表的时候建立索引

```sql
CREATE TABLE `student` (
  `id` int NOT NULL,
  `name` varchar(100) DEFAULT NULL,
  `class` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `name_class_idx` (`name`,`class`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```



#### 查看索引

```sql
SHOW INDEX FROM table_name;
```

这个语句将显示指定表（`table_name`）上的所有索引信息，包括索引名称、索引类型、索引所在列等。

如果想要查看特定索引的信息，可以使用`WHERE`子句指定索引名称：

```sql
SHOW INDEX FROM table_name WHERE Key_name = 'index_name';
```

这个语句将只显示名为`index_name`的索引信息。

#### 验证索引

创建索引后，可以使用 `SHOW INDEX` 命令或者 `EXPLAIN` 命令来验证索引是否被正确创建，并且是否被数据库优化器使用。

#### 删除索引

删除索引并不会删除索引所在的列，只会删除索引本身。

```sql
DROP INDEX index_name ON table_name;
```

### 性能分析

#### 查询频次

```sql
SHOW GLOBAL STATUS LIKE 'Com%';
```

这个查询将显示以"Com"开头的全局状态变量，这些变量通常与执行的命令（比如SELECT、INSERT、UPDATE等）相关。这些状态变量的名称包含了"Com"前缀，因此我们使用`LIKE 'Com%'`来匹配它们。

执行该命令将返回以"Com"开头的全局状态变量及其当前值。这些变量包括了MySQL服务器接收和处理各种类型命令的计数信息，对于监控数据库的性能非常有用。

#### show profiles

在 SQL 数据库中，查询分析（query profiling）通常是指跟踪和记录执行 SQL 查询的性能指标和统计信息的过程。这可以帮助开发人员和数据库管理员识别潜在的性能瓶颈，并优化查询以提高数据库性能。

查询分析可以提供的信息包括：

1. **执行时间**：每个查询的总执行时间。
2. **扫描行数**：查询过程中扫描的行数。
3. **使用的索引**：查询是否使用了索引，以及使用的是哪些索引。
4. **执行计划**：查询的执行计划，即数据库引擎执行查询的方式。
5. **锁定和等待**：查询执行时涉及的锁定情况和等待事件。

你可以通过启用查询分析功能来收集这些信息。具体方法可能因数据库管理系统而异。例如，在 MySQL 中，你可以通过执行以下语句启用查询分析：

```sql
SET profiling = 1;
```

然后，执行你想要分析的查询。完成后，你可以使用以下语句查看查询分析结果：

```sql
SHOW PROFILES;
```

这将显示每个已执行查询的分析结果，包括执行时间等信息。你还可以使用以下语句查看特定查询的分析详细信息：

```sql
SHOW PROFILE FOR QUERY <query_id>;
```

在这里，`<query_id>` 是你想要查看的查询的 ID。

查看 query_id的sql语句cpu的使用情况

```sql
show profile cpu for query <query_id>;
```

#### explain

在 SQL 中，`EXPLAIN` 是一个用于查询执行计划的关键字。通过 `EXPLAIN` 关键字，你可以查看数据库系统将如何执行特定查询的详细信息，包括使用的索引、连接类型和执行顺序等。这对于优化查询性能非常有用。

在 MySQL 中，你可以像这样使用 `EXPLAIN` 关键字：

```sql
EXPLAIN your_query;
```

其中 `your_query` 是你想要分析的 SQL 查询。

例如，如果你有一个简单的 `SELECT` 查询：

```sql
SELECT * FROM your_table WHERE column_name = 'some_value';
```

你可以通过添加 `EXPLAIN` 关键字来获取执行计划：

```sql
EXPLAIN SELECT * FROM your_table WHERE column_name = 'some_value';
```

执行结果将会是一张表，其中列出了查询的执行计划，包括每个步骤的执行顺序、使用的索引、扫描的行数等信息。

`EXPLAIN` 命令执行后，返回的结果都包含一组字段，每个字段提供了关于查询执行计划的不同方面的信息。以下是常见的一些字段及其含义：

1. **id**：每个查询操作的唯一标识符。这个标识符在执行计划的各个步骤中保持不变，可以用于跟踪查询的执行流程。
2. **select_type**：描述了查询的类型。常见的类型包括 SIMPLE（简单查询）、PRIMARY（主查询）、SUBQUERY（子查询）等。
3. **table**：涉及到的表名。如果是子查询，可能会显示为 `<subquery>`。
4. **type**：访问表的方式。常见的类型包括：
   - `ALL`：全表扫描
   - `INDEX`：索引扫描
   - `UNIQUE INDEX`：唯一索引扫描
   - `FULLTEXT`：全文索引扫描
   - `RANGE`：范围扫描
   - `REF`：引用表之间的外键关系
   - `SYSTEM`：系统表扫描
   - `CONST`：常量查询
5. **possible_keys**：可能应用的索引。这列显示的是查询涉及的可能用到的索引，但不一定会实际使用。
6. **key**：实际使用的索引。如果查询使用了索引，这个字段将显示用于查询的索引名称。
7. **key_len**：索引使用的长度。表示索引中使用的字节数。
8. **ref**：索引匹配的列。如果查询使用了索引，则此列将显示索引所用的列。
9. **rows**：估计的查询结果行数。这是优化器估算的结果集的大小。
10. **filtered**：按表条件过滤后，留存的记录数的百分比
11. **Extra**：额外的信息。这个字段可能包含一些额外的信息，如是否使用了临时表、文件排序等。

### 索引使用

#### 最左前缀法则

最左前缀法则是指在使用索引时，如果索引是一个复合索引（即包含多个列），那么查询语句中的条件必须从索引的最左边列开始，并且连续的使用索引的列。

#### 索引失效

索引失效是指数据库查询执行时未能有效利用索引的情况，导致查询性能下降或者需要进行全表扫描。

索引失效可能由多种情况导致，以下是一些常见的导致索引失效的情况：

1. **头部模糊查询**：例如 `LIKE 'prefix%'`，由于通配符 `%` 的使用，MySQL 无法利用索引来加速查询。

2. **尾部模糊查询**：例如 `LIKE '%suffix'`，同样由于通配符 `%` 的使用，索引可能失效。

3. **包含通配符的查询**：例如 `LIKE '%keyword%'`，通配符位于查询条件的中间，索引可能无法被有效利用。

4. **不等于（!=）查询**：对于不等于操作符（`!=`），MySQL 可能不会使用索引，而是执行全表扫描。

5. **使用函数操作**：对索引列进行函数操作，如 `UPPER()`、`LOWER()`、`TRIM()` 等，可能导致索引失效。

6. **类型转换**：如果在查询中对索引列进行了类型转换，可能导致索引失效。

7. **范围查询不满足最左前缀法则**：当在复合索引上进行范围查询时，如果查询条件不满足最左前缀法则，右边的列可能会失效。

8. **使用 OR 操作符连接多个条件**：当使用 OR 操作符连接多个条件时，索引可能会失效，特别是当条件涉及不同的列时。查询条件中使用 OR，且 OR 的前后条件中有一个列没有索引，涉及的索引都不会被使用到;

9. **数据分布不均匀**：如果索引列的数据分布不均匀，某些值的重复性很高，MySQL 可能会选择不使用索引，而是执行全表扫描。

10. **索引过多**：如果对数据库表创建了过多的索引，可能会导致索引失效，因为 MySQL 在执行查询时需要选择合适的索引，如果索引太多，可能会增加选择的难度。

**综上所述，索引失效的情况是多方面的，需要根据具体的查询需求和数据情况来选择合适的索引和查询方式，以尽量避免索引失效导致的性能问题**。

#### 索引优化

在 MySQL 中，可以使用一些指令和技巧来优化索引查询。以下是一些常用的索引查询优化指令和技巧：

**EXPLAIN**：使用 `EXPLAIN` 命令可以分析询语句的执行计划，包括查询将如何执行以及使用的索引等信息。通过分析执行计划，可以发现可能存在的性能瓶颈，并做出相应的优化。

```sql
EXPLAIN SELECT * FROM table_name WHERE condition;
```

**FORCE INDEX**：使用 `FORCE INDEX` 可以强制 MySQL 使用指定的索引来执行查询，即使优化器认为其他索引更合适。这在某些情况下可能会提高查询性能。

```sql
SELECT * FROM table_name FORCE INDEX (index_name) WHERE condition;
```

**USE INDEX**：类似于 `FORCE INDEX`，但 `USE INDEX` 会尝试使用指定的索引，如果索引不存在则会退化为全表扫描。

```sql
SELECT * FROM table_name USE INDEX (index_name) WHERE condition;
```

**IGNORE INDEX**：`IGNORE INDEX` 可以告诉 MySQL 在执行查询时忽略指定的索引，即使该索引可能会被选择。

```sql
SELECT * FROM table_name IGNORE INDEX (index_name) WHERE condition;
```

**ANALYZE TABLE**：使用 `ANALYZE TABLE` 命令可以更新表的统计信息，帮助 MySQL 优化查询计划和索引选择。

```sql
ANALYZE TABLE table_name;
```

**OPTIMIZE TABLE**：使用 `OPTIMIZE TABLE` 命令可以优化表的存储结构和索引，释放未使用的空间并更新索引统计信息。

```sql
OPTIMIZE TABLE table_name;
```

**覆盖索引查询**：设计查询语句使其只使用索引就可以获取所需数据，而不需要访问表的实际行数据。这可以通过仅选择索引列或者使用 `FORCE INDEX` 来实现。

**分区表**：将大表分割成多个分区可以提高查询性能，因为查询只需要在特定的分区上执行，而不需要扫描整个表。

#### 覆盖索引

覆盖索引（Covering Index）是一种特殊的索引，它包含了查询语句中所需的所有列，因此可以完全覆盖查询的需求，而无需回表到数据页中查找。这可以提高查询性能，因为不需要额外的IO操作去读取数据页。 key值=要查找的值

具体来说，当查询语句中的列都包含在索引中时，数据库引擎就可以直接从索引中获取所需的数据，而无需再次访问数据页。这样就可以减少IO操作，提高查询速度，特别是对于范围查询、排序和聚合查询等操作。

#### 回表查询

"回表查询"是指在使用非覆盖索引时，当数据库引擎通过索引定位到数据行后，需要再次回表（Table Lookup）到数据页中查找完整的数据行的过程。这是因为非覆盖索引只包含了部分查询中所需的列，无法完全满足查询的需求，因此需要进一步访问数据页来获取完整的数据行。

回表查询通常发生在以下情况下：

当查询命中了非覆盖索引时，数据库引擎首先使用索引定位到数据行。然后，数据库引擎根据索引中存储的指针信息，再次访问数据页，以获取完整的数据行。

#### EXTRA字段

在MySQL的`EXPLAIN`语句的执行计划中，"Extra"字段提供了有关查询执行的额外信息。这些额外信息可以帮助你更好地理解查询的执行过程和性能特征。以下是一些常见的"Extra"字段中可能包含的信息及其含义：

1. **Using index**：表示查询中使用了索引，但是不需要回表到数据行中查找额外的信息。这种情况下，查询可以直接从索引中获取所需的数据。
2. **Using index condition**：表示查询使用了覆盖索引，并且在索引层面上进行了条件过滤。这意味着查询中使用的条件可以在索引中评估，而无需回表到数据行。
3. **Using where**：表示查询中使用了WHERE子句进行条件过滤。这可能需要回表到数据行中查找符合条件的数据。
4. **Using filesort**：表示查询中需要对结果进行排序，但是无法使用索引的顺序来完成排序，因此需要额外的排序操作，可能会导致性能下降。
5. **Using temporary**：表示查询中需要使用临时表来存储中间结果，通常是因为涉及到了GROUP BY操作或者DISTINCT操作，需要对数据进行汇总或者去重处理。
6. **Using join buffer**：表示查询中使用了连接缓冲区来处理连接操作，通常是在连接操作中使用了索引范围扫描时出现的。
7. **Using index for group-by**：表示查询中使用了索引来执行GROUP BY操作。
8. **Using index for distinct**：表示查询中使用了索引来执行DISTINCT操作。
9. **Impossible WHERE**：表示查询中的WHERE条件不可能为真，因此查询将返回空结果集。
10. **No tables used**：表示查询中没有涉及到表的访问，通常是由于查询使用了常量或者内置函数。

这些是常见的一些"Extra"字段中可能包含的信息。根据具体情况，"Extra"字段可能还会包含其他类型的信息，但以上列举的是一些常见的情况。

#### 前缀索引

前缀索引是一种特殊类型的索引，它只索引列值的一部分而不是整个列值。这可以有效地减少索引的大小，从而节省磁盘空间并提高查询性能，尤其是在处理大量重复值的情况下。

以下是创建前缀索引的语法：

```sql
CREATE INDEX index_name ON table_name (column_name(prefix_length));
```

其中：

- `index_name` 是你要创建的索引的名称。
- `table_name` 是包含要创建索引的列的表的名称。
- `column_name` 是要索引的列的名称。
- `prefix_length` 是要索引的列的前缀长度，以字符为单位

##### 前缀长度的选择

索引的选择性是指索引中不同值的数量与表中行数的比率。具体来说，索引的选择性越高，表示索引中的不同值越多，重复值越少，这意味着查询时通过索引能够更精确地定位到所需的行，从而提高查询性能。

选择性可以用以下公式来计算：

选择性=不同值的数量总行数\text{选择性} = \frac{\text{不同值的数量}}{\text{总行数}}选择性=总行数不同值的数量

选择性的取值范围是从0到1。**如果选择性为1，表示索引中每个值都是唯一的，这是最理想的情况**。而如果选择性为0，表示所有的行都具有相同的索引值，这种情况下索引就没有什么用处。

常用到的两个语句

```sql
select count(distinct column_name)/count(*) from table_name;
select count(distinct substring(column_name,起始位置,长度))/count(*) from table_name
SELECT COUNT(DISTINCT LEFT(column_name, prefix_length)) / COUNT(*) FROM table_name;
```

### 设计原则

1.针对数据量较大，查询比较频繁的表建立索引

2.针对常作为查询条件（where)，排序（order by），分组（group by）操作的字段建立索引

3.尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高

4.如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引

5.尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率

6.要控制索引的数量，索引越多，维护索引结构的代价就越大，会影响增删改的效率

7.如果索引不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好的确定哪个索引最有效



## SQL优化

### 插入数据

#### insert优化

批量插入

手动事务提交

主键顺序插入

大批量插入数据

使用mysql的load指令进行插入

```sql
#客户端连接服务时，加上参数
mysql--local-infile-u root -p
#设置全局参数（local_infil为1.开启本地加载文件导入数据的相关
set global local_infile=1  ;
#执行load指令将准备好的数据，加到表结构中
load data local infite '/not/sql.vlog' into table 'tb_user' fields terminated by ',' lines terminated by'\n'
```

### 主键优化

#### 页分裂

页可以为空，也可以填充一半，也可以填充100%，每个页包含了2-N行的数据

#### 页合并

当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记为删除并且它的空间变得允许被其它记录声明使用

当页中删除得记录达到MERGE_THRESHOLD(默认为页得50%)，innoDB会开始寻找最靠近得页看看是否可以将两个页合并以优化空间使用

#### 主键设计原则

满足业务需求得情况下，尽量降低主键得长度

插入数据时，尽量选择顺序插入，选择使用AUTO_INCREMENT自增主键

尽量不要使用UUID做主键或者是其它自然主键，如身份证号

### order by 优化

using filesort:通过表的索引或者全表扫描，读取满足条件的数据行，然后再排序缓冲区sort buffer中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫Filesort排序

using index：通过有序索引排序扫描直接返回有序数据，这种情况即为using index，不需要额外排序，操作效率高

根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则

尽量使用覆盖索引

多字段排序，一个升序一个降序，此时需要注意联合索引再创建时的规则（ASC/DESC）

如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小sort_buffer_size（默认256K）

1.查看sort_buffer_size默认值，默认值为256K

```sql
show variables like 'sort_buffer_size';

```

2.设置global级别的sort_buffer_size值，设置sort_buffer_size=1M

```sql
 SET GLOBAL sort_buffer_size = 1024*1024;
```

3.设置session级别的sort_buffer_size值，设置sort_buffer_size=2M

```sql
SET session sort_buffer_size = 2*1024*1024;
mysql> show variables like 'sort_buffer_size';
+------------------+---------+
| Variable_name    | Value   |
+------------------+---------+
| sort_buffer_size | 2097152 |
+------------------+---------+
```

4.设置永久生效，需要修改/etc/my.cnf文件，[重启Mysql](https://so.csdn.net/so/search?q=重启Mysql&spm=1001.2101.3001.7020)后生效

```sql
[mysqld]
# 固定Mysql sort_buffer_size参数
sort_buffer_size=1M
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=D:\mysql-8.0.25-winx64  # 切记此处一定要用双斜杠\\，单斜杠我这里会出错
# 设置mysql数据库的数据的存放目录
datadir=D:\mysql-8.0.25-winx64\\data   # data目录之后初始化会自动创建
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8mb4
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8mb4
```

### group by 优化

分组时通过索引优化，使得using temporary转化成using index

### limit 优化

优化`LIMIT`语句的关键在于合适的索引使用、避免全表扫描、优化查询条件和合理使用分页技术

### count优化

MyISAM引擎把一个表的总行数存在了磁盘上，因此执行count（*）的时候会直接返回这个数

innoDB引擎，需要把数据一行一行读出，累积计数

`COUNT(1)` 和 `COUNT(*)` 会统计表中的所有行数，包括值为 `NULL` 的行。

count（column_name)计算多少条记录值不为NULL

当使用 `COUNT(主键列)` 进行计数时，它会统计主键列中非空（即非 `NULL`）值的行数

### update优化

当在 InnoDB 存储引擎中执行没有索引的 `UPDATE` 操作时，锁定行为通常会升级为表级锁。这是因为没有索引时，InnoDB 无法以有效的方式定位并锁定特定的行，而是需要锁定整个表，以确保更新操作的原子性和一致性。

innoDB的行锁是针对索引加的锁，不是针对记录加的锁，并且该索引不能失效，否则会从行锁升级成表锁

## 视图

MySQL中的视图（View）是一个虚拟的表，它是基于一个或多个查询的结果集构建的。视图本身并不存储数据，而是通过执行定义视图时指定的查询来动态生成结果集。

**视图的用途**：

- 简化复杂查询：将复杂的查询逻辑封装在视图中，简化应用程序中的查询语句。
- 数据安全性：通过视图可以隐藏表的部分列或者行，限制用户的访问权限。
- 重用查询逻辑：多个应用程序可以共享同一个视图，避免重复编写相同的查询逻辑。

### 创建视图

```sql
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

### 更新视图

```sql
UPDATE view_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

- 视图更新必须遵循视图的可更新性规则，即视图的定义必须允许进行更新操作。
- 更新视图时，实际上是更新视图所依据的基础表（或视图）中的数据。

### 删除视图

```sql
DROP VIEW view_name;
```

### 修改视图

在 MySQL 中，不能直接修改视图的定义，而是需要先删除原有的视图，然后重新创建。

### 查询视图

```sql
SELECT * FROM view_name;
```

在 MySQL 中，视图的检查选项通常指的是创建视图时可以设置的 `WITH CHECK OPTION` 子句。这个子句用于确保视图中的数据更新操作（如插入、更新）符合视图的定义条件。

### 视图的检查选项

1. **作用**：

   - `WITH CHECK OPTION` 确保任何插入或更新操作都不会使得视图中的行不再满足视图的定义条件。

2. **用法**：

   - 在创建视图时，可以使用 

     ```
     WITH CHECK OPTION
     ```

      来确保视图的数据更新符合视图定义的条件。例如：

     ```sql
     CREATE VIEW view_name AS
     SELECT column1, column2, ...
     FROM table_name
     WHERE condition
     WITH CHECK OPTION;
     ```

     当视图使用了 `WITH CHECK OPTION` 后，只有那些符合视图 `WHERE` 子句条件的行才可以被插入或更新如果插入或更新导致视图中的行不再满足 `WHERE` 子句的条件，MySQL 将会抛出错误并拒绝操作。此外，如果某一个视图是基于另一个视图创建的时候，插入或更新还需满足对应视图的where条件

   Cascade（级联）

   级联更新（CASCADE）：当视图中的数据被更新时，级联更新会将更新操作传播到视图基础表中。这意味着对视图的任何更新操作（如插入、更新、删除）都会反映到视图基础表中对应的行上。

   Local（本地）

   本地更新（LOCAL）：本地更新与级联更新相反，它指定更新操作仅影响视图本身，而不会影响视图基础表中的数据。这意味着对视图的更新操作仅影响视图的结果集，而不会对基础表进行任何实际的数据更改。

   ### 视图的更新

   1. **基于单表的简单视图**：

      - 如果视图是基于单个表而不包含以下元素，则视图通常可以进行更新操作：

        - 聚合函数（如SUM()、COUNT()等）
        - `DISTINCT`
        - `GROUP BY`
        - `HAVING`
        - 子查询

      - 例如，假设有一个名为 `active_users` 的视图，显示所有状态为 'Active' 的用户：

        ```sql
        CREATE VIEW active_users AS
        SELECT id, name, status
        FROM users
        WHERE status = 'Active';
        ```

        那么可以对 `active_users` 视图进行更新操作，如：

        ```sql
        UPDATE active_users
        SET status = 'Inactive'
        WHERE id = 1;
        ```

        这个更新操作将会反映到 `users` 表中对应的行上，将 `id` 为1的用户状态更新为 'Inactive'。

   2. **具有联结和必要条件的视图**：

      - 如果视图涉及多个表的联结，并且满足一定的更新条件，则也可以进行更新操作。这种情况下，MySQL 会根据视图定义中的条件来确定如何更新底层表。

   ### 不可更新视图

   视图通常是不可更新的，如果它包含以下情况之一：

   - 包含聚合函数或`DISTINCT`。
   - 使用了`GROUP BY`或`HAVING`子句。
   - 包含`UNION`操作。
   - 包含子查询。
   - 定义中使用了计算字段或表达式，而不是直接的列名。

   尝试对不可更新的视图执行更新操作时，数据库管理系统通常会返回错误消息，指出视图不满足更新条件。

   ### 更新操作示例

   假设有一个名为 `orders_view` 的视图，它基于 `orders` 和 `customers` 表的联结，并显示订单信息及其对应客户的姓名：

   ```sql
   CREATE VIEW orders_view AS
   SELECT o.order_id, o.order_date, c.customer_name
   FROM orders o
   JOIN customers c ON o.customer_id = c.customer_id;
   ```

   可以对 `orders_view` 进行更新操作，例如插入新的订单信息：

   ```sql
   INSERT INTO orders_view (order_id, order_date, customer_name)
   VALUES (1001, '2024-06-26', 'John Doe');
   ```

   这个插入操作将会根据视图的定义，向 `orders` 表中插入新的订单记录，并确保对应的客户信息在 `customers` 表中存在。

## 存储过程

存储过程是事先经过编译并存储在数据库中的一段SQL语句的集合

### 语法

#### 定义存储过程

```sql
CREATE PROCEDURE procedure_name(param1 datatype, param2 datatype)
BEGIN
    -- SQL statements
END;
```

- `CREATE PROCEDURE`: 创建存储过程的关键字。
- `procedure_name`: 存储过程的名称。
- `(param1 datatype, param2 datatype)`: 存储过程的参数列表，可以包括输入参数、输出参数或者同时包含两者。
- `BEGIN ... END`: 存储过程体，包含实际的SQL语句和控制流语句。
- `END;`: 存储过程定义的结束标记。

##### 参数传递

存储过程可以接受零个或多个参数，分为输入参数、输出参数和输入输出参数：

```sql
CREATE PROCEDURE procedure_name(IN param1 datatype, OUT param2 datatype, INOUT param3 datatype)
BEGIN
    -- SQL statements
END;
```

- `IN param1`: 输入参数，存储过程内部可以读取但不能修改。
- `OUT param2`: 输出参数，存储过程可以修改其值，并且调用者可以读取修改后的值。
- `INOUT param3`: 输入输出参数，既可以在存储过程内部读取和修改，也可以返回给调用者。

##### 变量声明

在存储过程中，可以使用变量来存储中间结果或者参数值：

```sql
DECLARE variable_name datatype [DEFAULT initial_value];
```

- `DECLARE`: 声明变量的关键字。
- `variable_name`: 变量名称。
- `datatype`: 变量的数据类型。
- `DEFAULT initial_value`: 可选项，指定变量的初始值

#### 查看存储过程

可以使用 `SHOW CREATE PROCEDURE` 命令来查看特定存储过程的定义

```sql
SHOW CREATE PROCEDURE procedure_name;
```

另一种查看存储过程的方法是查询 `information_schema` 数据库中的 `ROUTINES` 表。这个表包含了所有数据库中的存储过程、函数以及触发器的信息。

```sql
SELECT ROUTINE_DEFINITION
FROM information_schema.ROUTINES
WHERE ROUTINE_TYPE = 'PROCEDURE' AND ROUTINE_NAME = 'procedure_name';
```

#### 删除存储过程

```sql
DROP PROCEDURE [IF EXISTS] procedure_name;
```

#### 调用存储过程

```sql
CALL procedure_name(parameter1, parameter2, ...);
```

**在命令行中，执行创建存储过程的SQL时，需要通过关键字delimiter指定SQL语句的结束符**

### 变量

#### 系统变量

在MySQL中，系统变量分为全局变量和会话（session）变量，它们用于控制和管理MySQL服务器和会话的行为和状态。下面介绍一些常见的MySQL系统变量及其作用：

##### 全局变量

全局变量是影响整个MySQL服务器实例的配置参数，需要超级用户或者具有 `SUPER` 权限的用户才能修改。

- **`max_connections`**: 控制服务器允许的最大连接数。
- **`innodb_buffer_pool_size`**: InnoDB存储引擎使用的缓冲池大小。
- **`query_cache_size`**: 查询缓存的大小，用于存储查询结果。
- **`tmp_table_size`**: 临时表的最大大小限制。
- **`max_allowed_packet`**: 定义单个网络数据包或查询的最大大小。

##### 会话变量

会话变量是针对每个连接到MySQL服务器的客户端会话（session）的特定配置参数，其值在会话结束时被重置或者显式修改。

- **`autocommit`**: 控制事务自动提交的行为。
- **`sql_mode`**: 控制SQL语法和数据验证的严格性和标准。
- **`time_zone`**: 定义当前会话的时区设置。
- **`character_set_client`**: 定义客户端发送到服务器的字符集。
- **`collation_connection`**: 定义连接的排序规则。

##### 查看系统变量

```sql
#查看所有系统变量  默认golobal
SHOW [SESSION/GLOBAL]VARIABLES;
#通过LIKE模糊匹配方式查找变量
SHOW[SESSION/GLOBAL]VARIABLES LIKE '...'
#查看指定变量的值
SELECT @@[SESSION/GLOBAL]系统变量名;
```

##### 设置系统变量

```sql
SET [SESSION/GLOBAL]系统变量名=值;
SET @@[SESSION/GLOBAL]系统变量名=值;
```

#### 用户定义变量

在MySQL中，除了系统变量外，还可以使用用户定义变量。这些变量是在SQL会话中自定义的，主要用于存储临时的中间结果或者作为参数传递给存储过程或查询。

用户定义的变量无需对其进行声明或者初始化，只不过获取到的 值为NULL

##### 用户定义变量的语法

用户定义变量以 `@` 符号开头，后面跟着变量名。在使用变量之前，需要通过 `SET` 或者直接在查询中赋值来初始化它们。

###### 初始化变量

```sql
SET @variable_name = value;
SET @variable_name := value;
SELECT @variable_name := value;
SELECT COLUMN_NAME INTO @variable_name FROM TABLE_NAME;
```

###### 使用变量

```sql
SELECT @variable_nam
```



#### 局部变量

在MySQL中，局部变量是在存储过程或函数内部定义和使用的变量。它们主要用于存储和处理过程或函数执行过程中的临时数据，仅在其所属的存储过程或函数内部有效，不能跨过程或函数访问。

##### 声明局部变量

局部变量的声明通常在存储过程或函数的开头部分，使用 `DECLARE` 关键字：

```sql
DECLARE variable_name datatype [DEFAULT initial_value];
```

- `DECLARE`: 声明变量的关键字。
- `variable_name`: 变量名称。
- `datatype`: 变量的数据类型。
- `DEFAULT initial_value`: 可选项，指定变量的初始值。

##### 局部变量赋值

```sql
SET variable_name = value;
SET variable_name := value;
SELECT COLUMN_NAME INTO variable_name FROM TABLE_NAME;
```

### if判断

```sql
IF condition THEN
    statements_if_true;
ELSE
    statements_if_false;
END IF;
```

demo

```sql
#创建存储过程
create procedure p4()
begin
    declare score int default 70;
    declare result varchar(10);
  if score>= 85 then
set result :='优秀';
elseif score>=60 then
set result := '及格';
else set result := '不及格';
end if;
    select result;
end;
#调用
call p4();
```



### 参数

声明参数

参数声明通常在存储过程的名称后面使用 `IN`、`OUT` 或 `INOUT` 关键字来标识参数类型，并指定参数的名称和数据类型。

```sql
CREATE PROCEDURE procedure_name(IN|OUT|INOUT parameter_name data_type)
```

- `IN`: 表示参数是输入参数，用于向存储过程传递值。
- `OUT`: 表示参数是输出参数，用于从存储过程返回值。
- `INOUT`: 表示参数既是输入参数也是输出参数，允许在存储过程内部修改参数的值并将修改后的值返回。

demo1

```sql
create procedure p7(IN score int,OUT result varchar(10))
begin
  if score>= 85 then
set result :='优秀';
elseif score>=60 then
set result := '及格';
else set result := '不及格';
end if;
end;
call p7(59,@result);
select @result;
```

demo2

```sql
#将传入的200分制的分数，进行换算，换算成百分制，然后返回分数
create procedure p1(inout score double)
begin
  set score:=score*0.5;
end;
set @score=198;
call p1(@score);
select @score;
```



### case

在MySQL存储过程中，可以使用 `CASE` 表达式来实现根据不同条件执行不同逻辑的功能。`CASE` 表达式允许你根据一个或多个条件选择性地返回不同的值或执行不同的代码块。

CASE 表达式的基本语法

```sql
CASE case_value
    WHEN when_value1 THEN statement1;
    WHEN when_value2 THEN statement2;
    ...
    ELSE else_statement;
END CASE;
```

- `case_value`: 要比较的表达式或变量。
- `WHEN when_value`: 当 `case_value` 等于 `when_value` 时执行相应的 `statement`。
- `ELSE`: 可选项，表示如果没有任何 `when_value` 匹配，则执行 `else_statement`。

```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    ELSE else_result
END CASE;
```

`condition1`, `condition2`, ...: 一系列条件，可以是任意的布尔表达式。

`result1`, `result2`, ...: 如果条件为真，则返回的结果。

`else_result`: 可选项，如果没有条件为真，则返回 `else_result`。



demo

```sql
#输入一个月份，判断所属季度

```



### 循环

#### while

在 MySQL 中，存储过程中的循环结构通常使用 `WHILE` 语句来实现循环执行特定的操作，直到满足退出循环的条件。`WHILE` 循环允许在存储过程内部根据条件重复执行一段代码块，直到条件不再满足为止。

##### 基本语法

```sql
WHILE condition DO
    statements;
END WHILE;
```

- `condition`: 表达式，用于判断循环是否继续执行。如果 `condition` 为真（TRUE），则继续执行 `statements`；否则退出循环。
- `statements`: 要执行的语句或语句块。

##### 示例

以下是一个简单的存储过程示例，演示如何使用 `WHILE` 循环来累加一个变量的值，直到达到特定的条件：

```sql
CREATE PROCEDURE ExampleProcedure()
BEGIN
    DECLARE total INT DEFAULT 0;
    DECLARE counter INT DEFAULT 1;

    WHILE counter <= 10 DO
        SET total = total + counter;
        SET counter = counter + 1;
    END WHILE;

    SELECT total;
END//

DELIMITER ;
```

#### repeat

#### loop

### 游标

### 条件处理程序

## 触发器

MySQL触发器（Trigger）是一种数据库对象，它与表相关联，用于在表上执行自动化操作，如插入、更新或删除记录时触发特定的SQL语句块。触发器可以在特定的操作发生前或发生后执行，这取决于它们的类型（`BEFORE`或`AFTER`）。

### 触发器分类

在MySQL中，触发器（Trigger）根据其触发事件的时间点分为两种类型：`BEFORE` 和 `AFTER`。这两种类型的触发器定义了它们执行的时机，即在数据库执行操作之前或之后触发。

#### BEFORE 触发器

BEFORE 触发器会在触发事件（INSERT、UPDATE、DELETE）执行之前执行，**它允许在实际操作发生之前修改或验证数据。**

- **BEFORE INSERT**：在执行 INSERT 操作之前触发。
- **BEFORE UPDATE**：在执行 UPDATE 操作之前触发。
- **BEFORE DELETE**：在执行 DELETE 操作之前触发。

demo：

```sql
CREATE TRIGGER before_insert_trigger
BEFORE INSERT ON your_table
FOR EACH ROW
BEGIN
    -- 可以在这里执行一些逻辑，例如设置默认值或验证约束条件
    IF NEW.column1 IS NULL THEN
        SET NEW.column1 = 'default_value';
    END IF;
END;
```

#### AFTER 触发器

AFTER 触发器会在触发事件（INSERT、UPDATE、DELETE）执行之后执行，它允许在实际操作完成后执行一些后续操作。

- **AFTER INSERT**：在执行 INSERT 操作之后触发。
- **AFTER UPDATE**：在执行 UPDATE 操作之后触发。
- **AFTER DELETE**：在执行 DELETE 操作之后触发。

demo：

```sql
CREATE TRIGGER after_delete_trigger
AFTER DELETE ON your_table
FOR EACH ROW
BEGIN
    -- 可以在这里执行一些后续操作，例如更新相关联的表或记录删除操作日志
    INSERT INTO delete_log (deleted_id, delete_time)
    VALUES (OLD.id, NOW());
END;
```

### 触发器的执行逻辑

无论是 BEFORE 还是 AFTER 触发器，它们都是针对每一行的操作，即 `FOR EACH ROW`。这意味着每次触发事件影响到表中的每一行时，触发器都会执行一次。

### 触发器语法

#### 创建

```sql
CREATE TRIGGER trigger_name
{BEFORE | AFTER} {INSERT | UPDATE | DELETE} ON table_name
FOR EACH ROW
BEGIN
    -- 触发器的SQL语句块，包括需要执行的逻辑
END;
```

#### 查看

```SQL
SHOW TRIGGERS;
```

#### 删除

```SQL
DROP TRIGGER [SCHEMA_NAME.]TRIGGER_NAME;
```

**demo:**

当创建MySQL触发器时，通常需要指定在何种情况下触发器会执行，例如在插入、更新或删除表中的数据时。以下是一个简单的MySQL触发器示例，用来演示在数据插入时如何更新另一张表的情况。

假设有两张表：`orders` 和 `order_logs`，它们的结构如下：

```sql
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_number VARCHAR(50),
    order_date DATE,
    total_amount DECIMAL(10, 2)
);

CREATE TABLE order_logs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    log_message VARCHAR(255),
    log_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

现在，我们想要创建一个触发器，每当在 `orders` 表中插入新数据时，自动将相关信息插入 `order_logs` 表中，以记录订单的日志信息。

下面是创建触发器的示例SQL语句：

```sql
DELIMITER //

CREATE TRIGGER after_order_insert
AFTER INSERT ON orders
FOR EACH ROW
BEGIN
    DECLARE log_message VARCHAR(255);
    
    -- 构造日志信息
    SET log_message = CONCAT('New order created: ', NEW.order_number, 
                             '. Total amount: ', NEW.total_amount);

    -- 插入日志记录到 order_logs 表中
    INSERT INTO order_logs (order_id, log_message)
    VALUES (NEW.id, log_message);//因为是after_insert
END//

DELIMITER ;
```

解释一下上面的触发器示例：

- `after_order_insert` 是触发器的名称，用于表示这是一个在插入操作后触发的触发器。
- `AFTER INSERT ON orders` 表示触发器在 `orders` 表有数据插入后触发。
- `FOR EACH ROW` 表示每插入一行数据都会触发一次触发器。
- `BEGIN ... END` 之间是触发器的执行逻辑。
- `NEW` 是一个关键字，用于引用插入操作中新插入的数据行的值。
- 在 `BEGIN ... END` 中，我们首先声明了一个变量 `log_message` 来存储要插入到 `order_logs` 表中的日志信息。
- 使用 `CONCAT()` 函数构造了日志信息，包含了订单号和总金额。
- 最后，使用 `INSERT INTO ... VALUES ...` 语句将构造好的日志信息插入到 `order_logs` 表中，并将 `NEW.id`（即刚插入的订单的ID）作为外键 `order_id` 插入到 `order_logs` 表中。

使用这个触发器，每当向 `orders` 表中插入新订单数据时，都会自动在 `order_logs` 表中创建相应的日志记录，记录订单的重要信息。

以下是一个**AFTER_INSERT**简单的示例，演示如何创建一个在插入数据后触发的触发器：

假设我们有一个表 `employees`，包含以下字段：

- `id`：员工ID，主键自增
- `name`：员工姓名
- `salary`：员工薪水

现在我们希望在插入新员工数据后，自动向 `audit_log` 表中插入一条记录，记录员工的姓名和插入时间。

创建示例：

1. **创建 `employees` 表：**

```sql
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    salary DECIMAL(10, 2)
);
```

1. **创建 `audit_log` 表用于记录日志：**

```sql
CREATE TABLE audit_log (
    id INT AUTO_INCREMENT PRIMARY KEY,
    employee_name VARCHAR(100),
    action_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

1. **创建 `AFTER INSERT` 触发器：**

```sql
DELIMITER //

CREATE TRIGGER after_employee_insert
AFTER INSERT ON employees
FOR EACH ROW
BEGIN
    INSERT INTO audit_log (employee_name) VALUES (NEW.name);
END //

DELIMITER ;
```

在上面的示例中：

- 我们创建了一个 `AFTER INSERT` 触发器，命名为 `after_employee_insert`。
- 这个触发器会在 `employees` 表中插入新数据后触发。
- `FOR EACH ROW` 意味着每插入一行数据，触发器都会执行一次。
- 在触发器的主体中，我们使用 `NEW.name` 来引用插入操作中的新数据的姓名，并将其插入到 `audit_log` 表中，同时记录当前时间戳（因为 `action_time` 默认为当前时间）。

测试触发器：

现在，如果向 `employees` 表中插入一条数据，例如：

```sql
INSERT INTO employees (name, salary) VALUES ('Alice', 50000.00);
```

这会触发 `after_employee_insert` 触发器，在 `audit_log` 表中插入一条记录：

```sql
SELECT * FROM audit_log;
```

输出可能类似于：

```diff
+----+---------------+---------------------+
| id | employee_name | action_time         |
+----+---------------+---------------------+
| 1  | Alice         | 2024-06-27 10:00:00 |
+----+---------------+---------------------+
```

这样，每次插入新员工数据时，都会自动记录到 `audit_log` 表中，实现了基本的日志记录功能。

**AFTER_UPDATE示例**

```sql
create table LOL_user
(
    id int auto_increment primary key ,
    user_name varchar(15),
    layer varchar(10),
    section varchar(10)
);
create table update_log
(
    id int auto_increment primary key ,
    new_name varchar(10),
    new_layer varchar(15),
    update_time timestamp default current_timestamp
);
create trigger after_log_update after update on lol_user for each row
    begin
        insert into update_log(new_name, new_layer) values(NEW.user_name,NEW.layer);
    end;
insert into  LOL_user(user_name, layer, section) values('yyw','青铜','艾欧尼亚'),('czh','白银','班德尔城'),('ztw','黄金','扭曲丛林');
select * from LOL_user;
update LOL_user set layer='最强王者'where user_name='yyw';
select* from update_log;
```

当你需要在数据库中删除数据后自动执行某些操作时，可以使用触发器来实现。下面是一个简单的示例，演示如何创建一个在删除数据后触发的触发器。

假设我们有一个表 `employees`，包含以下字段：

- `id`：员工ID，主键自增
- `name`：员工姓名
- `salary`：员工薪水

我们希望在删除员工数据时，自动将被删除的员工姓名和删除时间记录到一个日志表 `delete_log` 中。

**AFTER_DELETE示例**

1. **创建 `employees` 表：**

```sql
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    salary DECIMAL(10, 2)
);
```

1. **创建 `delete_log` 表用于记录删除日志：**

```sql
CREATE TABLE delete_log (
    id INT AUTO_INCREMENT PRIMARY KEY,
    employee_name VARCHAR(100),
    delete_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

1. **创建 `AFTER DELETE` 触发器：**

```sql
DELIMITER //

CREATE TRIGGER after_employee_delete
AFTER DELETE ON employees
FOR EACH ROW
BEGIN
    INSERT INTO delete_log (employee_name) VALUES (OLD.name);
END //

DELIMITER ;
```

在上面的示例中：

- 我们创建了一个 `AFTER DELETE` 触发器，命名为 `after_employee_delete`。
- 这个触发器会在 `employees` 表中删除数据后触发。
- `FOR EACH ROW` 意味着每次删除一行数据，触发器都会执行一次。
- 在触发器的主体中，我们使用 `OLD.name` 来引用被删除的员工的姓名，并将其插入到 `delete_log` 表中，同时自动记录当前时间戳（因为 `delete_time` 默认为当前时间）。

测试触发器：

现在，如果从 `employees` 表中删除一条数据，例如：

```sql
DELETE FROM employees WHERE id = 1;
```

这会触发 `after_employee_delete` 触发器，在 `delete_log` 表中插入一条记录：

```sql
SELECT * FROM delete_log;
```

输出可能类似于：

```diff
+----+---------------+---------------------+
| id | employee_name | delete_time         |
+----+---------------+---------------------+
| 1  | Alice         | 2024-06-27 10:00:00 |
+----+---------------+---------------------+
```

这样，每次删除员工数据时，触发器会自动记录被删除员工的姓名和删除时间到 `delete_log` 表中，实现了基本的删除操作的日志记录功能。

## 锁

### 全局锁

在MySQL中，全局锁是一种特殊的锁定机制，用于在执行一些需要全局一致性的操作时（如备份），确保整个数据库的数据完整性和一致性。全局锁会对整个数据库实例加锁，阻止其他会话对数据库进行写操作，从而保证在锁定期间不会有数据发生变化。

全局锁的使用场景通常包括：

1. **全库备份**：当需要对整个数据库进行备份时，为了确保备份数据的一致性，可以使用全局锁来防止备份期间有新数据写入。
2. **全库复制**：在设置MySQL主从复制时，有时需要确保从库的数据与主库的数据完全一致。为了避免在复制初始数据时有其他写操作影响数据一致性，可以使用全局锁来暂时停止所有写操作，然后进行数据初始化。

虽然全局锁确保了数据的一致性，但也有一些显著的缺点和限制：

- **阻塞写操作**：全局锁会阻塞整个数据库的写操作，因此会在锁定期间导致数据库的写入性能下降。

- **锁定时间长**：如果需要锁定时间过长，会对系统的可用性产生影响。

- **不适合高并发**：在高并发环境中，全局锁可能会造成大量的等待和排队，从而降低系统的响应能力。

  

  **使用 `FLUSH TABLES WITH READ LOCK`**

  在MySQL中，可以通过以下步骤获取全局锁：

  ```sql
  -- 步骤1: 获取全局读锁
  FLUSH TABLES WITH READ LOCK;
  
  -- 步骤2: 执行备份操作
  -- 在这个步骤中，可以使用mysqldump命令或其他备份工具来备份数据库。
  
  -- 步骤3: 释放锁
  UNLOCK TABLES;
  ```

  **说明**：

  - `FLUSH TABLES WITH READ LOCK;`：这个语句会锁定所有的表，使得其他会话无法对这些表进行写操作。这样可以确保在备份过程中，数据库的数据不会发生变化。

  - 在获取全局锁后，可以使用 

    ```
    mysqldump
    ```

     命令来备份数据库。例如：

    ```bash
    mysqldump -u username -p dbname > backup.sql
    ```

  - `UNLOCK TABLES;`：释放全局锁，允许其他会话继续对表进行写操作。

  ### MYSQLDUMP

  `**mysqldump**`是MySQL提供的一个命令行工具，用于备份MySQL数据库中的数据和结构。它可以将整个数据库或者特定的数据库、表、甚至查询结果导出为SQL文件，方便后续的数据恢复或迁移操作。

  主要功能和用途：

  1. **数据库备份**：
     - 使用 `mysqldump` 可以备份整个MySQL数据库，包括数据库结构（表、视图、存储过程、触发器等）和数据。
     - 可以选择备份特定的数据库或表，而不是整个服务器。
  2. **数据导出**：
     - 可以将MySQL中的数据导出为标准的SQL语句文件，这些文件可以通过 `mysql` 命令或其他MySQL客户端来导入到其他MySQL服务器中，用于数据迁移或复制。
  3. **灵活的选项**：
     - 提供了多种选项来控制备份的行为，例如指定备份的目标数据库、排除某些表、指定备份的数据类型（仅结构、仅数据、或同时备份结构和数据）、以及输出文件的格式等。
  4. **定时备份和自动化**：
     - 可以与操作系统的定时任务（如cron）结合使用，定期执行备份操作，确保数据库的安全性和可恢复性。

  使用示例：

  #### 1. 备份整个数据库到文件：

  ```BASH
  mysqldump -u username -p dbname > backup.sql
  ```

  这条命令会将 `dbname` 数据库的结构和数据导出到 `backup.sql` 文件中。

  #### 2. 备份特定表：

  ```BASH
  mysqldump -u username -p dbname tablename > table_backup.sql
  ```

  这条命令会将 `dbname` 数据库中的 `tablename` 表的结构和数据导出到 `table_backup.sql` 文件中。

  #### 3. 备份数据库结构不包含数据：

  ```bash
  mysqldump -u username -p --no-data dbname > structure_backup.sql
  ```

  这条命令会导出 `dbname` 数据库的结构（表结构、存储过程、触发器等），但不包含数据，保存在 `structure_backup.sql` 文件中。

### 表级锁

表级锁是数据库管理系统（如MySQL）中一种粒度较大的锁定机制，**针对非索引字段加的锁**，它可以用来限制对表的并发访问，以确保数据的一致性和完整性。在MySQL中，主要有两种类型的表级锁：读锁（共享锁）和写锁（排它锁）。

#### 表锁

**** **共享锁（Read Lock，也称为读锁）******

- **作用：** 共享锁允许多个会话同时读取同一张表的数据，但阻止其他会话对该表进行写操作。

- **获取方式：** 当一个会话获取了共享锁后，其他会话可以继续获取共享锁，但无法获取排它锁（写锁）。

- 语法示例：

   在MySQL中，可以使用以下语句获取共享锁：

  ```sql
  LOCK TABLES table_name READ;
  ```

- 释放锁定：

   释放共享锁的语句为：

  ```sql
  UNLOCK TABLES;
  ```

**排他锁（Write Lock，也称为写锁）**

- **作用：** 排他锁阻止其他会话同时对表进行读取或写入操作，保证在获取锁的会话修改表数据时，其他会话无法读取或修改该表的数据,**另一个会阻塞，等待表解锁**。

- **获取方式：** 只有一个会话可以获取排他锁。获取排他锁会阻止其他会话获取任何类型的锁（包括共享锁）。

- 语法示例：

   在MySQL中，可以使用以下语句获取排他锁：

  ```sql
  LOCK TABLES table_name WRITE;
  ```

- 释放锁定：

   释放排他锁的语句同样是：

  ```sql
  UNLOCK TABLES;
  ```

- **锁定粒度：** 表级锁定影响整个表，因此在使用时需要注意锁定的粒度，以避免对整个数据库或大量数据行造成性能影响。
- **并发控制：** 合理使用锁机制可以有效控制并发，避免数据竞争和不一致的情况发生，但过度使用或错误使用锁机制可能会导致性能下降或死锁问题。
- **事务支持：** 表级锁通常在非事务型存储引擎（如MyISAM）中使用较多，对于事务型存储引擎（如InnoDB），推荐使用行级锁来提升并发性和事务隔离级别。

#### 元数据锁(MDL)

**申请 MDL 锁的操作会形成一个队列，队列中写锁获取优先级高于读锁，一旦出现 MDL 写锁等待，会阻塞后续该表的所有 CRUD 操作。**

某一个表设计一个未提交的事务时，不能修改该表的表结构

MDL 读锁（Metadata Lock Read Lock）：

- **作用：** MDL 读锁允许其他会话继续读取元数据对象，但不允许其他会话获取写锁，从而防止对象结构发生变化。
- **使用场景：** 当一个会话需要读取对象的元数据信息时（例如表结构、表的状态），会自动获取 MDL 读锁。这种锁不会阻止其他会话对对象的读取操作，但会阻止对对象结构进行更改的操作。
- **例子：** 在一个会话中使用 `SHOW CREATE TABLE` 命令查看表的创建语句时，会获取表的 MDL 读锁，其他会话仍然可以查询表的数据，但无法对表进行结构上的变更操作。

MDL 写锁（Metadata Lock Write Lock）：

- **作用：** MDL 写锁阻止其他会话对对象进行任何读取或写入操作，直到写锁被释放。
- **使用场景：** 当一个会话需要修改对象的结构（例如创建、修改或删除表、重命名表等）时，会自动获取 MDL 写锁。这种锁会阻塞其他会话对同一对象的所有操作，包括读取和写入。
- **例子：** 当一个会话执行 `ALTER TABLE` 命令修改表结构时，会获取表的 MDL 写锁，其他会话无法同时读取或修改表的结构，直到修改操作完成并释放锁。

#### **意向锁**

意向锁是表级锁，共有两种：

- **意向共享锁（Intention Shared Lock，IS 锁）**：事务有意向对表中的某些记录加共享锁（S 锁），加共享锁前必须先取得该表的 IS 锁。
- **意向排他锁（Intention Exclusive Lock，IX 锁）**：事务有意向对表中的某些记录加排他锁（X 锁），加排他锁之前必须先取得该表的 IX 锁。

**意向锁是由数据引擎自己维护的，用户无法手动操作意向锁，在为数据行加共享/排他锁之前，InooDB 会先获取该数据行所在在数据表的对应意向锁。**

意向锁之间是互相兼容的。

|       | IS 锁 | IX 锁 |
| ----- | ----- | ----- |
| IS 锁 | 兼容  | 兼容  |
| IX 锁 | 兼容  | 兼容  |

意向锁和共享锁和排它锁互斥（这里指的是表级别的共享锁和排他锁，意向锁不会与行级的共享锁和排他锁互斥）。

|      | IS 锁 | IX 锁 |
| ---- | ----- | ----- |
| S 锁 | 兼容  | 互斥  |
| X 锁 | 互斥  | 互斥  |

而普通的 select 是不会加行级锁的，普通的 select 语句是利用 MVCC 实现一致性读，是无锁的。

不过，select 也是可以对记录加共享锁和独占锁的，具体方式如下：

```sql
//先在表上加上意向共享锁，然后对读取的记录加共享锁
select ... lock in share mode;

//先表上加上意向独占锁，然后对读取的记录加独占锁
select ... for update;
```

**意向共享锁和意向独占锁是表级锁，不会和行级的共享锁和独占锁发生冲突，而且意向锁之间也不会发生冲突，只会和共享表锁（\*lock tables ... read\*）和独占表锁（\*lock tables ... write\*）发生冲突。**

表锁和行锁是满足读读共享、读写互斥、写写互斥的。

如果没有「意向锁」，那么加「独占表锁」时，就需要遍历表里所有记录，查看是否有记录存在独占锁，这样效率会很慢。

那么有了「意向锁」，由于在对记录加独占锁前，先会加上表级别的意向独占锁，那么在加「独占表锁」时，直接查该表是否有意向独占锁，如果有就意味着表里已经有记录被加了独占锁，这样就不用去遍历表里的记录。

所以，**意向锁的目的是为了快速判断表里是否有记录被加锁**

**例子**：

**无意向锁的情况：**

- 事务A启动，需要插入新的行，尝试获取表级锁。
- 事务B启动，需要更新已有行，尝试获取表级锁。
- MySQL需要检查表上所有行的锁状态，以确定是否有冲突。这可能涉及到遍历表的每一行，检查每一行的锁状态，成本较高。

**有意向锁的情况：**

- 事务A启动，获取意向锁表明要对表进行写操作。
- 事务B启动，获取意向锁表明也要对表进行写操作。
- MySQL首先检查意向锁的状态，它可以快速得知表上是否有其他事务在使用行级锁或者其他写操作的锁。如果没有冲突，两个事务可以同时进行操作；如果有冲突，则需要等待。

#### AUTO-INC 锁

表里的主键通常都会设置成自增的，这是通过对主键字段声明 `AUTO_INCREMENT` 属性实现的。

之后可以在插入数据时，可以不指定主键的值，数据库会自动给主键赋值递增的值，这主要是通过 **AUTO-INC 锁**实现的。

AUTO-INC 锁是特殊的表锁机制，锁**不是再一个事务提交后才释放，而是再执行完插入语句后就会立即释放**。

**在插入数据时，会加一个表级别的 AUTO-INC 锁**，然后为被 `AUTO_INCREMENT` 修饰的字段赋值递增的值，等插入语句执行完成后，才会把 AUTO-INC 锁释放掉。

那么，一个事务在持有 AUTO-INC 锁的过程中，其他事务的如果要向该表插入语句都会被阻塞，从而保证插入数据时，被 `AUTO_INCREMENT` 修饰的字段的值是连续递增的。

但是， AUTO-INC 锁再对大量数据进行插入的时候，会影响插入性能，因为另一个事务中的插入会被阻塞。

因此， 在 MySQL 5.1.22 版本开始，InnoDB 存储引擎提供了一种**轻量级的锁**来实现自增。

一样也是在插入数据的时候，会为被 `AUTO_INCREMENT` 修饰的字段加上轻量级锁，**然后给该字段赋值一个自增的值，就把这个轻量级锁释放了，而不需要等待整个插入语句执行完后才释放锁**。

InnoDB 存储引擎提供了个 innodb_autoinc_lock_mode 的系统变量，是用来控制选择用 AUTO-INC 锁，还是轻量级的锁。

- 当 innodb_autoinc_lock_mode = 0，就采用 AUTO-INC 锁，语句执行结束后才释放锁；
- 当 innodb_autoinc_lock_mode = 2，就采用轻量级锁，申请自增主键后就释放锁，并不需要等语句执行后才释放。
- 当 innodb_autoinc_lock_mode = 1：
  - 普通 insert 语句，自增锁在申请之后就马上释放；
  - 类似 insert … select 这样的批量插入数据的语句，自增锁还是要等语句结束后才被释放；

当 innodb_autoinc_lock_mode = 2 是性能最高的方式，但是当搭配 binlog 的日志格式是 statement 一起使用的时候，在「主从复制的场景」中会发生**数据不一致的问题**。

举个例子，考虑下面场景：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/mysql/%E9%94%81/innodb_autoinc_lock_mode=2.png)

session A 往表 t 中插入了 4 行数据，然后创建了一个相同结构的表 t2，然后**两个 session 同时执行向表 t2 中插入数据**。

如果 innodb_autoinc_lock_mode = 2，意味着「申请自增主键后就释放锁，不必等插入语句执行完」。那么就可能出现这样的情况：

- session B 先插入了两个记录，(1,1,1)、(2,2,2)；
- 然后，session A 来申请自增 id 得到 id=3，插入了（3,5,5)；
- 之后，session B 继续执行，插入两条记录 (4,3,3)、 (5,4,4)。

可以看到，**session B 的 insert 语句，生成的 id 不连续**。

当「主库」发生了这种情况，binlog 面对 t2 表的更新只会记录这两个 session 的 insert 语句，如果 binlog_format=statement，记录的语句就是原始语句。记录的顺序要么先记 session A 的 insert 语句，要么先记 session B 的 insert 语句。

但不论是哪一种，这个 binlog 拿去「从库」执行，这时从库是按「顺序」执行语句的，只有当执行完一条 SQL 语句后，才会执行下一条 SQL。因此，在**从库上「不会」发生像主库那样两个 session 「同时」执行向表 t2 中插入数据的场景。所以，在备库上执行了 session B 的 insert 语句，生成的结果里面，id 都是连续的。这时，主从库就发生了数据不一致**。

要解决这问题，binlog 日志格式要设置为 row，这样在 binlog 里面记录的是主库分配的自增值，到备库执行的时候，主库的自增值是什么，从库的自增值就是什么。

所以，**当 innodb_autoinc_lock_mode = 2 时，并且 binlog_format = row，既能提升并发性，又不会出现数据一致性问题**。

### 行级锁

#### 行锁

innoDB的数据是根据索引组织的，**行锁是通过对索引上的索引项加锁来实现的而不是对记录进行加锁**，当我们执行 `UPDATE`、`DELETE` 语句时，如果 `WHERE`条件中字段没有命中唯一索引或者索引失效的话，就会导致扫描全表对表中的所有行记录进行加锁。这个在我们日常工作开发中经常会遇到，一定要多多注意！！！

在MySQL中，行锁是一种用于管理并发访问的锁定机制，允许事务在修改或读取数据行时进行独占性控制，从而确保数据的一致性和完整性。MySQL支持两种主要类型的行锁：共享锁（Shared Lock，也称为读锁）和排他锁（Exclusive Lock，也称为写锁）。

##### 共享锁

- **作用：** 允许多个事务同时读取同一行数据，但阻止其他事务获取排他锁，从而防止并发写入。

- **语法：** 在查询语句后面加上 `LOCK IN SHARE MODE` 或者 `FOR SHARE` 可以获取共享锁。

- 示例：

  ```sql
  -- 开启事务
  START TRANSACTION;
  
  -- 查询并获取共享锁
  SELECT * FROM my_table WHERE id = 1 LOCK IN SHARE MODE;
  
  -- 在事务中可以读取数据，但不能修改数据
  
  -- 提交事务
  COMMIT;
  ```

##### 排他锁

- **作用：** 只允许持有排他锁的事务修改数据行，并阻止其他事务同时获取共享锁或排他锁。

- **语法：** 在查询语句后面加上 `FOR UPDATE` 可以获取排他锁。

- 示例：

  ```sql
  -- 开启事务
  START TRANSACTION;
  
  -- 查询并获取排他锁
  SELECT * FROM my_table WHERE id = 1 FOR UPDATE;
  
  -- 在事务中可以修改数据
  
  -- 提交事务
  COMMIT;
  ```

InnoDB 引擎是支持行级锁的，而 MyISAM 引擎并不支持行级锁。

前面也提到，普通的 select 语句是不会对记录加锁的，因为它属于快照读。如果要在查询时对记录加行锁，可以使用下面这两个方式，这种查询会加锁的语句称为**锁定读**。

```sql
//对读取的记录加共享锁
select ... lock in share mode;

//对读取的记录加独占锁
select ... for update;
```

上面这两条语句必须在一个事务中，**因为当事务提交了，锁就会被释放**，所以在使用这两条语句的时候，要加上 begin、start transaction 或者 set autocommit = 0。

共享锁（S锁）满足读读共享，读写互斥。独占锁（X锁）满足写写互斥、读写互斥。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/mysql/%E9%94%81/x%E9%94%81%E5%92%8Cs%E9%94%81.png)

行级锁的类型主要有三类：

- Record Lock，记录锁，也就是仅仅把一条记录锁上；
- Gap Lock，间隙锁，锁定一个范围，但是不包含记录本身；
- Next-Key Lock：Record Lock + Gap Lock 的组合，锁定一个范围，并且锁定记录本身。

### [#](https://www.xiaolincoding.com/mysql/lock/mysql_lock.html#record-lock)Record Lock

Record Lock 称为记录锁，锁住的是一条记录。而且记录锁是有 S 锁和 X 锁之分的：

- 当一个事务对一条记录加了 S 型记录锁后，其他事务也可以继续对该记录加 S 型记录锁（S 型与 S 锁兼容），但是不可以对该记录加 X 型记录锁（S 型与 X 锁不兼容）;
- 当一个事务对一条记录加了 X 型记录锁后，其他事务既不可以对该记录加 S 型记录锁（S 型与 X 锁不兼容），也不可以对该记录加 X 型记录锁（X 型与 X 锁不兼容）。

举个例子，当一个事务执行了下面这条语句：

```sql
mysql > begin;
mysql > select * from t_test where id = 1 for update;
```

就是对 t_test 表中主键 id 为 1 的这条记录加上 X 型的记录锁，这样其他事务就无法对这条记录进行修改了。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/mysql/%E9%94%81/%E8%AE%B0%E5%BD%95%E9%94%81.drawio.png)

当事务执行 commit 后，事务过程中生成的锁都会被释放。

### [#](https://www.xiaolincoding.com/mysql/lock/mysql_lock.html#gap-lock)Gap Lock

Gap Lock 称为间隙锁，只存在于可重复读隔离级别，目的是为了解决可重复读隔离级别下幻读的现象。

假设，表中有一个范围 id 为（3，5）间隙锁，那么其他事务就无法插入 id = 4 这条记录了，这样就有效的防止幻读现象的发生。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/mysql/%E9%94%81/gap%E9%94%81.drawio.png)

间隙锁虽然存在 X 型间隙锁和 S 型间隙锁，但是并没有什么区别，**间隙锁之间是兼容的，即两个事务可以同时持有包含共同间隙范围的间隙锁，并不存在互斥关系，因为间隙锁的目的是防止插入幻影记录而提出的**。

#### 间隙锁的基本语法

间隙锁可以在事务中使用，主要是通过一些读取操作来显式地锁定索引的范围，以防止其他事务在该范围内插入数据。下面是基本的语法形式：

```sql
SELECT * FROM table_name WHERE indexed_column BETWEEN value1 AND value2 FOR SHARE;
```

或者

```sql
SELECT * FROM table_name WHERE indexed_column BETWEEN value1 AND value2 FOR UPDATE;
```

- `table_name`：表名。
- `indexed_column`：被索引的列名。
- `value1` 和 `value2`：索引列的范围值。
- `FOR SHARE` 或 `FOR UPDATE`：指定事务的锁定类型，`FOR SHARE` 表示共享锁，`FOR UPDATE` 表示排他锁。

### [#](https://www.xiaolincoding.com/mysql/lock/mysql_lock.html#next-key-lock)Next-Key Lock

Next-Key Lock 称为临键锁，是 Record Lock + Gap Lock 的组合，锁定一个范围，并且锁定记录本身。

假设，表中有一个范围 id 为（3，5] 的 next-key lock，那么其他事务即不能插入 id = 4 记录，也不能修改 id = 5 这条记录。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/mysql/%E9%94%81/%E4%B8%B4%E9%94%AE%E9%94%81.drawio.png)

所以，next-key lock 即能保护该记录，又能阻止其他事务将新纪录插入到被保护记录前面的间隙中。

**next-key lock 是包含间隙锁+记录锁的，如果一个事务获取了 X 型的 next-key lock，那么另外一个事务在获取相同范围的 X 型的 next-key lock 时，是会被阻塞的**。

比如，一个事务持有了范围为 (1, 10] 的 X 型的 next-key lock，那么另外一个事务在获取相同范围的 X 型的 next-key lock 时，就会被阻塞。

虽然相同范围的间隙锁是多个事务相互兼容的，但对于记录锁，我们是要考虑 X 型与 S 型关系，X 型的记录锁与 X 型的记录锁是冲突的。

#### 临键隙锁的基本语法

临键隙锁主要在使用范围条件或者唯一索引条件进行查询时生效。它可以通过 `SELECT ... FOR SHARE` 或 `SELECT ... FOR UPDATE` 来显式地使用。

**语法示例 1：使用范围条件**

```sql
START TRANSACTION;

SELECT * FROM table_name WHERE indexed_column > value FOR SHARE;

-- 在此期间，其他事务无法在索引列大于 value 的范围内插入新数据

COMMIT;
```

**语法示例 2：使用唯一索引条件**

```sql
START TRANSACTION;

SELECT * FROM table_name WHERE indexed_column = value FOR UPDATE;

-- 在此期间，其他事务无法在索引列等于 value 的具体记录上插入新数据

COMMIT;
```

- `table_name`：表名。
- `indexed_column`：被索引的列名。
- `value`：索引列的比较值。
- `FOR SHARE` 或 `FOR UPDATE`：指定事务的锁定类型，`FOR SHARE` 表示共享锁，`FOR UPDATE` 表示排他锁。

### [#](https://www.xiaolincoding.com/mysql/lock/mysql_lock.html#插入意向锁)插入意向锁

一个事务在插入一条记录的时候，需要判断插入位置是否已被其他事务加了间隙锁（next-key lock 也包含间隙锁）。

如果有的话，插入操作就会发生**阻塞**，直到拥有间隙锁的那个事务提交为止（释放间隙锁的时刻），在此期间会生成一个**插入意向锁**，表明有事务想在某个区间插入新记录，但是现在处于等待状态。

举个例子，假设事务 A 已经对表加了一个范围 id 为（3，5）间隙锁。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/mysql/%E9%94%81/gap%E9%94%81.drawio.png)

当事务 A 还没提交的时候，事务 B 向该表插入一条 id = 4 的新记录，这时会判断到插入的位置已经被事务 A 加了间隙锁，于是事物 B 会生成一个插入意向锁，然后将锁的状态设置为等待状态（*PS：MySQL 加锁时，是先生成锁结构，然后设置锁的状态，如果锁状态是等待状态，并不是意味着事务成功获取到了锁，只有当锁状态为正常状态时，才代表事务成功获取到了锁*），此时事务 B 就会发生阻塞，直到事务 A 提交了事务。

插入意向锁名字虽然有意向锁，但是它并**不是意向锁，它是一种特殊的间隙锁，属于行级别锁**。

如果说间隙锁锁住的是一个区间，那么「插入意向锁」锁住的就是一个点。因而从这个角度来说，插入意向锁确实是一种特殊的间隙锁。

插入意向锁与间隙锁的另一个非常重要的差别是：尽管「插入意向锁」也属于间隙锁，但两个事务却不能在同一时间内，一个拥有间隙锁，另一个拥有该间隙区间内的插入意向锁（当然，插入意向锁如果不在间隙锁区间内则是可以的）。

## innoDB引擎

### 逻辑存储结构

tablespace->segment->extent->page->row

表空间（ibd文件）一个mysql实例可以对应多个表空间，用于存储记录，索引等数据

段，分为数据段（leaf node segment），索引段（non-leaf node segment），回滚段（rollback segment），innoDB是索引组织表，数据段就是B+树的叶子节点，索引段即为B+树的非叶子节点，段用来管理多个extent

区，表空间的单元结构，每个区的大小为1M。默认情况下，innoDB存储引擎页大小为16K，一个区中一共有64个连续的页

页，是innoDB存储引擎磁盘管理的最小单位，每个页的大小默认为16KB.为了保证页的连续性innDB存储引擎每次从磁盘申请4-5个区

### 架构

#### 内存架构

InnoDB 是 MySQL 中常用的存储引擎之一，它具有自己独特的内存架构来管理数据和索引在内存中的存储和访问。以下是 InnoDB 内存架构的主要组成部分和工作原理：

##### 缓冲池（Buffer Pool）

- **作用：** 缓冲池是 InnoDB 存储引擎的核心组件，用于管理数据页的内存缓存。数据页是 InnoDB 存储数据和索引的基本单位。
- **特点：** 缓冲池可以包含数据库表和索引的数据页的拷贝，以提供快速的数据访问。数据页在需要时从磁盘读入缓冲池，在查询时先尝试从缓冲池获取数据，避免频繁的磁盘读写操作，提高查询效率。
- **大小调整：** 缓冲池的大小可以通过配置参数 `innodb_buffer_pool_size` 来调整，建议设置为适合系统内存大小的值，以确保大多数数据可以保持在内存中，减少磁盘 I/O。

缓冲此以Page页为单位，底层采用链表数据结构管理Page，根据状态，将Page分为三种类型;

free page:空闲page，未被使用

clean page：被使用page，数据没有被修改过

dirty page：脏页，被使用page，数据被修改过，数据与磁盘的数据不一致

##### 日志缓冲（Log Buffer）

- **作用：** 日志缓冲用于暂存事务提交前的日志条目，加速事务的提交速度。
- **工作原理：** 当事务执行时，相关的修改操作被记录在日志缓冲中。一旦事务提交，这些日志条目会被刷新到磁盘的重做日志文件（redo log files）中，确保事务的持久性。
- **大小调整：** 日志缓冲的大小由配置参数 `innodb_log_buffer_size` 决定，通常设置为较小的值，因为它主要用于临时存储事务的日志条目。

**更改缓冲区（change buffer）**

在执行DML语句时，如果这些数据page不在buffer pool中，不会直接操作磁盘，而会将数据变更存在更改缓冲区change buffer中，在未来数据被读取时，再将数据合并并恢复到buffer pool中吗，再将合并后的数据刷新到磁盘中

##### 自适应哈希索引和重做日志缓冲

- **自适应哈希索引（Adaptive Hash Index）：** InnoDB 存储引擎会根据数据访问模式自动管理内存中的哈希索引结构，提高热数据的快速访问能力。
- **重做日志缓冲（Redo Log Buffer）：** 用于存储在事务提交之前的重做日志条目。如果重做日志缓冲不足以容纳事务的所有修改操作，这些日志条目将直接写入磁盘上的重做日志文件。

##### 其他内存结构

- **锁信息和事务信息：** InnoDB 存储引擎会维护一些额外的内存结构来管理事务的状态和锁信息，以支持事务的并发控制和隔离级别。

### 事务原理

事务的原子性，一致性，持久性靠redo log 和undo log 实现

隔离性用锁和mvcc实现



笼统介绍：

1. **Read View（读视图）**：
   - 在 MySQL 中，Read View 是用来实现多版本并发控制（MVCC）的重要概念之一。每个事务在开始时会创建一个 Read View，这个视图确定了事务开始时数据库的一个一致性快照（也可以理解为一个时间点的数据视图）。Read View 会记录当前事务开始时的活跃事务 ID 列表，用来判断事务是否可以看到其他并发事务已提交的更改，或者需要回滚到之前的版本。
2. **Undo 日志**：
   - Undo 日志用来记录事务执行过程中对数据的修改操作。当事务需要回滚时，MySQL 会利用 Undo 日志中的信息来撤销事务所做的更改，从而回滚到事务开始时的状态。
3. **快照读**：
   - 在 MySQL 的 InnoDB 存储引擎中，快照读是指事务在执行期间能够看到一致性的数据库快照。这个一致性快照是通过当前事务的 Read View 来确定的，确保事务可以看到在事务开始时已经提交的数据版本，而不会受到正在执行的其他事务未提交的更改的影响。

综上所述：

- **Read View** 是用来确定事务开始时的数据库快照，以实现快照读和多版本并发控制。
- **Undo 日志** 是用来撤销事务对数据所做的更改，以实现事务的原子性和一致性。
- **快照读** 是事务在执行过程中基于 Read View 所看到的数据库状态。

### MVCC

多版本并发控制（Multi-Version Concurrency Control, MVCC）是 MySQL 的 InnoDB 存储引擎实现隔离级别的一种具体方式，用于**实现提交读和可重复读**这两种隔离级别。而未提交读隔离级别总是读取最新的数据行，要求很低，无需使用 MVCC。可串行化隔离级别需要对所有读取的行都加锁，单纯使用 MVCC 无法实现

#### 当前读

**当前读操作：** 在事务中，当前读操作包括普通的SELECT查询和涉及UPDATE、DELETE等操作的SELECT查询。在不同的隔离级别下，当前读的行为可能会有所不同：

- **读已提交（Read Committed）：** 在这个隔离级别下，SELECT操作会读取其他事务已经提交的数据版本。这意味着在同一事务内，多次读取相同数据可能会看到不同的结果（不可重复读）。
- **可重复读（Repeatable Read）：** 在这个隔离级别下，事务的当前读操作会看到一个一致性视图，即使其他事务提交了数据变更，当前事务也只能看到事务开始时的数据版本。这可以避免不可重复读，但可能会导致幻读问题。
- **串行化（Serializable）：** 在这个隔离级别下，MySQL会对事务进行严格的串行化处理，确保每个事务的执行顺序和结果与串行执行时相同。这提供了最高的数据一致性，但可能会对性能造成较大影响。

#### 快照读

MVCC（Multi-Version Concurrency Control，多版本并发控制）中的快照读（Snapshot Read）是指事务在执行过程中，能够看到一个一致性的数据库快照或者版本，而不会受到其他并发事务对数据的影响。这种快照读的实现方式在不同的数据库系统中可能有所不同，以下是在MySQL中的相关描述：

##### 快照读在MySQL中的实现

1. **一致性视图（Consistent Read View）：** 当一个事务开始时，MySQL会为该事务创建一个一致性视图。这个视图包含了事务开始时数据库中所有数据的一个快照。在这个视图中，事务可以看到所有在事务开始前已经提交的数据版本。
2. **读取一致性视图：** 在事务执行过程中，所有的SELECT操作都是基于该一致性视图来执行的。这意味着无论其他事务是否对数据进行了修改或者删除，当前事务都只能看到在事务开始时存在的数据版本。
3. **快照的维护：** MySQL使用MVCC机制来维护这些快照。对于每个数据行，MySQL会保存多个版本，每个版本都有自己的时间戳。事务在执行时，根据其开始时间确定使用哪个版本的数据，以确保事务执行期间读取的数据是一致的。

##### 快照读的优点和应用场景

- **避免幻读问题：** 在可重复读（Repeatable Read）隔离级别下，快照读可以避免事务在执行期间看到其他事务插入的新数据（即幻读问题）。
- **增强并发性能：** 快照读允许多个事务并发执行，因为它们可以在不互相阻塞的情况下读取数据库。
- **提高数据一致性：** 快照读确保事务在执行期间看到的数据版本是一致的，这有助于维护数据库的数据完整性和一致性。

### MVCC-实现原理

#### 隐藏字段

InnoDB存储引擎中的记录确实包含一些隐藏字段，这些字段对于InnoDB引擎的内部工作至关重要。以下是一些常见的隐藏字段：

1. **事务ID（Transaction ID）**：
   - 每个InnoDB记录都包含一个事务ID，用于标识该记录是哪个事务所插入或修改的。这对于实现MVCC（多版本并发控制）非常重要。
2. **回滚指针（Rollback Pointer）**：
   - 每个活跃的事务都有一个回滚段（rollback segment），用于存储它们的旧数据。回滚指针存储了事务修改前的数据的位置，以便在事务回滚时可以还原数据。
3. **隐藏系统列**：
   - 隐藏主键，如果表结构没有指定主键，将生成该隐藏字段

这些隐藏字段是InnoDB引擎内部实现MVCC、事务管理和数据恢复的关键部分。虽然它们对于普通用户可能是不可见的，但在数据库引擎设计和性能调优中具有重要作用。

Undo日志是数据库管理系统中用于实现事务的关键组成部分，特别是在支持ACID（原子性、一致性、隔离性、持久性）事务特性的存储引擎中，如InnoDB。

#### undo log

功能和作用

1. **事务的原子性和回滚支持**：
   - Undo日志记录了事务执行过程中所做的修改操作。如果事务需要回滚，数据库系统可以利用Undo日志中的信息将数据恢复到事务开始之前的状态，从而确保事务的原子性和回滚能力。当insert的时候，产生的undo log只在回滚时需要，在事务提交后，可被立即删除。在update，delete的时候，产生的undo log 日志不仅在回滚时需要，在快照读时也需要，不会立即删除
2. **MVCC（多版本并发控制）的实现**：
   - Undo日志使得数据库能够实现MVCC，即在并发访问时读取到一致的数据版本。通过Undo日志，数据库可以在读取数据时查看事务的历史版本，而不会受到其他并发事务的影响。
3. **事务的持久性**：
   - Undo日志的另一个重要作用是确保事务的持久性。在提交事务时，数据库会将Undo日志中的操作应用到数据库中的数据页，从而将事务的修改永久保存到磁盘中。

结构和实现

- **Undo段（Undo Segment）**：
  - 数据库为每个活跃的事务分配一个Undo段，用于存储该事务所做的修改的旧数据。Undo段是一个逻辑概念，通常对应于物理存储上的Undo日志文件。
- **Undo日志文件**：
  - Undo日志以文件的形式存储在数据库中，用于记录每个事务的Undo操作。这些日志文件通常是循环使用的，即当Undo日志的空间用完时，数据库系统会重用之前的日志空间。
- **Undo日志记录格式**：
  - 每个Undo日志记录包含了事务所做的修改的详细信息，包括修改前的数据和修改后的数据。这些信息足以让数据库在需要时反向应用修改，以恢复到事务开始之前的状态。

性能影响和管理

- **性能影响**：
  - Undo日志的管理和维护对数据库系统的性能有一定的影响。因此，数据库管理员通常需要根据系统负载和需求来合理配置Undo日志的大小和管理策略。
- **管理**：
  - 数据库管理员可以监控和管理Undo日志的大小、使用率和性能影响，以确保系统能够有效地支持事务处理需求。

##### 版本链的基本概念

在InnoDB中，每当一个事务对数据进行修改时，不会直接在原始数据上进行更新，而是创建一个新的数据版本，并将这个新版本写入数据页。同时，旧版本的数据不会被覆盖，而是通过Undo日志维护一个版本链，这样可以实现不同事务之间的隔离。

关键点：

1. **版本链的结构**：
   - 每个修改操作（INSERT、UPDATE、DELETE）会生成一个新的数据版本，并且每个版本都与一个事务ID相关联。
   - 版本链由多个数据版本组成，每个版本都包含一个指向上一个版本的引用，形成一个链表结构。
2. **Undo日志的作用**：
   - Undo日志记录了每个事务对数据的修改，包括原始数据的旧值和新值。这些记录在事务提交或回滚时用于恢复数据。
3. **多版本并发控制（MVCC）的实现**：
   - 当一个事务需要读取数据时，数据库会根据事务的隔离级别以及事务开始时的时间点（或快照）决定读取哪个版本的数据：
     - **读已提交（Read Committed）**：读取当前活跃事务提交的最新版本。
     - **可重复读（Repeatable Read）**：在事务开始时确定一个快照，只读取这个快照创建时已提交的数据版本。
     - **串行化（Serializable）**：确保每个事务在读取数据时都能看到一个一致的快照版本。
4. **版本链的维护**：
   - 当事务提交时，其生成的新版本将变为当前版本，并在Undo日志中保留旧版本的信息，用于其他事务可能需要的读操作。
   - 当事务回滚时，系统将使用Undo日志中的信息来撤销该事务的修改，恢复数据到事务开始时的状态。



#### READview

## Mysql系统数据库

MySQL 中有一些系统数据库，这些数据库用于管理和存储关于MySQL服务器自身运行状态、权限、性能等方面的信息。以下是一些常见的MySQL系统数据库：

1. **information_schema**：提供关于数据库服务器的元数据（metadata）信息，比如数据库、表、列的信息、权限、字符集信息等。这是一个虚拟的数据库，不存储实际的数据。
2. **performance_schema**：用于收集MySQL服务器性能相关的统计信息，包括锁等待、I/O、SQL语句性能等。这些信息可以帮助进行性能调优和监控。
3. **mysql**：存储了MySQL服务器的权限信息、用户账号、密码等安全相关信息。包括用户授权、密码哈希值等。
4. **sys**：在MySQL 5.7及更新的版本中引入，提供了一些用于性能监控和分析的视图和函数，帮助用户更方便地分析和优化性能。

## 常用工具

### mysql

不是指mysql服务，而是指mysql的客户端工具

```
语法：
MYSQL [options] [database]
选项：
-U 指定用户名
-p 指定密码
-h 指定服务器ip或域名
-P（大写）指定连接端口
-e 执行sql语句并退出  该选项可以在mysql客户端执行sql语句，而不用连接到mysql数据库再执行，方便于一

```

```
查询itcast数据库下面stu表的内容
mysql-h192.168.200.202 -P3306 -uroot -p1234 itcast -e"select*from stu"
```

### mysqladmin

`mysqladmin` 是 MySQL 中的一个命令行实用程序，允许管理员执行各种管理任务，如管理数据库、检查服务器状态和执行维护操作。通常在终端或命令提示符环境中使用，与 MySQL 服务器进行交互。

一些可以使用 `mysqladmin` 完成的常见任务包括：

1. **服务器管理**：启动、停止或重启 MySQL 服务器。

   ```bash
   mysqladmin -u root -p shutdown
   mysqladmin -u root -p start
   ```

2. **用户管理**：创建或删除 MySQL 用户。

   ```bash
   mysqladmin -u root -p password "newpassword"
   mysqladmin -u root -p drop user username
   ```

3. **数据库操作**：创建或删除数据库。

   ```bash
   mysqladmin -u root -p create dbname
   mysqladmin -u root -p drop dbname
   ```

4. **服务器状态**：检查当前 MySQL 服务器的运行状态。

   ```bash
   mysqladmin -u root -p status
   ```

这些命令可以在命令行中直接执行，并且需要相应的权限（通常是使用 `-u` 参数指定用户名，`-p` 参数提示输入密码）。

### mysqlbinlog

`mysqlbinlog` 是 MySQL 提供的一个命令行工具，用于读取和展示 MySQL 二进制日志文件的内容。二进制日志文件包含了 MySQL 服务器的所有数据更改操作，例如对数据库的插入、更新、删除等操作，以及服务器的数据定义语句（DDL）操作，如创建或更改表结构等。

一些 `mysqlbinlog` 常见的用途包括：

1. **查看二进制日志内容**： 可以使用 `mysqlbinlog` 来查看二进制日志文件中记录的 SQL 语句，了解数据库的更改历史。例如：

   ```bash
   mysqlbinlog mysql-bin.000001
   ```

   这将输出 `mysql-bin.000001` 文件中的所有 SQL 语句。

2. **导出二进制日志内容**： 可以将二进制日志文件中的 SQL 语句导出到文本文件中，以便后续分析或恢复数据库更改。例如：

   ```bash
   mysqlbinlog mysql-bin.000001 > binlog.sql
   ```

   这将把 `mysql-bin.000001` 文件中的 SQL 语句输出到 `binlog.sql` 文件中。

3. **过滤特定时间段的日志**： 可以使用 `--start-datetime` 和 `--stop-datetime` 参数来选择特定时间段内的日志记录。例如：

   ```bash
   mysqlbinlog --start-datetime="2023-01-01 00:00:00" --stop-datetime="2023-01-02 00:00:00" mysql-bin.000001
   ```

   这将输出 `mysql-bin.000001` 文件中 2023 年 1 月 1 日至 1 月 2 日之间的 SQL 语句。

4. **过滤特定数据库或表的日志**： 可以使用 `--database` 或 `--table` 参数来过滤特定数据库或表的日志记录。例如：

   ```bash
   mysqlbinlog --database=mydatabase mysql-bin.000001
   ```

   这将只输出涉及 `mydatabase` 数据库的 SQL 语句。

`mysqlbinlog` 的使用需要在命令行中执行，并且通常需要相应的文件路径或参数来指定要处理的二进制日志文件。它对于数据库恢复、数据审计以及复制流程中的监控和调试非常有用。

### mysqlshow

`mysqlshow` 是 MySQL 提供的一个命令行工具，用于显示 MySQL 服务器上的数据库、表和列的信息。它允许用户查看数据库和表的基本结构和属性，而无需执行 SQL 查询。

一些 `mysqlshow` 常见的用途包括：

1. **显示所有数据库**： 若要查看 MySQL 服务器上的所有数据库列表，可以简单地执行以下命令：

   ```bash
   mysqlshow -u username -p
   ```

   这将列出 MySQL 服务器上所有可访问的数据库名称。

2. **显示特定数据库中的所有表**： 若要查看特定数据库中的所有表的列表，可以指定数据库名作为参数：

   ```bash
   mysqlshow -u username -p mydatabase
   ```

   这将列出 `mydatabase` 数据库中所有的表名称。

3. **显示表的列信息**： 若要查看特定表的列信息，可以指定数据库和表名作为参数：

   ```bash
   mysqlshow -u username -p mydatabase mytable
   ```

   这将列出 `mydatabase` 数据库中 `mytable` 表的列名称和类型等信息。

4. **显示服务器信息**： 若要查看 MySQL 服务器的基本信息，可以使用 `-i` 参数：

   ```bash
   mysqlshow -u username -p -i
   ```

   这将显示服务器的版本、主机和端口等信息。

`mysqlshow` 是一个轻量级的工具，适用于快速查看 MySQL 数据库和表的结构信息，特别是在管理和调试数据库时非常有用。注意，执行 `mysqlshow` 需要合适的 MySQL 用户权限（通过 `-u` 和 `-p` 参数指定用户名和密码）。

### mysqldumb

`mysqldump` 是 MySQL 提供的一个命令行工具，用于备份 MySQL 数据库的内容。它可以将数据库中的数据、表结构以及相关的 SQL 语句导出到一个文件中，这个文件可以用来恢复数据库或在不同的 MySQL 服务器上重新创建数据库结构和数据。

一些 `mysqldump` 常见的用途包括：

1. **备份整个数据库**： 若要备份整个 MySQL 数据库，可以使用如下命令：

   ```bash
   mysqldump -u username -p dbname > backup.sql
   ```

   这将导出名为 `dbname` 的数据库的结构和数据，并保存到 `backup.sql` 文件中。

2. **备份特定表或多个表**： 若要备份特定的表或多个表，可以在命令中指定表名：

   ```bash
   mysqldump -u username -p dbname table1 table2 > backup.sql
   ```

   这将导出 `dbname` 数据库中的 `table1` 和 `table2` 表的结构和数据到 `backup.sql` 文件。

3. **仅备份表结构而不包含数据**： 若要仅备份表的结构而不包含数据，可以添加 `--no-data` 参数：

   ```bash
   mysqldump -u username -p --no-data dbname > schema_backup.sql
   ```

   这将导出 `dbname` 数据库中的表结构到 `schema_backup.sql` 文件，但不包含表的实际数据。

4. **备份远程数据库**： 若要备份远程 MySQL 数据库，需要添加 `-h` 参数指定远程主机地址：

   ```bash
   mysqldump -h remote_host -u username -p dbname > backup.sql
   ```

   这将连接到名为 `remote_host` 的远程 MySQL 主机，并备份其中的 `dbname` 数据库。

`mysqldump` 是一个非常有用的工具，特别是在定期备份数据库或将数据库迁移到新的服务器时。备份文件可以通过 `mysql` 客户端或其他方式用来恢复数据库。务必注意保护备份文件的安全性，以防止未经授权的访问。



## 日志

### 错误日志

要查看MySQL错误日志，可以使用以下几种方法之一：

- 命令行方式

  ：可以使用命令行工具来实时查看日志文件内容，例如：

  ```bash
  tail -f /var/log/mysql/error.log
  ```

- **MySQL客户端**：通过MySQL客户端连接到数据库，然后使用 `SHOW VARIABLES LIKE 'log_error';` 命令查看错误日志文件的位置。

### 二进制日志

二进制日志（binlog）记录了所有的DDL语句和DML语句，但不包括数据查询（select，show）语句

作用：1.灾难时的数据回复  2.mysql的主从复制

```sql
show variables like '%log_bin%';
```

#### 二进制日志格式

MySQL的二进制日志（Binary Log）支持多种格式来记录数据库操作的细节。这些格式决定了如何将每个数据库操作的详细信息写入二进制日志文件中。目前MySQL主要支持三种二进制日志格式：

1. **Statement-Based Replication (SBR)**：
   - **语句级复制**。在这种格式下，MySQL服务器会将执行的SQL语句以原始的SQL语句形式记录到二进制日志中。当从服务器进行复制时，会重新执行这些SQL语句来达到数据同步的效果。
   - **优点**：日志文件相对较小，因为只记录了SQL语句。
   - **缺点**：在某些情况下，由于数据库版本不同、函数不同等，可能会导致主从不一致的问题，需要特别注意。
2. **Row-Based Replication (RBR)**：
   - **行级复制**。这种格式下，MySQL服务器会将每个受影响行的具体数据变更情况记录到二进制日志中，而不是记录执行的SQL语句。
   - **优点**：更加精确和可靠，不会受到SQL语句执行环境的影响。
   - **缺点**：可能导致日志文件较大，尤其是对于更新大量行的操作。
3. **Mixed Format**：
   - **混合格式**。MySQL在这种格式下会根据具体的操作类型选择适当的日志格式来记录，比如对于简单的语句可以采用SBR，对于复杂的语句或者涉及大量行的操作则采用RBR。
   - **优点**：结合了SBR和RBR的优点，既可以减小日志文件的大小，又可以保证复制的准确性。
   - **缺点**：实现复杂，管理和调试可能比较困难。

```sql
show variables like '%binlog_format%';
```

### 查询日志

### 慢查询日志

## 三大日志完全没了解，黑马mysql运维篇也没看
