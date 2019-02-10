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

