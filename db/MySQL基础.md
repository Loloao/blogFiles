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


