## SQL Course Crash
[TOC]
### Chapter 1
表：table，类似于一个表格；其中同一个数据库中不能有两个相同的表

列：column，表中的一个字段，表由多个字段组成

行：row，行是一个用户的记录可以成为record：记录

主键：primary key，一列，其值能够唯一区分表中的每一个行。

主键的性质：

 - 一个表应该具有一个主键
 - 主键可以由一列也可以由多列组成
 - 主键不可以为空

下面还有几个使用主键的好习惯：

 - 不更新主键列中的值
 - 不重用主键列的值
 - 不在主键列中使用可能被更改的值

### Chapter 2
MySQL是一个客户机-服务器DBMS，因此为了使用MySQL，需要一个客户机来和MySQL打交道，有MySQL命令行程序，MySQL Administrator，MySQL Query Browser。关于一些常用的任务集合可以将之写成一个脚本，该脚本由一系列的MySQL命令组成。

### Chapter 3


### Chapter 4
#### SELECT 语句

#### 检索单列：
```
SELECT prod_name FROM products;
```
SQL 语句以；结束，多条SQL语句之间使用；隔开！
SQL语句不区分大小写，SELECT 和 select是相同的，但是一些数据库名和表名列名可能区分大小写，这取决于数据库的配置！使用SQL语句时，命令中间的空格会被忽略，因此使用两个空格的效果和一个空格是一样的。

#### 检索多个列：
```
SELECT prod_id,prod_name FROM products;
```
检索所有列：
```
SELECT * FROM products；
```
使用通配符来达到目的

#### 检索不同的行：
```
SELECT DISTINCT * FROM products
```
#### 限制结果，检索其中一部分：
```
SELECT prod_name FROM products LIMIT 4 OFFSET 3；
```
检索从第三行开始后的4行，要知道OFFSET从0开始。

### Chapter 5
#### 排序检索数据

#### 按其中一列升序排序：
```
SELECT prod_name FROM products ORDER BY prod_name;
```

#### 按其中多列排序：
```
SELECT prod_id,prod_price,prod_name FROM products ORDER BY prod_price,prod_name;
```
排序的结果优先按照prod_price排序，然后再按照prod_name排序。

#### 按升序或者降序排序：
```
SELECT prod_id, prod_price,prod_name FROM products ORDER BY prod_price DESC, prod_name;
```
该结果将优先按照prod_price降序排序，然后按照prod_name升序排序；
例：找出一组货物中最贵的一个：
```
SELECT prod_price FROM products ORDER BY prod_price DESC LIMIT 1 OFFSET 0;
```
也即将货品按降序排序，并取出第一个

### Chapter 6
#### 过滤数据：使用WHERE子句
```
SELECT prod_name, prod_price FROM products WHERE prod_price = 2.5;
```
列出货物价格等于2.5的货物的名字和价格。

WHERE子句中可以使用的操作符：
```
=,<>,!=,<,>,<=,>=,BWTWEEN AND
```
以BETWEEN,AND 为例
```
SELECT prod_name,prod_price FROM products WHERE prod_price BETWEEN 5.4 AND 10.6;
```
以空值为例：
```
SELECT prod_name FROM products WHERE prod_price is NULL;
```
这条语句返回没有价格（不是价格为0）的货物的名字。

### Chapter 7
#### 数据过滤：使用WHERE子句和NOT,IN来构建更高级的搜索；

#### AND:
```
SELECT prod_id,prod_price,prod_name FROM products WHERE prod_id = 1003 AND prod_price <= 10;
```
#### OR:
```
SELECT prod_name, prod_price FROM products WHERE prod_id = 1002 OR prod_id = 1003;
```
#### AND,OR:
```
SELECT prod_name,prod_price FROM products WHERE prod_id = 1002 OR prod_id = 1003 AND prod_price >= 10;
```
此时输出的结果将可能有prod_price小于1-=0的出现，因为WHERE运算符的判断条件是后面的表达式为真即可，这和C语言中是一致的。因此需要做如下修改：
```
SELECT prod_name,prod_price FROM products WHERE (prod_id = 1002 OR prod_id = 1003) AND prod_price >= 10;
```
这样子就可以了，使用括号改变逻辑判断的次序。

#### IN:
```
SELECT prod_name, prod_price FROM products WHERE prod_id IN(1002,1003) ORDER BY prod_name;
```
注意此时IN的作用和OR是很相似的，但是毫无疑问的是IN明显降低了代码长度。而且使用IN操作符的命令比使用OR操作符的执行得更快。

#### NOT:
```
SELECT prod_name, prod_price FROM products WHERE prod_id NOT IN (1002,1003) ORDER BY prod_name;
```
NOT否定的是跟在它后面的条件，因此该结果为prod_id不等于1002,1003的结果。

### Chapter 8
#### 用通配符过滤，LIKE操作符

#### %百分号通配符：
%表示的是任何字符出现任何次数：
```
SELECT prod_id, prod_name FROM products WHERE prod_name LIKE 'jack%';
```
找出所有prod_name以jack开头的产品。
下划线通配符：
```
_
```
下划线通配符只能匹配单个字符而不是多个字符；
```
SELECT prod_id,prod_name FROM products WHERE prod_name LIKE '_ack';
```

#### 使用通配符的技巧：
 
 - 不要过多使用通配符；
 - 必须要使用通配符时，最好不要将通配符使用在开始处。将通配符放在开始处，搜索起来是最慢的。
 - 注意通配符的位置，确定需要的结果

### Chapter 9
#### 使用正则表达式进行搜索
正则表达式是用来匹配文本字符串的，使用正则表达式有利于实现比较复杂的匹配逻辑。目前几乎所有的程序设计语言都支持正则表达式！正则表达式使用正则表达式语言来建立。下面将要展示的是如何使用MySQL正则表达式。

#### 基本字符匹配：
```
SELECT prod_name FROM products WHERE prod_name REGEXP '1000' ORDER BY prod_name;
```
上面这个例子中REGEXP后面所跟的就是正则表达式，表示prod_name为1000的意思，这个例子并没有展示出正则表达式的威力。看下面一个例子。
```
SELECT prod_name FROM products WHERE prod_name REGEXP '.000' ORDER BY prod_name;
```
这里使用了正则表达式'.000'，它匹配了1000,2000等字符，其中使用了字符
```
.
```
它匹配任何一个字符。
我们假设上列中返回的结果为：
| prod_name      |   
| :-------- | 
| jetpack 1000  | 
| jetpack 2000  | 
那么如果我们使用的是命令
```
SELECT prod_name FROM products WHERE prod_name LIKE '1000' ORDER BY prod_name
```
 那么这些是不会返回任何结果的。
**注意此处的不同！因为LIKE后面跟着的结果是要求整个列的值都要与1000相等，但是如果是REGEXP那么只需要列值中的一部分和1000相等即可。**

#### OR字符匹配
```
SELECT prod_name FROM products WHERE prod_name REGEXP '1000|2000|3000 ORDER BY prod_name;
```
这里使用了OR，和LIKE相似的是我们可以使用多项选择来表示同样的功能。使用OR命令是要注意|之间是否需要括号的问题，该问题和之前的问题是一致的。
比如下面的命令：
```
SELECT prod_name FROM products WHERE prod_name REGEXP '1|2|3he';
```
 本意是想找出匹配1he，2he，3he的结果，然而实际上该命令只会匹配1，2，3he的结果！

#### 匹配几个字符之一
```
SELECT prod_name FROM products WHERE prod_name REGEXP '[123] Tom' ORDER BY prod_name;
```
[123]表示的是匹配其中任意一个字符。
使用该方法是有一个问题就是如果匹配的是1到9该怎么办，那么可以使用[1-9]来代替。

#### 匹配特殊字符：
我们看到的是  
```
. [] | -
```
 这些都是特殊字符，那么如果哦我们需要匹配这些特殊字符时，我们该如何处理这个问题？例子如下：
```
SELECT prod_name FROM products WHERE prod_name REGEXP '\\.' ORDER BY prod_name;
```
 我们可以看到的是：我们使用了
```
\\
```
 转意符，其中 
```
\\. \\- \\| \\[\\] \\n \\f \\t
```
 等等。**我们记得的是在C语言中转意符只需要一个 
```
\ 
```
 ，为什么在MySQL中需要两个转意符呢？这是因为（MySQL自己解释一个，正则表达式库解释另外一个!）**

#### 匹配字符类：
```
任意字符和字母 [:alnum:] = [a-zA-Z0-9] ; 任意字符 [:alpha:] = [a-zA-Z] ; 空格和制表[:blank:] = [\\t] 任意数字 [:digit:] = [0-9] 任意小写字母 [:lower:] = [a-z]; 等等还有很多，此处就不一一列举了。 
```

#### 匹配多个实例：
| 元字符|   说明 |
| :--------: | :----|
| * |  0个或者多个匹配{0，} |
| +     | 1个或者多个匹配等同于{1，} | 
| ? | 0个或者一个匹配等同于{0，1}|
| {n} | 指定数目的匹配 |
| {n,}  | 不少于数目n的匹配|
| {n,m} | 匹配数目的范围在n和m之间，m不超过255|
下面举个例子来说明这个问题：

```
SELECT prod_name FROM products WHERE prod_name REGEXP '\\([0-9] sticks?\\)' ORDER BY prod_name;
```

搜索结果如下：
| prod_name|  
| :--------: | 
| TNT (1 stick) | 
| TNT (5 sticks) |

```
\\([0-9] sticks? \\)
```
, 其中 
```
\\(
```
 表示匹配字符
```
(
```
, 
```
[0-9]
```
 表示匹配字符0-9，
```
sticks?
```
 表示匹配字符stick或者sticks，
```
\\)
```
表示匹配字符
```
)
```
; 其中需要解释的就是
```
sticks?
```
中
```
s?
```
表示的是字符
```
s
```
可以出现0次或者1次。

下面是另外一个例子我们需要匹配连在一起的四个数字：
- 
```
SELECT prod_name FROM products WHERE prod_name REGEXP '[[:digit:]]{4};
```
需要注意的是，使用正则表达式来完成字符匹配往往有多种方法，比如该命令还可以表示为：
- 
```
SELECT prod_name FROM products WHERE prod_name REGEXP '[0-9][0-9][0-9][0-9]';
```

#### 定位符
目前使用的正则表达式都是匹配一列中任意位置的文本，使用定位符可以匹配特定位置的文本。
| 元字符|   说明 |
| :--------: | :----|
| ^ |  文本的开始 |
| $ | 文本的结尾 |
| [[:<:]] | 词的开始 |
| [[:>:]] | 词的结尾 |
 - 注意^的两种用于^用于[]中比如[^a] 表示的非a，^[a]表示以a开始的字符串。
 - REGEXP可以和LIKE起一样的作用，只需要使用^和$，REGEXP `^$`和  LIKE ` `
 是等价的。

### Chapter 10
#### 使用计算字段
为什么需要计算字段：存储在数据库中的数据一般不是应用程序所需要的格式，例子如下：
 - 如果想在一个字段中既显示公司的名字，又显示公司的地址，但是这两个信息一般包含在不同的表列中。
 - 城市，省，和邮政编码存储在不同的列中，但是邮件标签打印程序需要将它们一个恰当的格式检索出来。
 - 列数据是大小写混恶化的，但是报表程序需要把所有的数据按大写表示出来。
 - 物品订单表存储物品的价格和数量，但是不需要存储物品的总价格。为了打印发票，需要物品的总价格。
 - 需要依据表数据进行总数，平均数等常用统计信息。
 上面这些实例表示了存储在数据库中的数据往往和我们应用所需要的数据是有一定的距离的，因此我们需要对检索出的数据做一定的转换，而不是直接使用检索出的数据即可。为了完成这些任务，我们就需要使用计算字段了。**需要注意的是，不同于列，计算字段并不实际存在于数据库表中，计算字段是运行时在SELECT语句内创建的！**
 **字段**基本上和列的意思相同，经常可以互换使用，不过数据库列一般称为列，而字段通常用于表示计算字段。**需要注意的是：MySQL使用的是客户机服务器模式，只有数据库或者数据库管理者知道那些是列，那些是计算字段。从客户机的角度来说，列和字段是没有区别的。**

#### 拼接字段：
下面使用例子说明如何使用计算字段，vendors 表中包含供应商和位置信息，假设需要生产一个供应商报表，需要在供应商的名字中按照name(location)这样的格式列出供应商的位置。此处报表需要单个值，但是表中的数据是存储在vend_name和vend_country不同列中的。
```
SELECT Concat(vend_name,'(', vend_country, ')') FROM vendors ORDER BY vend_name;
```
Concat()函数用于拼接字符串，我们可以看到的是我们也可以在客户机中完成以上功能，得到vend_name和vend_country的信息后在客户机完成以上功能，但是如果服务器资源够多，客户机性能受限的话，将该任务交给服务器是更合适的。
以上的例子中如果vend_name 后面有空格那么数据的格式将不是很好看。我们将上面的命令改写为：
```
SELECT Concat(RTrim(vend_name),'(',RTrim(vend_country),')') FROM vendors ORDER BY vend_name;
```

#### 使用别名：
使用SELECT语句拼接字段后新的计算列的名字是什么呢？实际上，它没有名字，它只是一个值。如果只是使用SQL语句看一下结果，并无不可，但是一个没有命名的列不能用户客户机的应用中，因为客户机没法引用它?(**需要更近一步的解释！**)。为了解决这个问题，SQL支持别名，别名(alias)是一个字段或者值的替换名。
```
SELECT Concat(RTrim(vend_name),'(','RTrim(vend_country)',')') AS vend_title FROM vendors ORDER BY vend_name;
```
使用了别名后，表中多了一个字段vend_title，它只是SQL创建一个包含指定名为vend_title的字段。现在任何客户机都可以按名引用这个列，就像它是一个实际的表列一样。

#### 执行算术运算：
```
SELECT prod_id, quality, item_price quantity*item_price AS expanded_price FROM orderitems WHERE order_num = 2000;
```
MySQL算术操作符：
| 操作符 | 说明 |
| :---: | :----|
| + |  加 |
| - | 减 |
| * | 乘 |
| / | 除 |

### Chapter 11
#### 使用数据处理函数
和大多数语言一样，SQL支持用函数来处理数据。函数一般是在数据上执行的，它给数据处理提供了方便。对于函数的使用需要注意的是：
 - 函数没有SQL可移植性强，大多数的SQL语句在不同的DBMS之间是可以移植的，但是不同的DBMS之间对相同的命令往往有着不同的函数名和函数处理细节。

使用函数，大多数的SQL实现支持下面几种类型的函数：
 - 用于处理字符串（比如删除或者填充值，转换值的大小写）
 - 用于在数值上进行算术操作（如返回绝对值，进行四则运算）
 - 用于处理日期和时间值并从这些值中提取特定的成分（例如，返回两个日期之差，检测日期的有效性，返回月份）的日期和时间函数
 - 返回DBMS当前状态下的特殊信息（如返回用户的登录信息，检查版本细节）的系统函数
 
#### 文本处理函数：
```
SELECT vend_name, Upper(vend_name) AS vend_name_upcase FROM vendors ORDER BY vend_name;
```

 |  函数 | 说明 |  函数 | 说明 |
| :---: | :----|  :---: | :----|
| Left() |  返回左边的字符 | LTrim() | 去掉串左边的空格 |
| Length() | 返回字符串的长度 | Locate() | 找出串的一个子串 |
| Right() | 返回串右边的字符 |
等等。。。

#### 日期和时间处理函数
日期的格式为：yyyy-mm--dd
常用的日期处理函数
 |  函数 | 说明  |  函数 | 说明 |
| :---: | :---- | :---: | :----|
| CurDate() |  返回当前日期 | Hour() | 返回一个时间的小时部分 |
| CurTime() | 返回当前时间 | Now()| 返回当前日期和时间 |
| Time() | 返回一个日期时间的时间部分 |
等等
举几个例子：
```
SELECT cust_id, order_num FROM orders WHERE Date(order_date) BETWEEN '2005-09-01' AND '2006-09-01';
```
```
SELECT cust_id, order_num FROM orders WHERE Year(order_date) = 2005 AND Month(order_date) = 9;
```

#### 数值处理函数
|  函数 | 说明  |  函数 | 说明 |
| :---: | :---- | :---: | :----|
| Abs() |  返回一个数的绝对值 | Rand() | 返回一个随机数 |
| Cos() | 返回数的余弦 | Sqrt() | 返回一个数的平方根 |
| Mod() | 返回除操作的余数 |

### Chapter 12
#### 汇总数据
本章介绍什么是SQL的聚集函数以及如何利用它们汇总表的数据
#### 聚集函数
我们经常需要汇总数据但是不需要把它们实际检索出来，为此MySQL提供了专门的函数。这些类型的检索例子有以下几种
 - 确定表中行数（或者满足某个条件或包含某个特定值的行数）
 - 获得表中行组的和
 - 找出表列（或者所有行或者某些特定的行）的最大值，最小值和平均值

#### 聚集函数：
|  函数 | 说明  |  函数 | 说明 |
| :---: | :---- | :---: | :----|
| AVG() |  返回某列的平局值 | COUNT() | 返回某列的行数 |
| MAX() | 返回某列的最大值 | MIN() | 返回某列的最小值 |
| SUM() | 返回某列值的和 |
举几个例子：
```
SELECT AVG(prod_price) AS avg_price FROM products;
```
```
SELECT AVG(prod_price) AS avg_price AVG(prod_count) AS avg_count FROM products WHERE prod_id = 1002;
```
```
SELECT COUNT(*) AS num_cust FROM customers;
```
```
SELECT COUNT(cust_email) AS num_cust FROM customers;
```
```
SELECT MAX(prod_prices) AS max_price FROM products;
```

#### 聚焦不同的值：
```
SELECT AVG(DISTINCT prod_price) AS avg_price FROM products WHERE prod_id = 1003;
```
**注意：**DISTINCT只能用于列，不能用于字段。

#### 组合聚集函数：
```
SELECT COUNT(*) AS num_items, MIN(prod_price) AS price_min, MAX(prod_price) AS price_max, AVG(prod_price) AS price_avg FROM products;
```

### Chapter 13
分组数据：本章介绍如何分组数据，一遍能够汇总表内容的子集。这涉及两个新SELECT语句子句，分别是GROUP BY 子句和HAVING 子句。
#### 数据分组：
目前为止，所有计算都是在表的所有数据或者匹配特定的WHERE子句的数据上进行的。下面的例子：
```
SELECT COUNT(*) AS num_prod FROM products WHERE vend_id = 1003;
```
返回的是prod_id=1003的商品的数字，但是如果要返回每个供应商提供的产品数目怎么办？或者返回只提供单项产品的供应商所提供的产品，或返回提供10个以上产品的供应商怎么办？
此时我们就需要使用分组了。分组允许把数据分为多个逻辑组，以便能够对每个组进行聚集计算。

#### 创建分组：
分组是在SELECT语句的GROUP BY子句中建立的。理解分组的办法是看一个例子：
```
SELECT vend_id, COUNT(*) AS num_prods FROM products GROUP BY vend_id;
```
结果如下：
| vend_id | num_prods |
| :---: | :---:|
| 1001 |  3 |
| 1002 | 2 |
| 1003 | 7 |
上面的SELECT语句指定了两个列，vend_id包含产品供应商的ID，num_prods为计算字段COUNT(*)函数建立。GROUP BY 语句指定按vend_id排序并分组数据。这导致对每个vend_id而不是整个表计算num_prods一次。**因为使用了GROUP BY，就不必指定要计算每个组了。系统会自动完成，GROUP BY 子句指示MySQL分组数据，然后对每个组而不是整个结果进行聚集。**
在具体使用GROUP BY之前，需要知道一些重要的规定。
 - GROUP BY子句可以包含任意数目的列。这使得对分组进行嵌套，微数据分组提供更细致的控制。
 - 如果GROUP BY子句中嵌套了分组，数据将在最后规定的分组上进行汇总。换句话说，建立分组时，指定的所有列都一起计算。（需要更进一步的解释）
 - GROUP BY 子句中列出的每个列都必须是检索列或有效的表达式（但不能使用聚集函数）。如果SELECT 中使用表达式，则必须在GROUP BY子句中指定相同的表达式。不能使用别名。
 - 除了聚集计算语句外，SELECT语句中的每个列都必须在GROUP BY子句中给出。
 - 如果分组中具有NULL值，则NULL作为一个分组返回。如果有多行NULL值，它们作为一组。
 - **GROUP BY 子句必须在WHERE子句之后，ORDER BY子句之前。**

#### 过滤分组
除了使用GROUP BY分组数据外，MySQL还允许过滤分组，规定包括哪些分组，排除哪些分组。例如，可能想要列出至少有两个订单的所有顾客，为了得出这种数据，必须基于完整的分组而不是个别的行进行过滤。
WHERE子句使用来过滤指定的行而不是分组。事实上，WHERE也没有分组的概念。
不使用WHERE那么就使用HAVING子句。HAVING非常类似于WHERE，事实上目前所学的所有类型的WHERE子句都可以用HAVING来代替。唯一的差别是WHERE过滤行，HAVING 过滤分组。
如果过滤分组参见下例：
```
SELECT cust_id, COUNT(*) AS orders FROM orders GROUP BY cust_id HAVING COUNT(*) >= 2;
```
 - **注意一个重要的差别！HAVING和WHERE的差别，WHERE在数据分组前进行过滤，HAVING 在数据分组后进行过滤。这是一个重要的区别！因此WHERE会影响分组结果，但是HAVING不影响分组结果，它只是过滤掉一些分组。**
下面给出一个WHERE和HAVING共同使用的例子：

```
SELECT vend_id, COUNT(*) AS num_prods FROM products WHERE prod_price >= 10 GROUP BY vend_id HAVING COUNT(*) >= 2;
```
翻译：找出prod_price>=10的vend_id,以vend_id分组，统计每一组的数目，并过滤掉每组数目小于2的分组。

#### 分组和排序
GROUP BY 和 ORDER BY经常完成相同的工作，但是它们是非常不同的。
| ORDER BY| GROUP BY |
| :---: | :---:|
| 排序产生的输出 |  分组行。单输出可能不是分组的顺序 |
| 任意列都可以使用 | 只能用于选择列或者表达式列，而且必须使用每个选择列表达式 |
|   | 如果和聚集函数一起使用列或者表达式，则必须使用 |
```
SELECT order_num, SUM(quantity*item_price) AS ordertotal FROM orderitems GROUP BY order_num HAVING SUM(quantity*item_price) >= 50;
```
按照订单号顺序输出。
```
SELECT order_num, SUM(quantity*item_price) AS ordertotal FROM orderitems GROUP BY order_num HAVING SUM(quantity*item) >= 50 ORDER BY ordertoal;
```
按照总单价进行排序输出。

#### SELECT子句顺序
| 子句| 说明 | 是否必须使用 |
| :--- | :---:|:---: |
| (1) SELECT |  要返回的列或表达式 |  是 |
| (2) FROM | 从中检索数据的表 | 仅在从表选择数据时使用 |
| (3) WHERE  | 行级过滤 | 否 |
| (4) GROUP BY| 分组说明| 仅在按组计算聚集时使用|
| (5) HAVING | 组级过滤 | 否 |
| (5) ORDER BY | 输出排序顺序 | 否 |
| (6) LIMIT | 要检索的行数 | 否 | 

### Chapter 14
#### 使用子查询进行过滤
本章使用的数据库表是关系表。订单存储在两个表中。对于包含订单号、客户ID、订单日期的每个订单，orders表存储一行。各订单的物品存储在相关的orderitems表中。orders不存储客户信息，它只存储客户ID。实际的客户信息存储在customers表中。那些现在需要列出订购物品TNT2的所有客户，如何检索?
顺序如下：
1. 检索包含物品TNT2的所有订单的编号
2. 检索具有前一步骤列出的订单编号的所有客户的ID
3. 检索前一步骤返回的所有客户ID的客户信息
```
SELECT order_num FROM orderitems WHERE prod_id = 'TNT2';
```
  假设order_num结果为2001，2003；
```
SELECT cust_id FROM orders WHERE order_num IN (2001，2003);
```
 将上面两个命令结合起来可以得到：
```
 SELECT cust_id FROM orders WHERE order_num IN (SELECT order_num FROM orderitems WHERE prod_id = 'TN2');
```
假设得到客户ID的结果为10002，10004；
那么查询客户的ID和信息如下:
```
SELECT cust_name,cust_contack FROM customers WHERE cust_id IN(10002,10004);
```
可以将上面的三个查询结合到一起：
```
SELECT cust_name, cust_contack FROM customers WHERE cust_id IN( SELECT cust_id FROM orders WHERE order_num IN ( SELECT order_num FROM orderitems WHERE prod_id='TNT2'));
```
我了执行上面的SELECT语句，其实MYSQL执行了三条SELECT语句。虽然对嵌套的子查询的数目并没有限制，不过在实际使用时由于性能的限制，不能嵌套太多的子查询！
注意点：
 - WHERE子句中的应该保证SELECT语句具有和WHERE子句中相同数目的列。
 - 虽然子查询一般与IN结合使用，但也可以用于测试等于，不等于等等。

#### 作为计算字段使用子查询
假如需要显示customers表中每个客户的订单总数。订单与相应的客户ID存储在orders表中。
1. 从customers表中检索客户列表
2. 对于检索出的每个客户，统计其在orders表中的订单数目

SQL 语句如下：

```
SELECT cust_name, cust_state, (SELECT COUNT(*) FROM orders WHERE orders.cust_id = customer.cust_id) AS orders FROM customers ORDER BY cust_name;
```

### Chapter 15
联结表
SQL最强大的功能之一就是能在数据检索的执行中联接(join)表。联结表是利用SQL的SELECT能执行的最重要的操作，很重要！
#### 关系表
理解关系表的最好的方法是来看一个现实世界的例子。
假如有一个包含产品目录的数据库表，其中每种类别的物品占一行。对于每种物品要存储的信息包含产品描述和价格，以及生产该产品的供应商信息。
现在，假如有由一个供应商生产的多种物品，那么在何处存储供应商信息（供应商名，地址，联系方法等）呢？将这些数据和产品信息分开存储的理由如下：
 - 因为同一个供应商生产的每个产品的供应商信息都是相同的，对每个产品重复此信息是很浪费的。
 - 如果供应商信息要改变，只需要改动一次就行
 - 如果有重复数据，很难保证每次输入该数据的方式都相同。可能会有不同的数据格式。

**关键是：**相同的数据出现多次不是一件好事，该因素是关系数据库设计的基础！关系表的设计就是要保证把信息分解成多个表，一类数据一个表。各个表通过某些常用的值（即关系数据库中的关系）互相关联！
在这个例子中，我们可以建立两个表，一个存储供应商信息，另一个存储产品信息。vendors表包含所有的供应商的信息，每个供应商占一行，每个供应商具有唯一的标识，该标识成为主键（primary key）,可以是供应商ID也可以是其他唯一值。
products表只存储产品信息，它除了存储供应商ID（vendors表的主键）外不存储其他供应商信息。**vendors表的主键又叫做products表的外键**，它将vendors表和products表相关联。利用供应商ID能从vendors表中得到供应商的详细信息。
**外键（foreign key）** 是某个表中的一列，它包含了另外一个表的主键值，它定义了两个表之间的关联！
这样的好处如下：
 - 供应商信息不重复，不浪费时间和空间
 - 如果供应商信息变动，可以只更新vendors表中的单个记录，相关表中的数据不需要变动
 - 由于数据无重复，显然数据是一致的，这使得数据处理更加简单

总之，关系数据可以有效地存储和方便地处理。因此，关系数据库的可伸缩性远比非关系数据库要好！（**需要更多的解释和比较**）
**可伸缩性（scale）**能够适应不断增加的工作量而不失败。设计良好的数据库或者应用程序称之为可伸缩性好（scale well）。

为什么使用联结。分解数据为多个表能够更有效地存储，更方便地处理，并且具有更好的伸缩性。但是这些好处也是有代价的。
如果数据存在于多个表中，怎样用单条SELECT语句检索出数据? 使用联结，联结是一种机制用来在一条SELECT语句中关联表，因此称之为联结。

####创建联结
```
SELECT vend_name,prod_name,prod_price FROM vendors,products WHERE vendors.vend_id = products.vend_id ORDER BY vend_name, prod_name;
```
需要注意的是在引用中如果可能出现二义性时，必须使用完全限定列名。这个和C++语言中的namespace是类似的。如果一条SQL语句存在二义性，MySQL将会返回错误。
**记住：所有的联结都要有WHERE子句，否则上例子会返回一个笛卡尔积的结果!**

内部联结，目前为止使用的联结是等值联结（equijoin），它基于两个表之间的相等测试。这种联结成为内部联结。对于这种联结还有一个新的写法：
```
SELECT vend_name, prod_name, prod_price FROM vendors INNER JOIN products ON vendors.vend_id = products.vend_id;
```
这是ANSI SQL推荐的写法！

联结多个表
```
SELECT prod_name,vend_name,prod_price,quantity FROM orderitems,products,vendors WHERE products.vend_id = vendors.vend_id AND orderitems.prod_id = products.id AND order_num = 20005;
```
联结越多的表，性能下降得越厉害，尽量不要联结不必要的表。

和子查询的相互转换：
```
SELECT cust_name, cust_contack FROM customers WHERE cust_id IN( SELECT cust_id FROM orders WHERE order_num IN ( SELECT order_num FROM orderitems WHERE prod_id='TNT2'));
```
上面的命令可以写为：
```
SELECT cust_name, cust_contack FROM customers, orders, orderitems WHERE prod_id = 'TNT2' AND orders.order_num = orderitems.order_num AND customers.cust_id = orders.cust_id; 
```
**小结：**
联结是SQL中最重要最强大的特性，有效地使用联结需要对关系数据库设计有基本的了解。本章主要介绍了最长使用的联结形式。下一章介绍如何创建其他类型的联结。

### Chapter 16
创建高级联结
#### 使用表别名
使用表别人的好处有：
 - 缩短SQL语句
 - 允许在单条SELECT语句中多次使用相同的表

看前一章的例子
```
SELECT cust_name, cust_contack FROM customers, orders, orderitems WHERE prod_id = 'TNT2' AND orders.order_num = orderitems.order_num AND customers.cust_id = orders.cust_id; 
```
可以简写为：
```
SELECT cust_name,cust_contact FROM customers AS c, orders AS o, orderitems AS oi WHERE c.cust_id = o.cust_id AND oi.order_num = o.order_num AND prod_id = 'TNT2';
```
这里c是表customers的别名，o是orders的别名，oi是orderitems的别名。**注意：**表别名只在查询执行中使用，与列别名不同的是表别名不返回到客户机。

#### 使用不同类型的联结
自联结：
如前所述，使用表别名的主要原因之一是能够在单条SELECT 语句中不止一次地使用相同的表。
下面给出一个例子：
```
SELECT prod_id,prod_name FROM products WHERE vend_id = (SELECT vend_id FROM products WHERE prod_id = 'DTNTR');
```
这里使用了子查询，找出prod_id = 'DTNTR'的vend_id，找出该vend_id对应的prod_id，prod_name；
下面给出使用了联结的查询：
```
SELECT p1.prod_id,p1.prod_name FROM products AS p1, products AS p2 WHERE p1.vend_id = p2.vend_id AND p2.prod_id = 'DTNTR';
```
此查询中需要查询该表两次，但是因为这两个表是相同的表。因此products在FROM语句中出现了两次，为了消除二义性，此处使用了表别名。
**注意：**自联结可以完成的功能，用自联结而不用子查询。

#### 自然联结
无论何时对表进行联结，应该至少有一个列出现在不止一个表中（被联结的列）。标准的联结返回所有的数据，甚至相同的列多次出现。自然联结排除多次出现，是每个列值返回一次。
这个先过，有点绕。。。。

#### 外部联结
许多联结将一个表中的行与另一个表中的行相关联。但是有时候会需要包含没有关联行的那些行。例如需要使用联结完成以下工作：
 - 对每个客户下了多少订单进行计数，包括那些至今尚未定下订单的客户；
 - 列出所有产品以及订购数量，包括没有人订购的产品；
 - 计算平均销售规模，包括那些至今尚未下订单的客户；
上面的例子中，联结包含了那些在相关表中没有关联行的行
```
SELECT customers.cust_id, orders.order_num FROM customers LEFT OUTER JOIN orders ON customers.cust_id = orders.cust_id;
```
用右联结可以表示为：
```
SELECT customers.cust_id, orders.order_num FROM customers RIGHT OUTER JOIN orders ON orders.cust_id = customers.cust_id;
```

#### 使用带聚集函数的联结
检索所有客户及每个客户所下的订单数：
```
SELECT customers.cust_name, customers.cust_id, COUNT(orders.order_num) AS num_ord FROM customers INNER JOIN orders ON customers.cust_id = orders.cust_id GROUP BY customers.cust_id;
```
用外联结如下：
```
SELECT customers.cust_name, customers.cust_id COUNT(orders.order_name) AS num_ord FROM customers LEFT OUTER JOIN orders ON customers.cust_id = orders.cust_id GROUP BY customers.cust_id;
```
外联结和上面的联结的不同在于，那些没有下订单的用户也会被包括进来，只要其满足customers.cust_id = orders.cust_id; 

### Chapter 17
#### 组合查询
多数SQL查询都只包含从一个或多个表中返回数据的单条SELECT语句。MySQL支持执行多个查询，并将结果作为单个查询结果返回。这些组合查询通常称为并（union）或者符合查询（compound query）。有两种基本情况，其中需要使用组合查询：
 - 对单个查询中从不同的表返回类似结果的数据；
 - 对单个表执行多个查询，按单个查询返回数据；

**组合查询和多个WHERE条件：**多数情况下，组合相同表的两个查询完成的工作和具有多个WHERE子句条件的查询完成的工作相同。换句话来讲，任何具有多个WHERE子句的SELECT语句都可以作为一个组合查询给出。下面章节将给出例子。

#### 创建组合查询
可以用UNION操作符来组合数条SQL查询。
举一个例子：加入需要价格小于等于5的所有物品的一个 列表，而且还想包括供应商1001和1002生成的所有物品（不考虑价格）。当然，可以使用WHERE 子句完成此工作，不过我们将使用UNION；
```
SELECT vend_id,prod_id,prod_price FROM products WHERE prod_price<=5 OR vend_id IN (1001,1002);
```
使用UNION：
```
SELECT vend_id,prod_id,prod_price FROM products WHERE prod_price<=5
UNION
SELECT vend_id,prod_id,prod_price FROM products WHERE vend_id IN (1001,1002);
```
UNION规则：
 - UNION必须由两条或者两条以上的SELECT语句组成，语句之间用关键字UNION分隔
 - UNION中的每个查询必须包含相同的列，表达式或聚集函数（可以以不同的顺序给出）
 - 列数据类型必须兼容：类型可以不必完全相同，但必须是DBMS可以隐含地转换的类型

包含或者取消重复的行：
UNION从查询结果中会自动去除重复的行，如果需要显示重复 的行，将UNION用UNION ALL代替即可。

对组合查询结果排序：
SELECT语句的输出用ORDER BY子句排序。在使用UNION组合查询时，只能使用一条ORDER BY 子句。它必须出现在最后一条SELECT语句之后。

### Chapter 18
全文本搜索
#### 理解全文本搜索
并非所有的引擎都支持全文本搜索 MySQL支持集中基本的数据库引擎。并非所有的引擎都支持全文本搜索。两个最常用的引擎是MyISAM和InnoDB，前者支持全文本搜索，后者不支持。
**如果需要使用全文本搜索引擎，切记这一点！**
我们之前介绍了LIKE和REGEXP关键字，这些搜索机制很有用，但是存在几个下面的限制：
 - 性能，通配符和正则表达式匹配通常要求匹配表中所有行（而且这些搜索极少使用表索引）。因此这些搜索可能非常耗时。
 - 明确控制， 使用通配符和正则表达式匹配，很难明确控制匹配什么不匹配什么。
 - 智能化的结果， 不够智能
 
#### 使用全文本搜索
 为了进行全文本搜索，必须索引被搜索的列，而且要随着数据结果的改变不断地重新索引。在对表列进行适当的设计后，MySQL会自动进行所有的索引和重新索引。
启用全文本搜索支持：一般在建表时启用全文本搜索。CREATE TABLE 语句接受FULLTEXT子句，它给出被索引列的一个逗号分隔的列表。
```
CREATE TABLE productnotes
(
	note_id int NOT NULL AUTO_INCREMENT,
	prod_id char(10) NOT NULL,
	note_date datetime NOT NULL,
	note_text text NULL,
	PRIMARY KEY(note_id),
	FULLTEXT(note_text)
)ENGINE=MyISAM;
```
MySQL根据FULLTEXT(note_text)的指示对它进行索引。这里FULLTEXT索引单个列，如果需要也可以指定多个列。
**不要在导入数据时使用FULLTEXT**，应该在导入数据之后，在修改表，定义FULLTEXT。

进行全文本搜索：
在索引之后，使用两个函数Match()和Against()执行全文本搜索，其中Match()指定被搜索的列，Against()指定要使用的搜索表达式。
```
SELECT note_text FROM productnotes WHERE Match(note_text) Against ('rabbit');
```

### Chapter 19
插入数据
#### 数据插入
毫无疑问，SELECT是最常使用的SQL语句。但是还有三个经常使用的SQL语句需要学习。第一个就是INSERT。
INSERT就是用来插入（或者添加）行到数据库表的。插入可以用几种方式使用：
 - 插入完整的行
 - 插入行的一部分
 - 插入多行
 - 插入某些查询的结果

```
INSERT INTO customers VALUES(NULL, 'Pep', '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA', NULL, NULL);
```
一个更繁琐的但是也更安全的写法如下：
```
INSERT INTO customers(cust_id,cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact ) VALUES(NULL, 'Pep', '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA', NULL);
```
如果该列可以为空，那么可以省略该列。多个INSERT可以组合到一起：
```
INSERT INTO customers(cust_id,cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact ) VALUES(NULL, 'Pep', '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA', NULL),(NULL, 'And', '101 Main Street', 'Los Angeles', 'CA', '90046', 'USA', NULL) ;
```
插入检索出的数据：
```
INSERT INTO customers(cust_id, cust_contact, cust_email, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country) SELECT cust_id, cust_contact, cust_email, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country FROM custnew;
```

### Chapter 20
更新和删除数据
更新数据：使用UPDATE，可以采用两种方式UPDATE:
 - 更新表中特定行
 - 更新表中所有行
 
下面分别对它们进行介绍：
**不要省略WHERE子句：** 在使用UPDATE时一定要注意，因为不小心就更新表中所有行
基本的UPDATE语句非常容易使用。基本的UPDATE语句由三个部分组成，分别是：
 - 要更新的表；
 - 列名和它们的新值
 - 确定要更新行的过滤条件
 
举一个简单的例子如下：
```
UPDATE customers SET cust_email = 'elmer@gmail.com' WHERE cust_id = 1005;
```
UPDATE语句总是以要更新的表的名字开始。在此例子中，要更新的表的名字是customers，SET命令用来将新值赋给被更新的列。UPDATE语句以WHERE子句结束，它告诉MySQL更新哪一行。
更新多列的语法和这和很相似：
```
UPDATE customers SET cust_name = 'The Fund', cust_email = 'elemer@gmail.com' WHERE cust_id = 1005;
```
在UPDATE中语句中使用子查询，。
IGNORE关键字，使用UPDATE关键字更新多行是，如果遇到错误将会停止，使用IGNORE关键字后，会忽略错误继续进行更新余下的更新命令。

#### 删除数据
为了从一个表中删除数据，使用DELETE语句。可以两种方式使用DELETE：
 - 从表中删除特定的行
 - 从表中删除所有行
 
**不要省略WHERE子句！**

DELETE 使用例子：
```
DELETE FROM customers WHERE cust_id = 1006;
```
**DELETE删除的是表中的行，删除的不是表本身，因此即便所有的行都被删除，表依然是存在的!**
如果要删除所用行，可以不用DELETE，而是使用TRUNCATE TABLE，它完成相同的工作，但是速度更快，它实际直接删除原来的表并重新创建一个而不逐行删除表中的数据。

#### 更新和删除的指导原则
前一节中使用的UPDATA和DELETE语句全部拥有WHERE子句。
下面是许多SQL程序员使用UPDATA或者DELETE时所遵循的习惯。
 - 除非确实打算更新或删除每一行，否则绝对不使用不带WHERE子句的UPDATE或DELETE语句
	 - 保证每个表都有主键，尽可能像WHERE子句那样使用它
	 - 相对UPDATA 或 DELETE 语句使用 WHERE子句前，应该先用SELECT进行测试，保证它过滤的是正确的记录，以防编写的WHERE子句不正确。
	 - 使用强制实施引用完整性的数据库，这样MySQL将不允许删除具有与其他表相关联的数据的行。
	
### Chapter 21
表的创建，更改和删除的基本知识
#### 创建表
一般有两种创建表的方法：
 - 使用具有交互式创建和管理表的工具
 - 可以直接用MySQL语句操纵

为了用程序创建表，可使用SQL的CREATE TABLE语句。
表创建：
为了使用CREATE TABLE 创建表，必须给出下列信息：
 - 新表的名字，在关键字 CREATE TABLE 后面给出
 - 表列的名字和定义，用逗号分隔
 
举一个例子：
```
CREATE TABLE customers
(
 cust_id  int NOT NULL AUTO_INCREMENT,
 cust_name char(50) NOT NULL,
 cust_address char(50) NULL,
 PRIMARY KEY(cust_id)
)ENGINE=InnoDB;
```
**注意：** 创建新表时，指定的表明必须不存在，否则将出错。如果要防止意外覆盖已有的表，SQL要求首先手工删除该表。

使用NULL值：
NULL值就是没有值或者缺值。允许NULL值的列也允许在插入行是不给出该列的值。

主键再介绍：
主键值必须唯一。表中的每个列必须具有唯一的主键值。如果主键使用单个列，则它的值必须唯一。如果使用多个列，则这些列的组合值必须唯一。
下面给出一个由多个列组成的主键，应该以逗号分配的列表给出各列名：
```
CREATE TABLE orderitems
(
 order_num  int      NOT NULL,
 order_item int      NOT NULL,
 prod_id    char(10) NOT NULL,
 quantity   int      NOT NULL,
 item_price decimal(8,2)  NOT NULL,
 PRIMARY KEY(order_num, order_item)
) ENGINE=InnoDB;
```
引擎类型：
以下是几个需要知道的引擎：
 - InnoDB 是一个可靠的事务处理引擎，它不支持全文本搜索
 - MEMORY 在功能等同于MyISAM，但由于数据存储在内存中，速度很快，适合于临时表
 - MyISAM是一个性能极高的引擎，它支持全文本搜索

引擎类型可以混用，但是外键不能跨引擎！即使用一个引擎的表不能引用具有使用不同引擎的表的外键。

#### 更新表
为了更新表定义，可以使用ALTER TABLE语句。理想状态下，当表中存储数据后，该表就不应该再被更新。在表的设计过程中需要花费大量时间来考虑，以便后期不对该表进行大的改动。
为了使用ALTER TABLE更改，必须给出下面的信息：
 - 在ALTER TABLE 之后给出要更改的表名
 - 所做更改的列表

在表中增加一列：
```
 ALTER TABLE vendors ADD vend_phone CHAR(20);
```
在表中删除一列：
```
ALTER TABLE vendors DROP COLUMN vend_phone;
```
ALTER TABLE常见的用途是定义外键。下面是 用来定义本书中的表所用的外键的代码：
```
ALTER TABLE orderitems ADD CONSTANT fk_orderitems_orders FOREIGN KEY (order_num) REFERENCES orders (order_num);
```
```
ALTER TABLE orderitems ADD CONSTANT fk_orderitems_products FOREIGN KEY (prod_id) REFERENCE products(prod_id);
```

#### 删除表
```
DROP TABLE customers;
```

#### 重命名表
```
RENAME TABLE customers TO customers2;
```
```
RENAME TABLE customers TO customers2, backvendor TO backup2;            
```

### Chapter 22
使用视图
视图是虚拟的表。与包含数据的表不一样，视图只包含使用时动态检索数据的查询。
下面给出一个帮助理解视图的例子：
```
SELECT cust_name, cust_contact FROM customers, orders, orderitems WHERE customers.cust_id = orders.cust_id AND orderitems.order_num = orders.order_num AND prod_id = 'TNT2';
```
此查询用来检索订购了某个特定产品的客户。任何需要这个数据的人都必须理解相关表的结构。并且知道如何创建查询和对表进行联结。
现在假如可以把整个查询包装为一个名为productcustomers的虚拟表，则可以如下轻松地检索出相同的数据：
```
SELECT cust_name,cust_contact FROM productcustomers WHERE prod_id = 'TNT2';
```
这就是视图的作用！

为什么使用视图
刚刚我们举了一个视图的例子，下面是视图的一些常见应用
 - 重用SQL语句
 - 简化复杂的SQL操作
 - 使用表的组成部分而不是整个表
 - 保护数据，可以给用户授予表的特定部分的访问权限而不是整个表的访问权限
 - 更改数据格式和表示。

视图不包含数据，所以每次使用视图时，都必须处理查询执行时所需要的任一个检索。

视图的规则和限制：
 - 与表一样，视图必须唯一命名
 - 对于可以创建的视图数目没有限制
 - 为了创建视图，必须具有足够的访问权限。这些权限通常由数据库管理人员授予
 - 视图可以嵌套，即可以利用从其他视图中检索数据的查询来构造一个视图
 - ORDER BY可以用在视图中，但是如果从该视图检索数据的SELECT语句中也包含ORDER BY，那么该视图中的ORDER BY 将被覆盖。
 - 视图不能索引，也不能有关联的触发器或默认值
 - 视图可以和表一起使用，例如，编写一条联结表和视图的SELECT语句。

#### 使用视图
在理解什么是视图后，我们来看一下视图的创建。
 - 视图使用CREATE VIEW来创建
 - 使用 SHOW CREATE VIEW viewname; 来查看创建视图的语句
 - 使用DROP删除视图，其语法为DROP VIEW viewname
 - 更新视图时，可以先用DROP再用CREATE，也可以直接用CREATE OR REPLACE VIEW。

使用视图简化复杂的联结：
视图的最常见的应用之一是隐藏复杂的SQL，这通常会涉及联结。
```
CREATE VIEW productcustomers AS SELECT cust_name, cust_contact, prod_id FROM customers, orders, orderitems WHERE customers.cust_id = orders.cust_id AND orderitems.order_num = orders.order_num;
```
列出了订购任意产品的用户。
如果要检索订购了产品TNT2的用户，可用如下命令：
```
SELECT cust_name, cust_contact FROM productcustomers WHERE prob_id = 'TNT2';
```

用视图重新格式化检索出的数据：
```
SELECT Concat(RTrim(vend_name), '(', RTrim(vend_country), ')') AS vend_title FROM vendors ORDER BY vend_name;
```
将上面的语句用视图来代替：
```
CREATE VIEW vendorlocations AS SELECT Concat(RTrim(vend_name), '(', RTrim(vend_country), ')') AS vend_title FROM vendors ORDER BY vend_name;
```
这条语句使用与以前的SELECT相同的查询创建视图。简化了编写SQL语句。

使用视图和计算字段
```
SELECT VIEW orderitemsexpanded AS SELECT order_num, prod_id, quantity, item_price, quantity*item_price AS expanded_price FROM orderitems;
```

更新视图：
通常视图是可以更新的。更新一个视图，将更新其基表，因为视图本身没有数据。如果对视图增加或删除行，实际上是对基表增加或删除行。
但是并非所有的视图都是可更新的。基本上可以说，如果MySQL不能正确地确定被更新的基数据，则并允许更新。所以如果视图定义中有以下操作，则不能进行视图的更新：
 - 分组(GROUP BY 和 HAVING)
 - 联结
 - 子查询
 - 并
 - 聚集函数
 - DISTINCT
 - 导出（计算）列

**视图主要用于数据的检索**

### Chapter 23
使用存储过程
#### 存储过程
迄今为止，使用的大多数SQL语句都是针对一个或多个表的单条语句。并非所有操作都这么简单，经常会有一个完整的操作需要多条语句才能完成。考虑以下的情形：
 - 为了处理订单，需要核对以保证库存中有相应的物品
 - 如果库存有物品，这些物品需要预定以便不将它们再卖给别人，并且要减少可用的物品数量以反映正确的库存量
 - 关于哪些物品入库和哪些物品退订，需要通知相应的客户

执行这些处理往往需要多条SQL语句。这多条SQL可以创建存储过程，存储过程简单来说，就是为以后的使用而保存的一条或多条MySQL语句的集合。可将其视为批处理文件，但是它们的作用不限于批处理。

#### 为什么要使用存储过程
为什么使用存储过程，下面给出几个理由：
 - 通过把处理封装在易用的单元中，简化复杂的操作
 - 由于不要求反复建立一系列处理步骤，保证了数据的完整性，减低粗心的错误
 - 简化对表动的管理，如果表名、列名或者业务逻辑有变化，只需要更改存储过程的代码。使用他的人员甚至不需要知道这些变化。封装的好处！

#### 使用存储过程
执行存储过程
```
CALL productpricing(@pricelow, @pricehigh, @priceaverage);
```
其中执行名为productpricing的存储过程，它计算并返回产品的最低，最高和平均价格。

创建存储过程
正如所述，编写存储过程并不是微不足道的事情。需要对数据库的管理有很好的认识。
```
CREATE PROCEDURE productpricing() BEGIN SELECT AVG(prod_price) AS priceaverage FROM products; END;
```
使用该存储过程：
```
CALL productpricing();
```

删除存储过程
```
DROP PROCEDURE (IF EXISTS 可选) productpricing;
```
创建含参数的存储过程：
```
CREATE PROCEDURE productpricing(OUT p1 DECIMAL(8,2) OUT ph DECIMAL(8,2) OUT pa DECIMAL(8,2) BEGIN SELECT Min(prod_price) INTO pl FROM products; SELECT Max(prod_price) INTO ph FROM products; SELECT AVG(prod_price) INTO pa FROM products); END; 
```
调用含参数的存储过程：
```
CALL productpricing(@pricelow, @pricehigh, @priceaverage);
```
**所有MySQL变量都必须以@开始**
为了获得产品平均价格使用如下命令：
```
SELECT @priceaverage;
```
获得产品的最高价格，最低价格，平均价格：
```
SELECT @pricehigh, @pricelow, @priceaverage;
```
使用IN，OUT参数：
```
CREATE PROCEDURE ordertotal(IN onumber INT, OUT ototal DECIMAL(8,2))) BEGIN SELECT Sum(item_price * quantity) FROM orderitems WHERE ordernum = onumber INTO ototal; END;
```
调用该存储过程：
```
CALL ordertotal(20005, @total);
```

### Chapter 24
使用游标
MySQL检索操作反馈一组称为结果集的行。这组返回的行嗾使与SQL语句相匹配的行。使用简单的SELECT语句没法得到第一行，下一行或前10行，也不存在一个批处理它们的方法。有时需要在检索出的结果行中前进一行或者后退一行到多行。这就是使用游标的原因。游标是一个存储在MySQL服务器上的数据库查询，它不是一条SELECT语句，而是被该语句检索出来的结果集。
**游标只能用于存储过程**

使用游标涉及几个明确的步骤：
 - 在能够使用游标前，必须声明它。这个过程实际上没有检索数据，它只是定义要使用的SELECT语句
 - 一旦声明后，必须打开游标以供使用。这个过程用前面定义的SELECT语句把数据检索出来
 - 对于填有数据的游标，根据需要取出各行
 - 在结束游标使用时，必须关闭游标

创建游标：
```sql
CREATE PROCEDURE processorders() BEGIN DECLARE ordernumbers CURSOR FOR SELECT order_num FROM orders; END;
```
DECLARE语句用来创建游标。上面的例子定义了名为ordernumbers的游标。
打开或关闭游标：
```sql
OPEN ordernumbers;
CLOSE ordernumbers;
```
使用游标数据：
在一个游标被打开后，可以使用FETCH语句访问它的每一行。FETCH指定检索什么数据，检索出来的数据存储在什么地方。
```sql
CREATE procedure processorders() 
BEGIN 
DECLARE done BOOLEN DEFAULT 0;
DECLARE o INT;
DECLARE ordernumbers CURSOR FOR SELECT order_num FROM orders;
DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done  = 1;
OPEN ordernumbers;
REPEAT
  FETCH ordernumbers INTO o;
UNTIL done END REPEAT;
CLOSE ordernumbers;
```
need an example to exercise!

### Chapter 25
使用触发器：
MySQL语句在需要时被执行，存储过程也是如此。但是如果你想要某条语句在事件发生时自动执行，就需要使用触发器了。比如：
 - 每当增加一个顾客到某数据库表时，都检查其电话号码格式是否正确，州的缩写是否为大写
 - 每当订购一个产品时，都从库存数量中减去订购的数量
 - 无论何时删除一行，都在某个存档表中保留一个副本

所有的这些例子的共同之处在于：当一个表被修改时，都需要设计到其他表的修改。这确切的说，就是一个触发器，trigger。MySQL支持对INSERT,UPDATE,DELETE命令的出发，其他不支持。



