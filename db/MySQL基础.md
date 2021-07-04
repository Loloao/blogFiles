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
 
- 约束：对表中的数据进行限定，保证数据的正确性，有效性和完整性。分类：
  - 主键约束`primary key`，非空且唯一，一张表只有一个字段为主键，主键就是表中记录的唯一标识。如果某一列是数值类型，使用`auto_increment`可以完成值自动增长
    删除主键约束`alter table <表名> drop primary key`
  - 非空约束`not null`，值不能为`null`
  - 唯一约束`unique`，值不能重复。多个`null`不算重复
  - 外键约束`foreign key`，就是让本表的外键和其他表的被唯一约束的列联系起来`constraint <外键名> foreign key (<外键列名>) references <主表名>(<主表列名>)`
    删除外键约束`alter table <表名> drop foreign key <外键名>`
    添加外键约束`alter table <表名> add constraint <外键名> foreign key (<外键列名>) references <主表名>(<主表列名>)`
    级联添加`on update cascader`
    级联删除`on delete cascader`
  可以在创建表的时候进行约束，比如`name varchar(20) not null`，或是创建表之后添加约束`alter table <表名> modify <列名> <列类型> not null`
 
`DCL`管理用户、授权
一般来讲会有`DBA(数据库管理员)`来对数据库进行管理
当 mysql 安装完成之后会有一个 mysql 的文件夹，里面会有一个`user`表用于存放用户信息，所以操作用户其实就是操作这张表，我们首先要
1. 切换到 mysql 数据库`use mysql;`
2. 查询`user`表`select * from user;`，`%`表示可以在任意主机使用用户登录数据库
- 管理用户
  - `create user <用户名>@<主机名> identified by <密码>`：创建用户
  - `drop user <用户名>@<主机名>`：删除用户
- 设置密码
  - `set password for <用户名>@<主机名> = password(<新密码>)`：更改用户密码
  - 如果忘了`root`用户的密码怎么办？
    1. `net stop mysql`：停止 mysql 服务，需要管理员权限
    2. `mysql --skip-grant-tables`： 使用无验证方式启动 mysql，同时必须在`mysql`所在的物理主机上才可以操作
    3. `update user set password = password('root') where user = 'root'`：更新`root`密码
    4. `net start mysql`：启动 mysql 服务
- 权限配置
  - `show grants for <用户名>@<主机名>;`：查询权限
  - `grant <权限列表> on <数据库名>.<表名> to <用户名>@<主机名>;`：授予权限，权限列表可以有`select, delete, update`，`*.*`表示所有权限
  - `revoke <权限列表> on <数据库名>.<表名> to <用户名>@<主机名>;`：撤销权限

## 数据库设计
表的设计直接影响到项目开发的难易程度，也影响到项目性能
多表之间的关系
1. 一对一：比如人和身份证，在任意一方添加外键指向另一方并且这个外键唯一
2. 一对多(多对一)：部门和员工，在多的一方建立外键指向一的一方的主键
3. 多对多：学生和课程的关系，多对多关系实现需要借助第三张中间表的，中间表需要包含两个字段，作为外键分别指向两种表的主键。中间表的主键可以是两个表的联合主键`primary key(<表一主键，表二主键...>)`
数据库范式就是设计数据库时需要遵循的规范，各种范式呈递增规范，越高的范式数据库冗余越小，一般遵循前三种范式就够了，要遵循后面的范式需要先遵循前面的范式
首先有些概念必须了解
  - 函数依赖：如果通过a属性或是属性组的值可以确定唯一b属性的值，则称b依赖于a，比如学号被姓名依赖
  - 完全函数依赖：如果a是一个属性组，b属性的确定需要依赖于a属性组中所有属性的确定，比如学号和课程确定分数
  - 部分函数依赖：属性组中的一个属性可以确定另一个属性的值
  - 传递函数依赖：通过a属性(属性组)的值可以确定b属性的值，再通过b属性(属性组)的值可以确定c属性的值，则称c传递函数依赖于a，比如学号被系名依赖，系名被系主任依赖
  - 码：如果在一张表中，一个属性或属性组被其他所有属性完全依赖，则称这个属性或属性组为该表的码
    - 主属性：码属性组中的属性
    - 非主属性：非码属性组中的属性
1. 第一范式`1NF`：每一列都是不可分割的原子数据项
2. 第二范式`2NF`：非码属性必须完全依赖于候选码，在`1NF`基础上消除非主属性对主码的部分函数依赖
3. 第三范式`3NF`：在`2NF`基础上，任何非主属性不依赖于其他非主属性(在`2NF`基础上消除传递依赖)

## 数据库的备份和还原
- `mysqldump -u<用户名> -p<密码> > <保存的路径>`：备份
- 如果还原，需要登录->创建数据库->使用数据库->`source <文件路径>`

## 多表查询
`select * from <表1>, <表2>`：返回两张表的笛卡尔积，我们要做的就是消除无用数据，这种做法有以下几种
- 内连接查询：当数据没有另一个表的对应信息时，不会查询出来，比如外键值为`null`，所以它只查询交集部分
  - 隐式内连接：使用`where`条件消除无用数据，比如`select <表1>.<字段>, <表2>.<字段>... from <表1>, <表2> where <表1>.dept_id = <表2>.id`
  `from`后可以设定表的别名，使用空格隔开
  - 显式内连接：`select <字段列表> from <表1> inner? join <表2> on <条件>`
- 外连接查询：查询的是左表所有数据以及其交集部分
  - 左外连接：`select <字段列表> from <表1> left outer? join <表2> on <条件>`，当第一个表数据没有另一个表的对应信息时，还是会查询出来
  - 右外连接：`select <字段列表> from <表1> right outer? join <表2> on <条件>`，左外连接反过来而已
- 子查询：查询中嵌套查询，称嵌套查询为子查询，也就是使用一个查询语句表示条件，比如`select * from emp where emp.salary = (select max(salary) from emp)`
  - 单行单列：子查询可以作为条件，使用运算符`> >= < <= =`判断
  - 多行单列：可以使用运算符`in`来判断
  - 多行多列：可将查询语句作为表来使用，`select <字段列表> from <表1>, <查询语句> where <条件>`
      
## 事务
如果一个包含多个步骤的业务操作，被事务管理，这些操作要不同时成功要不同时失败。如果操作失败就回滚，操作如下
1. 开启事务：`start transaction`
2. 回滚：`rollback`
3. 提交：`commit`
比如，张三给李四转500
```sql
# 开启事务
start transaction;
update account set balance = balance - 500 where name = 'zhangsan';
update account set balance = balance + 500 where name = 'lisi'; 
# 如果没问题提交数据
commit;
# 发现出问题了，回滚事务，回滚到启动事务之前
rollback;
```
在Mysql中事务默认自动提交，一条`DML(赠删改)`会自动提交一次事务，而Oracle数据库默认手动提交
- `select @@autocommit;`：如果值为`1`表示默认提交方式为自动提交，`0`为手动提交
- `set @@autocommit = 0;`：用于设定事务提交方式，可以将默认自动提交的事务改为手动提交才会生效

### 事务的四大特征
1. 原子性：是不可分割的最小操作单位，要么同时成功，要么同时失败
2. 持久性：如果事务一旦提交或回滚后，数据会持久化地保存数据
3. 隔离性：多个事物之间，相互独立
4. 一致性：事务操作前后，数据总量不变

### 事务的隔离级别
多个事物之间相互独立的，但是如果多个事务操作同一批数据，则会引发一些问题，设置不同的隔离级别就可以解决这些问题
存在问题
- 脏读：一个事务读取到另一个事务没有提交的数据
- 不可重复读(虚读)：在同一个事务中，两次读取到的数据不一样
- 幻读：一个事务(DML)数据表中所有记录，另一个事务添加了一一条数据，则第一个事务查询不到自己的修改
隔离级别
- `read uncommitted`：读未提交，产生的问题：脏读、不可重复读、幻读，同一个事务只要更改了数据就会发生变化
- `read committed`：读已提交，产生的问题：不可重复读、幻读，orcale 默认为这种，同一个事务只要有一个地方`commit`数据就会发生变化
- `repeated read`：可重复读，产生的问题：mysql 默认，幻读，也就是同一个事务只有都`commit`之后读取的数据才会发生变化
- `serializable`：串行化：可以解决所有问题，当一个事务在处理一张表时，这个表，会变为不可操作，任何操作会进入无限等待状态，当`commit`之后才会有结果
问题表现
- 脏读：事务读取到未`commit`的数据
- 不可重复读：也就是两次读取的数据不一样
注意：隔离级别从小到大安全性越来越高，但效率越来越低
- `set global transaction isolation level <级别字符串>`：数据库设置隔离级别
- `select @@tx_isolation`：查询隔离级别

## JDBC(Java Database Connectivity)
`JDBC`为Java操作数据库语言，本质是希望使用一套Java代码可以操作所有的关系型数据库，`JDBC`定义了操作所有关系型数据库的规则(接口)，各个数据库厂商去实现这套接口，提供数据库驱动`jar`包，我们可以使用这套`JDBC`编程，真正执行的是驱动`jar`包中的类

### 如何使用`JDBC`
1. 导入驱动`jar`包，先复制`jar`包到`libs`目录下，然后右键`Add As Library`
2. 注册驱动`Class.forName('com.mysql.jdbc.Driver')`
3. 获取数据库连接对象`Connection`，`Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db3", "root", "password")`
4. 定义`sql`：`String sql = "update account set balance = 500 where id = 1"`
5. 获取执行`sql`语句的对象`Statement`：`Statement stmt = conn.createStatement();`
6. 执行`sql`，接受返回结果：`int count = stmt.executeUpdate(sql)`
7. 处理结果：`System.out.println(count)`
8. 释放资源：`stmt.close();conn.close();`

### 各个对象
- `DriverManager`：驱动管理对象，功能有
  1. `static void registerDriver(Driver diver)`：注册驱动，而`Class.forName('com.mysql.jdbc.Driver')`中有静态代码块会自动执行注册操作，但其实也可以不进行注册，在`jar`包中的`META-INF`的`services`中的`java.sql.Driver`中就有启动语句
  2. `static Connection getConnection(String url, String user, String password)`，参数
    - `url`：指定连接的路径，`jdbc:mysql://ip地址(域名):端口号/数据库名称`
    - `user`：用户名
    - `password`：密码
- `Connection`：数据库连接对象，功能
  1. 获取执行`sql`的对象
    - `Statement createStatement()`
    - `PreparedStatement prepareStatement(string sql)`
  2. 管理事务
    - `void setAutoCommit(boolean autoCommit)`：调用该方法设置参数为`false`，即开始事务
    - `commit()`：提交事务
    - `rollback()`：回滚事务
- `Statement`：执行`sql`的对象，用于执行静态`sql`并返回其生成的结果的对象，易受到 sql 注入攻击
  - `boolean execute(String sql)`：执行给定的`sql`语句，可能返回多个结果，`true`如果第一个结果是一个`ResultSet`对象；`false`如果是更新计数或没有结果
  - `int executeUpdate(String sql)`：执行`DML(insert, update, delete)`语句、`DDL(create, alter, drop)`语句，返回值为影响的行数，可以通过影响的行数判断`DML`语句是否执行成功，返回值 > 0 的则执行成功，反之失败
  - `ResultSet executeQuery(String sql)`：执行`DQL(select)`语句
- `ResultSet`：结果集对象，用于封装查询结果，可以通过一个游标一个一个指向下一条数据，和构造器类似
  - `next()`：游标向下移动一行，判断是否是最后一行，如果是返回`false`，不是则返回`true`
  - `get<数据类型>(<列编号 | 列名>)`：获取数据，比如`int getInt()`
  使用步骤
  - 游标向下移动
  - 判断是否有数据
  - 获取数据
  ```java
  while(rs.next()) {
    int id = rs.getInt(1);
  }
  ```
- `PreparedStatement`：也是执行`sql`对象但是功能更加强大，执行预编译`sql`，可防止`sql`注入，同时效率更高。参数使用`?`作为占位符
  - `PreparedStatement Connection.preparedStatement(String sql)`：`sql`语句使用`?`作为占位符，比如`select * from user where username = ? and password = ?`
  - `set<类型>(<?的位置>, <?的值>)`：用于给占位符赋值

### 事务
事务为一个包含多个步骤的业务操作。如果这个业务操作被事务管理，则多个步骤要么同时成功，要么同时失败
- `setAutoCommit(boolean autocommit)`：调用该方法设置参数为`false`，即开始事务，在执行`sql`之前开启
- `commit()`：提交事务，当所有`sql`执行完后提交事务
- `rollback()`：回滚事务，在`catch`中进行事务的回滚
