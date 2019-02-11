# Hibernate

## 配置

- 依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.huifer</groupId>
    <artifactId>hibernateBook</artifactId>
    <version>1.0-SNAPSHOT</version>






    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <hibernate.version>5.3.7.Final</hibernate.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>${hibernate.version}</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.13</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.25</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.25</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.4</version>
        </dependency>
    </dependencies>

</project>
```



- Hibernate 核心配置文件

  hibernate.cfg.xml 

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">


<hibernate-configuration>
    <session-factory>
        <property name="hibernate.connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/dy_java?serverTimezone=UTC&amp;rewriteBatchedStatements=true&amp;useUnicode=true&amp;characterEncoding=utf8
        </property>
        <property name="hibernate.connection.username">root</property>
        <property name="hibernate.connection.password">root</property>
        <!--方言-->
        <property name="hibernate.dialect">org.hibernate.dialect.MySQL8Dialect</property>
        <property name="hibernate.show_sql">true</property>
        <property name="hibernate.format_sql">true</property>

        <!--映射文件-->
        <mapping resource="Customer.hbm.xml.tld"/>

        
    </session-factory>
</hibernate-configuration>
```


## 实例
- Customer 实体
```java
package com.huifer.hibernatebook.bean;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;

/**
 * 描述:
 * 客户表
 *
 * @author huifer
 * @date 2019-02-10
 */
@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString
public class Customer {
    /**
     * 客户编号(主键)
     */
    private Long cust_id;
    /**
     * 客户名称(公司名称)
     */
    private String cust_name;
    /**
     * 客户信息来源
     */
    private String cust_source;
    /**
     * 客户所属行业
     */
    private String cust_industry;
    /**
     * 客户级别
     */
    private String cust_level;
    /**
     * 固定电话
     */
    private String cust_phone;
    /**
     * 移动电话
     */
    private String cust_mobile;
}
```

- Customer.hbm.xml.tld
```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">


<hibernate-mapping>

    <!--类与表的映射-->
    <class name="com.huifer.hibernatebook.bean.Customer" table="cst_customer">
        <!--主键对应-->
        <id name="cust_id" column="cust_id">
        <!--生成策略-->
            <generator class="native"/>
        </id>
    <!--其他字段属性对应-->

        <property name="cust_name" column=""/>
        <property name="cust_source" column=""/>
        <property name="cust_industry" column=""/>
        <property name="cust_level" column=""/>
        <property name="cust_phone" column=""/>
        <property name="cust_mobile" column=""/>
    </class>




</hibernate-mapping>
```



- 测试保存

```java
package com.huifer.hibernatebook.bean;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;
import org.junit.Test;
import org.junit.runner.RunWith;

import static org.junit.Assert.*;

public class CustomerTest {


    @Test
    public void save() {
        // 加载 hibernate 配置
        Configuration cfg = new Configuration().configure();
        // sessionFactory 获取
        SessionFactory sessionFactory = cfg.buildSessionFactory();
        // sessionFactory 获取 Session 获取
        Session session = sessionFactory.openSession();
        // 手动事务开启
        Transaction transaction = session.beginTransaction();
        // 具体逻辑

        Customer customer = new Customer();
        customer.setCust_name("测试数据");


        session.save(customer);
        // 事物提交
        transaction.commit();
        // 资源释放
        session.close();
    }


}

```

## 映射配置

class标签

- name ： 类的全路径

- table ： 表名

- catalog : 数据库名



 id标签主键标签

- name ： 类中的属性
- column：表中的字段
- length：长度
- type：类型

property字段属性

- name：类中的属性
- column：表中的字段
- length：长度
- type：类型
- not-null：不为空
- unique：唯一

## class

### org.hibernate.cfg.Configuration

 Configuration hibernate核心配置文件

- hibernate.properties
  - 通过  new Configuration() 即可获取
- hibernate.cfg.xml  **常用**
  - 通过new Configuration().configure(); 获取

### org.hibernate.SessionFactory

 cfg.buildSessionFactory();获取一个项目只要一个即可

### org.hibernate.Session

类似jdbc Connection 连接对象 不是线程安全的 

- 保存方法 返回数据库 id

  - ``` java
    Serializable save(Object var1);
    ```

- 查询方法

  - ```java
    <T> T get(Class<T> var1, Serializable var2);
    
    ```

  - ```java
    <T> T load(Class<T> var1, Serializable var2);
    ```

    **get方法立即执行，直接发送，返回的是对象本身**

    **load方法是延时加载 ， 只有使用对象才发送，返回的是代理对象**

    区别列表

    | 行为           | get            | load                        |
    | -------------- | -------------- | --------------------------- |
    | 运行到当前代码 | 立即执行并发送 | 不发送，直到使用对象        |
    | 返回值         | 真实对象       | 代理对象                    |
    | 查询不存在值   | 返回null       | 返回ObjectNotFoundException |

- 修改方法

  - ```java
    void update(Object var1);
    ```

    **先查询在修改**

- 删除方法

  - ```java
    void delete(Object var1);
    ```

    **先查询在删除**

- 保存或更新

  - ```java
    void saveOrUpdate(Object var1);
    ```

- 查询

  - ```java
    org.hibernate.query.Query createQuery(String var1);
    ```

### org.hibernate.Transaction 

- commit() 提交
- rollback() 回滚 

## 提取工具

```java
package com.huifer.hibernatebook.utils;


import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

/**
 * 描述:
 * hibernate工具
 *
 * @author huifer
 * @date 2019-02-11
 */
public class HibernateUtils {

    public static final Configuration HIBERNATE_CFG;
    public static final SessionFactory HIBERNATE_SESSIONFACTORY;

    static
    {
        HIBERNATE_CFG = new Configuration().configure();
        HIBERNATE_SESSIONFACTORY = HIBERNATE_CFG.buildSessionFactory();
    }


    public static Session getHibernateSession(){
        return HIBERNATE_SESSIONFACTORY.openSession();
    }

}


```



- 测试

  ```java
    @Test
      public void hibernateUtilsTest() {
          Session hibernateSession = HibernateUtils.getHibernateSession();
          Transaction transaction = hibernateSession.beginTransaction();
          Customer customer = new Customer();
          customer.setCust_name("hibernateUtilsTest测试数据");
          hibernateSession.save(customer);
          transaction.commit();
          // 资源释放
          hibernateSession.close();
      }
  ```

  

## 持久化编写

- 将内存中的对象放到数据库称为持久化

### 编写规则

- **持久化类必须提供无参构造方法**
- **属性需要私有，对私有属性提供get&set方法**
- **对持久化类提供唯一标识与数据库主键对应**
- **持久化类中的属性使用包装类 Interge ， Double 等**
- **持久化类不能用 final 修饰**

### 主键

#### 自然主键

 - 实体类中的具体属性， 表中的具体字段

#### 代理主键

- 不是实体类中的具体属性，不是表中的具体字段
- **主要使用**
  - **原因： 自然主键参与业务需要修改源代码**

#### 主键生成策略

- increment 

  - hibernate 提供自增长

  - **用于为 long, short 或者 int 类型生成 唯一标识**。**只有在没有其他进程往同一张表中插入数据时才能使用。在集群下不要使用。**

- identity

  - **使用数据库的自增长机制**
  - 对 DB2，MySQL，MS SQL Server，Sybase 和 HypersonicSQL 的内置标识字段提供支持。**返回的标识符是 long，short 或者 int 类型的。**

- sequence

  - 在 DB2，PostgreSQL，Oracle，SAP DB，McKoi 中使用序列（sequence）， 而在 Interbase
    中使用生成器（generator）。**返回的标识符是 long，short 或者 int 类型的。**

- uuid

  - **用于字符串类型的主键**，使用hibernate 的字符串随机方式生成 

- native

  - **根据底层数据库的能力选择**

- assigned
  - 用户自己设置

---

###  持久化类状态

