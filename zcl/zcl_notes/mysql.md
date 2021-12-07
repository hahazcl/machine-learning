> - SQL语句不区分大小写，因此 SELECT与select是相同的。同样，写成Select也没有关系。 许多SQL开发人员喜欢对所有SQL关键字使用大写，而对所有 列和表名使用小写，这样做使代码更易于阅读和调试。
>
> - 在处理SQL语句时，其中所有空格都被忽略。SQL 语句可以在一行上给出，也可以分成许多行。多数SQL开发人 员认为将SQL语句分成多行更容易阅读和调试。

#### user

- `create user test identified by 'password';`
  - 指定散列口令 IDENTIFIED BY指定的口令为纯文本，MySQL 将在保存到user表之前对其进行加密。为了作为散列值指定口 令，使用IDENTIFIED BY PASSWORD。

- `rename user ben to testuser;`

  - 仅MySQL 5或之后的版本支持RENAME USER。 为了在以前的MySQL中重命名一个用户，可使用UPDATE直接 更新user表

- `drop user testuser;`

  - 自MySQL 5以来，DROP USER删除用户账号和 所有相关的账号权限

- `show grants for test;`

  - ```mysql
    +----------------------------------+
    | Grants for test@%                |
    +----------------------------------+
    | GRANT USAGE ON *.* TO 'test'@'%' |
    +----------------------------------+
    1 row in set (0.06 sec)
    ```

  - 输出结果显示用户test有一个权限USAGE ON *.*。USAGE表示根本没有权限，所以，此结果表示在 任意数据库和任意表上对任何东西没有权限。

  - `grant select,insert on haha.* to test;`

  - 此GRANT允许用户在haha.*（haha数据库的所 有表）上使用SELECT。通过只授予SELECT访问权限，用户bforta 对haha数据库中的所有数据具有只读访问权限。

- `show grants for test;`

  - ```mysql
    +----------------------------------------+
    | Grants for test@%                      |
    +----------------------------------------+
    | GRANT USAGE ON *.* TO 'test'@'%'       |
    | GRANT SELECT ON `haha`.* TO 'test'@'%' |
    +----------------------------------------+
    2 rows in set (0.07 sec)
    ```

- `revoke select on haha.* from test;`

  - REVOKE语句取消刚赋予用户test的SELECT访问权限。被 撤销的访问权限必须存在，否则会出错。

> GRANT和REVOKE可在几个层次上控制访问权限：
>
> - 整个服务器，使用GRANT ALL和REVOKE ALL;
> - 整个数据库，使用ON database.*； 
> -  特定的表，使用ON database.table； 
> - 特定的列； 特定的存储过程

- `set password for test = password('newpassword');`
  - SET PASSWORD更新用户口令。新口令必须传递到Password()函 数进行加密
  - `set password = password('newpassword');`在不指定用户名时，SET PASSWORD更新当前登录用户的口令。

#### 连接mysql

- `show databases;`
- `use haha;`
- `show tables;`
- `use mysql;`切换到mysql内置库
- `show columns from saic_dn;`查看当前选择的数据库内可用表的列表。

- `describe saic_dn;`

- `show status;`用于显示广泛的服务器状态信息

- `SHOW CREATE DATABASE 和SHOW CREATE TABLE`，分别用来显示创 建特定数据库或表的MySQL语句；

  - `show create database haha;`

    - ```mysql
      +----------+---------------------------------------------------------------+
      | Database | Create Database                                               |
      +----------+---------------------------------------------------------------+
      | haha     | CREATE DATABASE `haha` /*!40100 DEFAULT CHARACTER SET utf8 */ |
      +----------+---------------------------------------------------------------+
      1 row in set (0.06 sec)
      ```

  - `show create table saic_dn;`

- `show grants;`用来显示授予用户（所有用户或特定用户）的安 全权限

- `SHOW ERRORS和SHOW WARNINGS`，用来显示服务器错误或警告消息。



#### 检索查询

- `select distinct batch_id from saic_synthesis_order;`按照batch_id去重查询

- `select distinct batch_id from saic_synthesis_order limit 5;`SELECT语句返回所有匹配的行，它们可能是指定表中的每个行。为 了返回第一行或前几行，可使用LIMIT子句。

- `select distinct batch_id from saic_synthesis_order limit 5,10;`LIMIT 5, 10指示MySQL返回从行5开始的10行。第一个数为开始 位置，第二个数为要检索的行数。

- `select distinct batch_id from saic_synthesis_order limit 10 offset 5;`指示MySQL返回从行5开始的10行。第一个数为开始 位置，第二个数为要检索的行数。

- `select saic_synthesis_order.batch_id from haha.saic_synthesis_order;`使用完全限定 的名字来引用列（同时使用表名和列字）。

#### 排序检索数据

- `select outbound_dispatch_number from saic_shipping_rates order by order_total_price_excluding_vat desc;`(从上往下看)从大到小排序,降序排序(ASC 升序)

#### 过滤数据

> 操作符说明 = 等于, <> 不等于, != 不等于, < 小于, <= 小于等于, > 大于, >= 大于等于, BETWEEN 在指定的两个值之间

- `select outbound_dispatch_number from saic_shipping_rates where order_total_price_excluding_vat is null;`判断是否为null

- > NULL与不匹配: 在通过过滤选择出不具有特定值的行时，你 可能希望返回具有NULL值的行。但是，不行。因为未知具有 特殊的含义，数据库不知道它们是否匹配，所以在匹配过滤 或不匹配过滤时不返回它们。 因此，在过滤数据时，一定要验证返回数据中确实给出了被 过滤列具有NULL的行。

- `select outbound_dispatch_number from saic_shipping_rates where order_total_price_excluding_vat in (90.87,1173.10);`**IN操作符一般比OR操作符清单执行更快**

  - ```mysql
    +--------------------------+
    | outbound_dispatch_number |
    +--------------------------+
    | SH202100000040           |
    | SH202100000079           |
    | SH202100000082           |
    | SH202100000093           |
    | SH202100000090           |
    | SH202100000090           |
    +--------------------------+
    6 rows in set (0.08 sec)
    ```

- `select outbound_dispatch_number from saic_shipping_rates where order_total_price_excluding_vat not in (90.87,1173.10);`not条件相反

#### 用通配符进行过滤

- `select prod_id,prod_name from products where pord_name like 'jet%';`找出所有以词jet起头的产品

  - > 区分大小写 根据MySQL的配置方式，搜索可以是区分大小 写的。如果区分大小写，'jet%'与JetPack 1000将不匹配。

- `select prod_id,prod_name from products where pord_name like '%jet%';`

  > 注意尾空格 尾空格可能会干扰通配符匹配。例如，在保存词 anvil 时，如果它后面有一个或多个空格，则子句WHERE prod_name LIKE '%anvil'将不会匹配它们，因为在最后的l 后有多余的字符。解决这个问题的一个简单的办法是在搜索模 式最后附加一个%。一个更好的办法是使用函数,去掉首尾空格。

- `select prod_id,prod_name from products where pord_name like '_ ton anvil';`

  - ```mysql
    +----------+--------------+
    | prod_id  | prod_name    |
    +----------+--------------+
    | ANVO2    | 1 ton anvil  |
    +----------+--------------+
    | ANVO3    | 5 ton anvil  |
    +----------+--------------+
    2 row in set (0.06 sec)
    ```

> - **不要过度使用通配符**。如果其他操作符能达到相同的目的，应该 使用其他操作符。
>
> - 在确实需要使用通配符时，除非绝对有必要，**否则不要把它们用 在搜索模式的开始处**。**把通配符置于搜索模式的开始处，搜索起来是最慢的。**

#### 用正则表达式进行搜索

- `select package_type from saic_sku_database where package_type regexp '蜂巢零件：644 气泡袋' order by id;`

  - ```mysql
    +-----------------------------------------+
    | package_type                            |
    +-----------------------------------------+
    | 蜂巢零件：644 气泡袋                    |
    | 蜂巢零件：644 气泡袋                    |
    | 蜂巢零件：644 气泡袋（供应商）+零件标签    |
    | 蜂巢零件：644 气泡袋                    |
    | 蜂巢零件：644 气泡袋                    |
    | 蜂巢零件：644 气泡袋                    |
    | 蜂巢零件：644 气泡袋                    |
    | 蜂巢零件：644 气泡袋（供应商）+零件标签    |
    | 蜂巢零件：644 气泡袋                    |
    | 蜂巢零件：644 气泡袋                    |
    | 蜂巢零件：644 气泡袋                    |
    | 蜂巢零件：644 气泡袋                    |
    +-----------------------------------------+
    ```

  - 除关键字LIKE被REGEXP替代外，这条语句看上去非常像使用 LIKE的语句（第8章）。它告诉MySQL：REGEXP后所跟的东西作 为正则表达式（与文字正文`蜂巢零件：644 气泡袋`匹配的一个正则表达式）处理。

##### 基本字符匹配

- `select prod_name from products where pord_name regexp '.000' order by prod_name;`  

  - ```mysql
    +-----------------+
    | prod_name       |
    +-----------------+
    | Jet pack 1000   |
    +-----------------+
    | Jet pack 2000   |
    +-----------------+
    2 row in set (0.05 sec)
    ```

  - 这里使用了正则表达式.000。.是正则表达式语言中一个特殊 的字符。它表示匹配任意一个字符，因此，1000和2000都匹配 且返回。

> ，LIKE匹配整个列。如果被匹配的文本在列值 中出现，LIKE将不会找到它，相应的行也不被返回（除非使用 通配符）。而REGEXP在列值内进行匹配，如果被匹配的文本在 列值中出现，REGEXP将会找到它，相应的行将被返回。这是一 个非常重要的差别。
>
> - REGEXP能不能用来匹配整个列值（从而起与LIKE相同的作用）？答案是肯定的，使用^和$定位符（anchor）即可



> **匹配不区分大小写 MySQL中的正则表达式匹配（自版本 3.23.4后）不区分大小写（即，大写和小写都匹配）。为区分大 小写，可使用BINARY关键字，如WHERE prod_name REGEXP BINARY 'JetPack .000'。**

##### 进行OR匹配

- `select prod_name from products where pord_name regexp '1000|2000' order by prod_name;`为搜索两个串之一（或者为这个串，或者为另一个串），使用`|`

##### 匹配几个字符之一

- `select prod_name from products where pord_name regexp '[123] Ton' order by prod_name;`匹配任何单一字符

  - ```mysql
    +-----------------+
    | prod_name       |
    +-----------------+
    | 1 Ton anvil     |
    +-----------------+
    | 2 Ton anvil     |
    +-----------------+
    2 row in set (0.03 sec)	
    ```

```xml
[:alnum:] 任意字母和数字（同[a-zA-Z0-9]）
[:alpha:] 任意字符（同[a-zA-Z]）
[:blank:] 空格和制表（同[\\t]）
[:cntrl:] ASCII控制字符（ASCII 0到31和127）
[:digit:] 任意数字（同[0-9]）
[:graph:] 与[:print:]相同，但不包括空格
[:lower:] 任意小写字母（同[a-z]）
[:print:] 任意可打印字符
[:punct:] 既不在[:alnum:]又不在[:cntrl:]中的任意字符
[:space:] 包括空格在内的任意空白字符（同[\\f\\n\\r\\t\\v]）
[:upper:] 任意大写字母（同[A-Z]）
[:xdigit:] 任意十六进制数字（同[a-fA-F0-9]）
```

#### 创建计算字段

- `select concat(RTrim(Part_Number), package_type) as new_filed from saic_sku_database limit 10;`concat连接两个字段

  - ```mysql
    +---------------------------------+
    | new_filed                       |
    +---------------------------------+
    | 10004100防锈袋                  |
    | 10005000防锈袋                  |
    | 10000432-ASA容器:644；D；气泡袋 |
    | 10000434-ASA气泡袋              |
    | 10000435-ASA自封袋              |
    | 10000501蜂巢零件：644 气泡袋    |
    | 10000502自封袋                  |
    | 10000503自封袋                  |
    | 10000504自封袋                  |
    | 10000506纸箱（供应商）          |
    +---------------------------------+
    10 rows in set (0.11 sec)
    ```

- `select id,Lenght_in_mm,Width_in_mm,Hight_in_mm,Lenght_in_mm*Width_in_mm*Hight_in_mm as Dimension from saic_sku_database limit 10;`

  - ```mysql
    +-------+--------------+-------------+-------------+--------------------+
    | id    | Lenght_in_mm | Width_in_mm | Hight_in_mm | Dimension          |
    +-------+--------------+-------------+-------------+--------------------+
    | 10680 | 93.0         | 93.0        | 40.0        |             345960 |
    | 10681 | 80.1         | 10.2        | 20.0        | 16340.399999999998 |
    | 89403 | 240.0        | 210.0       | 40.0        |            2016000 |
    | 89404 | 240.0        | 210.0       | 40.0        |            2016000 |
    | 89405 | 60.0         | 30.0        | 28.0        |              50400 |
    | 89406 | 235.0        | 90.0        | 60.0        |            1269000 |
    | 89407 | 65.0         | 50.0        | 40.0        |             130000 |
    | 89408 | 120.0        | 80.0        | 25.0        |             240000 |
    | 89409 | 102.0        | 60.0        | 13.0        |              79560 |
    | 89410 | 500.0        | 500.0       | 240.0       |           60000000 |
    +-------+--------------+-------------+-------------+--------------------+
    10 rows in set (0.11 sec)
    ```

#### 使用数据处理函数

```
常用的文本处理函数
函数        	   说明
Left()   	返回串左边的字符
Length() 	返回串的长度
Locate() 	找出串的一个子串
Lower()  	将串转换为小写
LTrim()  	去掉串左边的空格
Right()     返回串右边的字符
RTrim()     去掉串右边的空格
Soundex()   返回串的SOUNDEX值
SubString() 返回子串的字符
Upper()  	将串转换为大写
```

> SOUNDEX是一个将任何文 本串转换为描述其语音表示的字母数字模式的算法。SOUNDEX考虑了类似 的发音字符和音节，使得能对串进行发音比较而不是字母比较。虽然 SOUNDEX不是SQL概念，但MySQL（就像多数DBMS一样）都提供对 SOUNDEX的支持。

```mysql
常用日期和时间处理函数
函数 			说明
AddDate() 	增加一个日期（天、周等）
AddTime() 	增加一个时间（时、分等）
CurDate() 	返回当前日期
CurTime() 	返回当前时间
Date() 		返回日期时间的日期部分
DateDiff() 	计算两个日期之差
Date_Add() 	高度灵活的日期运算函数
Date_Format() 返回一个格式化的日期或时间串
Day() 		返回一个日期的天数部分
DayOfWeek() 对于一个日期，返回对应的星期几
Hour() 		返回一个时间的小时部分
Minute() 	返回一个时间的分钟部分
Month() 	返回一个日期的月份部分
Now() 		返回当前日期和时间
Second() 	返回一个时间的秒部分
Time() 		返回一个日期时间的时间部分
Year() 		返回一个日期的年份部分
```

- `select part_number,create_time from saic_sku_database where Date(create_time) = '2021-10-09';`

  - ```mysql
    +--------------+---------------------+
    | part_number  | create_time         |
    +--------------+---------------------+
    | 10005000     | 2021-10-09 09:54:00 |
    | 10000432-ASA | 2021-10-09 09:54:00 |
    | 10000434-ASA | 2021-10-09 09:54:00 |
    | 10000435-ASA | 2021-10-09 09:54:00 |
    +--------------+---------------------+
    4 rows in set (0.15 sec)
    ```

```
常用数值处理函数
函 数 		说 明
Abs() 	返回一个数的绝对值
Cos() 	返回一个角度的余弦
Exp() 	返回一个数的指数值
Mod() 	返回除操作的余数
Pi() 	返回圆周率
Rand()	返回一个随机数
Sin() 	返回一个角度的正弦
Sqrt() 	返回一个数的平方根
Tan() 	返回一个角度的正切
```

#### 汇总数据

##### 聚集函数

```
SQL聚集函数
函 数 		说 明
AVG() 	返回某列的平均值
COUNT() 返回某列的行数
MAX() 	返回某列的最大值
MIN() 	返回某列的最小值
SUM() 	返回某列值之和
```

- `select AVG(Net_Weight_in_KG) as avg_net_weight from saic_sku_database;`

  - ```mysql
    +--------------------+
    | avg_net_weight     |
    +--------------------+
    | 1.5312376649854007 |
    +--------------------+
    1 row in set (0.48 sec)
    ```

**COUNT()函数有两种使用方式**

- 使用COUNT(*)对表中行的数目进行计数，不管表列中包含的是空 值（NULL）还是非空值。
- 使用COUNT(column)对特定列中具有值的行进行计数，忽略 NULL值。



- `select AVG(distinct Net_Weight_in_KG) as avg_net_weight from saic_sku_database;`

  - ```mysql
    +------------------+
    | avg_net_weight   |
    +------------------+
    | 9.91903870967738 |
    +------------------+
    1 row in set (0.12 sec)
    ```

- `select fo_id,count(*) as count from saic_dn_head group by fo_id limit 5;`

  - ```mysql
    +-------+-------+
    | fo_id | count |
    +-------+-------+
    | NULL  |    52 |
    |     2 |     1 |
    |     3 |     1 |
    |     4 |     1 |
    |     5 |     1 |
    +-------+-------+
    5 rows in set (0.05 sec)
    ```

- `select outbound_dispatch_number,count(*) as count from saic_dn group by outbound_dispatch_number having count(*) >=20 limit 5;`

  - ```mysql
    +--------------------------+-------+
    | outbound_dispatch_number | count |
    +--------------------------+-------+
    | 5A91209004               |    20 |
    | 5A91209006               |    30 |
    | 5A91209011               |    98 |
    | 5A91209018               |    60 |
    | 5A91209019               |   359 |
    +--------------------------+-------+
    5 rows in set (0.06 sec)
    ```

#### 笛卡儿积（cartesian product） 

> 由没有联结条件的表关系返回 的结果为笛卡儿积。检索出的行的数目将是第一个表中的行数乘 以第二个表中的行数。

- `select batch_id,outbound_dispatch_number from saic_synthesis_order,saic_dn_head;`

- `select batch_id,outbound_dispatch_number from saic_synthesis_order,saic_dn_head where saic_synthesis_order.fo_id = saic_dn_head.fo_id;`

> 性能考虑 MySQL在运行时关联指定的每个表以处理联结。 这种处理可能是非常耗费资源的，因此应该仔细，不要联结 不必要的表。联结的表越多，性能下降越厉害

#### 组合查询UNION

- UNION必须由两条或两条以上的SELECT语句组成，语句之间用关 键字UNION分隔（因此，如果组合4条SELECT语句，将要使用3个 UNION关键字）
- UNION中的每个查询必须包含相同的列、表达式或聚集函数（不过各个列不需要以相同的次序列出）。
- 列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以 隐含地转换的类型（例如，不同的数值类型或不同的日期类型）。
- UNION从查询结果集中自动去除了重复的行(UNION ALL 可以取消去除重复)

#### 全文本搜索

> 并非所有引擎都支持全文本搜索，MySQL 支持几种基本的数据库引擎。并非所有的引擎都支持全文本搜索。两个最常使用的引擎为MyISAM和InnoDB， 前者支持全文本搜索，而后者不支持.

#### 数据插入

对于自增的字段,在sql语句中,执行insert时,可以置NULL,mysql会自动自增

> 总是使用列的列表 一般不要使用没有明确给出列的列表的 INSERT语句。使用列的列表能使SQL代码继续发挥作用，即使 表结构发生了变化。



> **提高整体性能**: 数据库经常被多个客户访问，对处理什么请 求以及用什么次序处理进行管理是MySQL的任务。INSERT操作可能很耗时（特别是有很多索引需要更新时），而且它可能降低等待处理的SELECT语句的性能。 如果数据检索是最重要的（通常是这样），则你可以通过在 INSERT和INTO之间添加关键字`LOW_PRIORITY`，指示MySQL 降低INSERT语句的优先级(这也适用于UPDATE和DELETE语句)，如下所示：
>
> ```mysql
> insert low_priorit into
> ```

- `insert into customers (cust_name,cust_address,cust_city) values('Tom', '100 main street', 'USA'),('martin', '42 Galaxy Way','NL');`多行插入语句

##### 插入检索出的数据

- `insert into customers (cust_name,cust_address,cust_city) select cust_name,cust_address, cust_city from cust_new;`

#### 更新和删除数据

- `update customers set cust_email = 'emmer@uud.com', cust_name = 'Maik' where cust_id = '10005';`

> IGNORE关键字 如果用UPDATE语句更新多行，并且在更新这些 行中的一行或多行时出一个现错误，则整个UPDATE操作被取消 （错误发生前更新的所有行被恢复到它们原来的值）。为即使是发 生错误，也继续进行更新，可使用IGNORE关键字，如下所示： `UPDATE IGNORE customers…`

- `update customers set cust_email = NULL where cust_id = '10005';`为了删除某个列的值，可设置它为NULL

> 更快的删除 如果想从表中删除所有行，不要使用DELETE。 可使用TRUNCATE TABLE语句，它完成相同的工作，但速度更 快（TRUNCATE实际是删除原来的表并重新创建一个表，而不 是逐行删除表中的数据）。

#### 创建和操纵表

> 处理现有的表 在创建新表时，指定的表名必须不存在，否则 将出错。如果要防止意外覆盖已有的表，SQL要求首先手工删 除该表（请参阅后面的小节），然后再重建它，而不是简单地 用创建表语句覆盖它。如果你仅想在一个表不存在时创建它，应该在表名后给出`IF NOT EXISTS`。这样做不检查已有表的模式是否与你打算创建 的表模式相匹配。它只是查看表名是否存在，并且仅在表名不 存在时创建它。

