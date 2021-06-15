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
- `mysql`
- `performance_schema`
- `test`
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



