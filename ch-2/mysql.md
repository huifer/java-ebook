# mysql

## sql分类

- 数据定义语言DDL， 用来定义数据库，表格，列
  - create,alter,drop
- 数据操作语言DML，用来对数据库中的表格进行更新
  - update,delete
- 数据库查询语言DQL，用来查询数据库表的记录
  - select ,where



## sql语句说明

### 数据库操作

- 创建数据库
  - create database 数据库名称 character set 字符集
- 查看数据库
  - 查看所有数据库
    - show databases
  - 查看指定数据库的定义信息
    - show create database 数据库名称
- 删除数据库
  - drop database 数据库名称

- 切换使用数据库
  - use 数据库名称

### 表操作

#### 字段类型

- **表格内容并非全部字段类型**

| 分类         | 类型名称   | 说明                |
| ------------ | ---------- | ------------------- |
| 整数类型     | tinyInt    |                     |
|              | smallint   |                     |
|              | mediumint  |                     |
|              | int        |                     |
| 小数类型     | float      | 单精度浮点数        |
|              | double     | 双精度浮点数        |
|              | decimal    | 金钱类型            |
| 日期类型     | year       | 年                  |
|              | time       | HH:MM:SS            |
|              | date       | YYYY-MM-DD          |
|              | datetime   | YYYY-MM-DD HH:MM:SS |
|              | timestamp  | UTC                 |
| 二进制、文本 | char(M)    | m[0,255]            |
|              | varchar(m) | m[0,65535]          |
|              | blob       | [0,65535]           |
|              | tinyblob   | [0,255]             |
|              | text       | 65535               |

- 约束
  - 唯一约束
    - unique
  - 主键约束
    - 唯一约束+非空约束的合集
    - primary key
  - 非空约束
    - not null
- 创建表
  - create table 表明(字段名 类型(长度) 约束) 单表约束
- 查看表
  - 查看所有表
    - show  tables
  - 查看表结构
    - desc 表名
- 删除表
  - drop table 表名
- 修改表
  - 添加列
    - alter table 表名	add 列名 类型(长度) 约束
  - 修改列名
    - alter table 表明 change 旧列名 新列名 类型(长度) 约束
  - 删除列
    - alter table 表名 drop 列名
- 插入数据
  - insert into 表名 (列名1，列名2....) values (值1 ,值2)