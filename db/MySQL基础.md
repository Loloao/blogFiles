# Mysql基础
数据库有以下特点
1. 持久化存储数据，其实数据库就是一个文件系统
2. 方便存储和管理数据
3. 使用了统一方式操作数据库 -- SQL

## 服务

### 启动和退出
- `mysql`
  - `-u<账户>`：输入账户
  - `-p<密码>`：输入密码
  - `-h<ip>`：进入

### 目录结构
每个数据库服务器会有多个数据库，而一个数据库会有多个表，而表中会有多个数据记录
每个数据库其实就是一个文件夹，在安装好之后会有三个数据库分别对应三个文件夹
- `mysql`：核心数据库，放置有很多表
- `performance_schema`：对性能提升做一些操作
- `test`：空的数据库，用于测试
还有一个没有文件夹的数据库
- `infomation_schema`：用来描述`mysql`里的信息，包括表，库的信息
表对应的就是数据库文件夹里的文件

### SQL(Struct Query Language) 结构化查询语言
`SQL`其实就是定义了操作所有关系型数据库的规则，但每种数据库操作的方式存在不一样的地方
通用语法
- 以单行或多行书写，以分号结尾
- 可以使用空格和缩进来增强语句的可读性
- Mysql 数据库的`SQL`语句不区分大小写，关键字建议使用大写
三种注释
- `-- `或`# `单行注释
- `/* */`多行注释
`SQL`分类
- `DDL(Data Defination Language)`：数据定义语言，用于定义数据库对象：数据库、表、列等，关键字`create`，`drop`，`alter`等
- `DML(Data Manipulation Language)`：数据操作语言，用于对数据库中表的数据进行增删改，关键字`insert`，`delete`，`update`等
- `DQL(Data Query Language)`：用来查询数据库中表的表的记录(数据)，关键字`select`，`where`等
- `DCL(Data Control Language)`：数据控制语言，用于授权
数据库`CRUD`
- `Create`：创建
  - `create database <库名>`：用于创建一个数据库
  - `create database if not exists <库名>`：只有当要创建的数据库不存在时创建一个数据库
  - `create database <库名> character set <字符集>`：只有当要创建的数据库不存在时创建一个数据库
- `Retrive`：查询
  - `show database`：查询所有数据库名称
  - `show create datebase <库名>`：查询创建某个数据库的创建语句，同时可以查看字符集
- `Update`：更新
  - `alter database <库名> charater set <字符集>`
- `Delete`：删除
  - `drop database <库名>`
  - `drop database if exists <库名>`：只有当要创建的数据库不存在时创建一个数据库
使用数据库
- `select database()`：查询正在使用的数据库名称
- `use <库名>`：使用数据库
表`CRUD`：
- `Create`：创建
  - `create table <表名>(列名 数据类型,)`：创建表，数据库类型有以下几种
    - `int`：整数类型
    - `double(<位数>,<小数点后几位>)`：小数类型
    - `date`：只包含年月日的日期`yyyy-MM-dd`
    - `datetime`：日期，包含年月日时分秒`yyyy-MM-dd HH:mm:ss`
    - `timestamp`：时间戳类型，包含年月日时分秒`yyyy-MM-dd HH:mm:ss`，如果将来不给这个字段赋值，则默认使用当前的系统时间，来自动赋值
    - `varchar(最大多少字符)`：字符串类型
  - `create table <表名> like <被复制的表名>`：用于复制表
- `Retrive`：查询
  - `show tables`：查询某个数据库中的表
  - `desc <表名>`：展示表结构
  - `show create table <表名>`：查询表信息 
- `Update`：更新
  - `alter table <表名> add <列名> <类型>`：添加一列
  - `alter table <表名> drop <列名>`：删除某列
  - `alter table <表名> rename to <新表名>`：修改表名
  - `alter table <表名> charater set utf8`：修改表名
  - `alter table <表名> change <列名> <新列名> <类型>`：修改列名和类型
  - `alter table <表名> modify <列名> <类型>`：修改类型
- `Delete`：删除
  - `drop table <表名> if exists`：删除某个表
  - `truncate table <表名>`：也是删除表，然后再创建一个一模一样的空表 

`DML`操作表中数据
- 添加数据
  - `insert into <表名>(<列1>,<列2>...) values(<值1>,<值2>...)`
  注意
  - 列名和表名要一一对应 
  - 如果表名后不定义列名，则默认给所有列添加值
  - 除了数字类型，其他类型需要使用引号 
- 删除数据
  - `delete from <表名> [where <条件>]`
  注意
  - 如果没加条件则删除表中所有数据，并且有多少条数据就会执行多少条删除的操作，不推荐使用，最好使用`truncate`
- 修改数据
  - `update <表名> set <列名1 = 值1>,<列名2 = 值2>...[where <条件>]`
  注意
  - 如果不加任何条件，则会将表中所有数据全部修改

`DQL`查询语句
- 排序查询
  - `order by <排序字段1 排序方式1>, <排序字段2 排序方式2>`，如果第一种排序方式的值一致就按第二种排序方式排序，默认为`ASC(升序)`，还有`DESC(降序)`。
- 聚合函数：将一列数据作为一个整体，进行纵向的计算，`select <函数名>(<列名>) from <表名>`，聚合函数的计算会排除`null`值
  如果想把`null`纳入计算，需要加入`ifnull(<列名, 0)`把`null`替换为`0`
  - `count`：计算个数，一般选择非空的列进行计算，比如主键，或是`count(*)`计算所有列，但不推荐`*`
  - `max`：计算最大值
  - `min`：计算最小值
  - `sum`：求和
  - `avg`：计算平均值
  可以给聚合函数加别名，在语句其他地方可以使用这个别名代替聚合函数`<函数名>(<列名>) <别名>`
- 分组查询：`group by <分组字段>`
  注意：分组之后查询的字段：分组字段、聚合函数
  - `select <分组字段>, <聚合函数1>, <聚合函数2>... from <表名> group by <分组字段>`，其中`select`后的为展示的列字段
  - 可以设定参与分组的条件比如`where <条件> group by <分组字段>`
  - 可以设定分组之后的条件比如`group by <分组字段> having <条件>`
  `where`和`having`的区别
  - `where`在分组之前进行限定，如果不满足条件不参与分组，`having`在分组之后进行限定，不满足条件不会被查询
  - `where`后不可以跟聚合函数，`having`可以进行聚合函数的判断
- 分页查询：`limit <开始的索引>, <每页条数>`
  - `select * from limit 0,3`：从`0`开始查，查`3`条数据
- 基础查询
  - `select * from <表名>`：查询表中数据
  - `select distinct <字段名> from <表名>`：主去除重复的结果集，要是加上`distinct`这个字段，如果写了多个字段则多个字段必须完全一样才会看作一样
  - `select <字段1 + 字段2> from <表名>`：计算列，一般只进行数值型的就按比如可以通过`+`来加上一个字段表示多个字段值相加后的值，如果有`null`参与了计算则值也为`null`。另外也可以给字段起别名，在字段后加个空格即可
  
- 条件查询
  - `where`后跟条件语句
  - 运算符
    - 比较运算符`> < <= >= = <>`：`<>`表示不等于，也可以使用`!=`，`null`不能使用`=`或是`!=`来判断
    - `between...and`：在一个范围之内比如`between 100 and 200`
    - `in(集合)`：集合表示多个值，使用逗号分隔
    - `like ''`：模糊查询
      - 占位符：
        - `_`：任意单个字符
        - `%`：任意多个字符
    - `is null | is not null`：是否为`null`
    - `and | &&`：与，建议使用前者，`&&`并不通用
    - `or | ||`：或
    - `not | !`：非
