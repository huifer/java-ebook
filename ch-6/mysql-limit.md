# mysql limit 

## 测试数据创建

```sql
CREATE TABLE `at15`.`Untitled`  (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `age` int(11) NULL DEFAULT NULL,
  `content` text CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;
```



```
DROP PROCEDURE
IF
	EXISTS person_add_int;

DELIMITER $
CREATE PROCEDURE person_add_int ( ) BEGIN
	DECLARE
		i INT DEFAULT 1;
	WHILE
			i <= 100000 DO
			INSERT INTO t_test_limit
		VALUES
			( 0, uuid( ), 1, uuid( ) );
		
		SET i = i + 1;
		
	END WHILE;

END $ CALL person_add_int ( )
```



## 基本语法

```sql
SELECT * FROM table LIMIT [offset,] rows | rows OFFSET offset 
```

> LIMIT 接受一个或两个数字参数。参数必须是一个整数。如果给定两个参数，第一个参数指定第一个返回记录行的偏移量，第二个参数指定返回记录行的最大数目。



### 案例说明

```sql
SELECT * FROM table LIMIT 5,10; // 检索记录行 6 , 15
SELECT * FROM table LIMIT 5,-1; // 检索记录行 6 , 最后一条
SELECT * FROM table LIMIT 5; // 检索记录到前5行
```



## 效率

- LIMIT 是一种物理分页。

| 对比项目       | 物理分页                                                     | 逻辑分页                                                     |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 数据库访问次数 | 每次进行都需要查询                                           | 一次查询                                                     |
| 实时性         | 每次查询数据库内容，都能够从数据库中获取最新数据，**实时性强** | 一次查询数据放置再内存中，本地磁盘数据修改内存中数据不会及时修改，**实时性弱** |

- 本次测试数据量`14388` 



### 测试

#### 00x00

- `EXPLAIN select * from  t_test_limit LIMIT 100,3000;`

| id   | select_type | table        | partitions | type | possible_keys | key  | key_len | ref  | rows  | filtered | Extra |
| ---- | ----------- | ------------ | ---------- | ---- | ------------- | ---- | ------- | ---- | ----- | -------- | ----- |
| 1    | SIMPLE      | t_test_limit |            | ALL  |               |      |         |      | 14405 | 100      |       |

- type:
  - ALL:对于每个来自于先前的表的行组合,进行完整的表扫描。

- rows
  - 显示MySQL认为它执行查询时必须检查的行数。多行之间的数据相乘可以估算要处理的行数。



> 上述查询中是一个全表查询，提出第一次优化 使用主键id自增长来进行查询优化
>
> 改进：减少查询范围

#### 00x01

- `EXPLAIN select * from  t_test_limit WHERE t_test_limit.id >100  LIMIT 5000,3000;`

| id   | select_type | table        | partitions | type  | possible_keys | key     | key_len | ref  | rows | filtered | Extra       |
| ---- | ----------- | ------------ | ---------- | ----- | ------------- | ------- | ------- | ---- | ---- | -------- | ----------- |
| 1    | SIMPLE      | t_test_limit |            | range | PRIMARY       | PRIMARY | 4       |      | 7202 | 100      | Using where |

- type:
  - range:只检索给定范围的行,使用一个索引来选择行。

- rows
  - 显示MySQL认为它执行查询时必须检查的行数。多行之间的数据相乘可以估算要处理的行数。

> 上述查询并非全表查询，只查询了7202，查询数量少时间自然就变少了.但是存在下列缺陷
>
> - 自增长id,且知道查询区域(id的一个大概范围)
>
> 改进：增加索引

#### 00x02

- 给`name`增加索引

```sql
CREATE TABLE `at15`.`Untitled`  (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `age` int(11) NULL DEFAULT NULL,
  `content` text CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL,
  PRIMARY KEY (`id`) USING BTREE,
  UNIQUE INDEX `a`(`name`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;
```



- sql

  ```java
  EXPLAIN Select * From t_test_limit Where name in (Select id From t_test_limit where ( name like '%c2%' )) limit  5000,3000;
  EXPLAIN Select id From t_test_limit where ( name like '%c2%' )
  ```

  | id   | select_type | table        | partitions | type   | possible_keys | key     | key_len | ref                    | rows  | filtered | Extra       |
  | ---- | ----------- | ------------ | ---------- | ------ | ------------- | ------- | ------- | ---------------------- | ----- | -------- | ----------- |
  | 1    | SIMPLE      | t_test_limit |            | ALL    | a             |         |         |                        | 14405 | 100      | Using where |
  | 1    | SIMPLE      | t_test_limit |            | eq_ref | PRIMARY       | PRIMARY | 4       | at15.t_test_limit.name | 1     | 11.11    | Using where |

  | id   | select_type | table        | partitions | type  | possible_keys | key  | key_len | ref  | rows  | filtered | Extra                    |
  | ---- | ----------- | ------------ | ---------- | ----- | ------------- | ---- | ------- | ---- | ----- | -------- | ------------------------ |
  | 1    | SIMPLE      | t_test_limit |            | index |               | a    | 1023    |      | 14405 | 11.11    | Using where; Using index |

  - 子查询中使用索引进行查询没有实际的数据操作，不涉及IO操作进而改进查询时间

> 大数据量情况下in操作效率不好
>
> 改进: join 优化

#### 00x03

`select * from t_test_limit inner join ( select id from t_test_limit where (name like '%c2%' ) limit 5000,300) b using (id)`



| id   | select_type | table        | partitions | type   | possible_keys | key     | key_len | ref  | rows  | filtered | Extra                    |
| ---- | ----------- | ------------ | ---------- | ------ | ------------- | ------- | ------- | ---- | ----- | -------- | ------------------------ |
| 1    | PRIMARY     | `<derived2>` |            | ALL    |               |         |         |      | 1600  | 100      |                          |
| 1    | PRIMARY     | t_test_limit |            | eq_ref | PRIMARY       | PRIMARY | 4       | b.id | 1     | 100      |                          |
| 2    | DERIVED     | t_test_limit |            | index  |               | a       | 1023    |      | 14405 | 11.11    | Using where; Using index |

> 此时是一个最优解了嘛？
>
> - 相对而言如果每次需要经过一次全局查询或者说有多条件的查询这个可以是一个优化方案
> - 如果可以知道主键的范围这个必然不是一个优化的最优解



### 想法

- 一般的优化方式
  1. 加索引
  2. 控制搜索范围
     1. 使用多线程来分配N个limit查询语句，最终将这个整体合并返回









