# mysql 主键INT 与 varchar 比较

- 创建两个表格，一张 用int 一张用 varchar

```sql
create table person_int(id int primary key auto_increment,name varchar(10),sex char(1));
create table person_uuid(id varchar(36) primary key ,name varchar(10),sex char(1));
```



- 两张表分别插入1000 条数据

  ```sql
  DROP PROCEDURE IF EXISTS person_add_int;
  DELIMITER $
  CREATE PROCEDURE person_add_int()
  BEGIN
      DECLARE i INT DEFAULT 1;
      WHILE i<=1000 DO
          insert into person_int  (name,sex) select '张三','1' ;
          SET i = i+1;
      END WHILE;
  END $
  
  CALL person_add_int()
  
  ```

  ![1559185508894](assets/1559185508894.png)

  耗时 56.59s

   ```sql
  DROP PROCEDURE IF EXISTS person_add_uuid;
  DELIMITER $
  CREATE PROCEDURE person_add_uuid()
  BEGIN
      DECLARE i INT DEFAULT 1;
      WHILE i<=1000 DO
         insert into person_uuid select uuid(),'张三','1' ;
          SET i = i+1;
      END WHILE;
  END $
  
  CALL person_add_uuid()
  
   ```

  ![1559185617038](assets/1559185617038.png)

  耗时62.207s



相同数据的1000条记录数据容量使用情况

![1559185791871](assets/1559185791871.png)



- 添加索引 索引类型 `unique` 索引方法`btree`

  - int类型

    ![1559185909774](assets/1559185909774.png)

  - varchar类型

    ![1559185928959](assets/1559185928959.png)



进行select count() 查询

![1559186114173](assets/1559186114173.png)