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


----



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





## 持久化类状态

### 瞬时态

- 没有唯一标识，没有被session管理

### 持久态

- 持久态对象可以自动更新

- 有唯一标识，被session管理

### 脱管态

- 有唯一标识，没有被session管理

### 转换

- 瞬时态
  - new  获取
  - 转换成持久态
    - save() 方法
  - 转换成脱管态
    - 重设主键
- 持久态
  - get() 获取 ， 通过查询获取
  - 转换成瞬时态
    - delete()方法
  - 转换成脱管态
    - close(), clear() 方法
- 脱管态
  - new 一个新的对象在设置id
  - 转换成持久态
    - update()，saveOrUpdate() 方法
  - 转换成瞬时态
    - id设置null

### hibernate缓存

#### 一级缓存

- session级别
- 自动开启

##### 快照区域

-   org.hibernate.internal.SessionImpl
     执行get或者其他方法即可获取
-   session.close() 或 session.clear() 都会清空缓存

![hibernate一级缓存](/ch-2/pic/hibernate一级缓存.png)



### 事务

#### 特性

 - 原子性 ：事务不可分割
 - 一致性 ： 事务执行前后数据一致
 - 隔离性 ： 事务执行过程中不受其他事务y影响
 - 持久性 ： 事务结束后数据放在数据库中

#### 读问题

- 脏读 ： 一个事务读取到了另一个没有提交事务的数据
- 不可重复度：一个事务读到了另一个事务已经提交的update数据，导致前一个事务多次查询结果的不一致
- 虚读 ： 一个事务读到了另一个事务已经提交的insert数据 致前一个事务多次查询结果的不一致

###### 解决方法

- 设置隔离级别

  - read uncommitter ： 读问题都会发送
  - read committed ：解决脏读问题
  - repeatable read ：解决脏读+不可重复读
  - serializable : 解决所有读问题

- hibernate 设置

  在核心配置中增加

  ```xml
          <property name="hibernate.connection.isolation">4</property>
  
  ```

  

#### 提供统一的session
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


    public static Session getCurrentSession(){
        return HIBERNATE_SESSIONFACTORY.getCurrentSession();
    }
}


```
核心配置
```xml
        <property name="hibernate.current_session_context_class" >thread</property>

```



## api 

### query 

```java
	@Test
    public void query() {

        Session session = HibernateUtils.getCurrentSession();
        Transaction transaction = session.beginTransaction();

        // 条件查询
        Query query1 = session.createQuery("from Customer where cust_id>2 ");
        List list = query1.list();
        System.out.println(list.size());
        query1 = session.createQuery("from Customer  where cust_name like :cust_name");
        query1.setParameter("cust_name", "a%");
        list = query1.list();
        System.out.println(list);

        // 分页查询
        query1 = session.createQuery("from Customer ");
        // 设置分页
        // 设置第几页
        query1.setFirstResult(0);
        // 每一页最多
        query1.setMaxResults(3);
        list = query1.list();
        System.out.println(list);

        
        transaction.commit();

    }
```

### criteria

```java
	@Test
    public void criteria() {

        Session session = HibernateUtils.getCurrentSession();
        Transaction transaction = session.beginTransaction();

        Criteria criteria = session.createCriteria(Customer.class);

        List list = criteria.list();
        System.out.println(list);

        Criteria cust_name = criteria.add(Restrictions.like("cust_name", "a%"));
        List list1 = cust_name.list();
        System.out.println(list1);
        transaction.commit();

    }
```

----



## 数据表之间关系

### 一对多关系

- 一个客户对应多个联系人

#### 建表原则

 - 客户表
    - 客户id
    - 客户名称
 - 联系人
    - 联系人id
    - 联系人名称
    - 外键 ： 客户id
 - **在多的一方写   一的一方的主键id**

#### 级联操作

##### 级联保存或更新

- 一方配置

```xml
<set name="linkMans" cascade="save-update" inverse="true">
```

- 多方配置

```xml
        <many-to-one name="customer" cascade="save-update" class="com.huifer.hibernatebook.bean.Customer" column="lkm_cust_id"/>

```

**问题**

- 如果多方 一方 都写了 cascade 会出现数据变少问题 详见demo4

  ```java
     /**
       * 对象导航
       * 双方都配置 cascade
       */
      @Test
      public void demo4() {
  
          Session session = HibernateUtils.getCurrentSession();
          Transaction transaction = session.beginTransaction();
          Customer customer = new Customer();
          customer.setCust_name("cust01");
  
          LinkMan linkMan1 = new LinkMan();
          linkMan1.setLkm_name("LM1");
          LinkMan linkMan2 = new LinkMan();
          linkMan2.setLkm_name("LM2");
          LinkMan linkMan3 = new LinkMan();
          linkMan3.setLkm_name("LM3");
  
          linkMan1.setCustomer(customer);
          customer.getLinkMans().add(linkMan2);
          customer.getLinkMans().add(linkMan3);
  
          // 双方都设置了cascade
  //		session.save(linkMan1); // 发送几条insert语句  4条
  //		session.save(customer); // 发送几条insert语句  3条
          session.save(linkMan2); // 发送几条insert语句  1条
          transaction.commit();
  
      }
  ```

  

##### 级联删除

- 一方

  ```xml
          <set name="linkMans" cascade="save-update,delete" inverse="true">
  
  ```

  一方放弃外键维护权 inverse 不重复提交sql

#### 实例

**一定要重写 hashCode方法将 关联字段去除**

一对多配置映射说明

- 在一方配置

  set标签

  name ： 多方的集合属性和实体类中字段对应

  cascade： 级联

  inverser ：放弃外键维护权

  key 标签

  column ： 填写多方的外键名称

  one-to-many 标签

  class ： 多方全路径

- 在多方配置

  many-to-one 标签 

  name : 一方的属性名称 和实体类对应

  class ： 一方全路径

  column ： 填写多方的外键名称

- Customer

  ```java
  
  package com.huifer.hibernatebook.bean;
  
  import lombok.AllArgsConstructor;
  import lombok.Data;
  import lombok.NoArgsConstructor;
  
  import java.util.HashSet;
  import java.util.Objects;
  import java.util.Set;
  
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
  
  
      /**
       * 联系人
       */
      private Set<LinkMan> linkMans = new HashSet<LinkMan>();
  
      @Override
      public boolean equals(Object o) {
          if (this == o) return true;
          if (o == null || getClass() != o.getClass()) return false;
          Customer customer = (Customer) o;
          return Objects.equals(cust_name, customer.cust_name) &&
                  Objects.equals(cust_source, customer.cust_source) &&
                  Objects.equals(cust_industry, customer.cust_industry) &&
                  Objects.equals(cust_level, customer.cust_level) &&
                  Objects.equals(cust_phone, customer.cust_phone) &&
                  Objects.equals(cust_mobile, customer.cust_mobile);
      }
  
      @Override
      public int hashCode() {
          return Objects.hash(cust_name, cust_source, cust_industry, cust_level, cust_phone, cust_mobile);
      }
  }
  
  ```

- Customer.hbm.xml.tld

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  
  <!DOCTYPE hibernate-mapping PUBLIC
          "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
          "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
  
  
  <hibernate-mapping>
  
      <class name="com.huifer.hibernatebook.bean.Customer" table="cst_customer">
          <!-- 建立OID与主键映射 -->
          <id name="cust_id" column="cust_id">
              <generator class="native"/>
          </id>
          <!-- 建立普通属性与数据库表字段映射 -->
          <property name="cust_name" column="cust_name"/>
          <property name="cust_source" column="cust_source"/>
          <property name="cust_industry" column="cust_industry"/>
          <property name="cust_level" column="cust_level"/>
          <property name="cust_phone" column="cust_phone"/>
          <property name="cust_mobile" column="cust_mobile"/>
          <!-- 配置一对多的映射：放置的多的一方的集合 -->
          <!--
              set标签 ：
                  * name	：多的一方的对象集合的属性名称。
                  * cascade：级联
                  * inverse：放弃外键维护权。
          -->
          <set name="linkMans" cascade="save-update" inverse="true">
              <!--
                   key标签
                      * column：多的一方的外键的名称。
               -->
              <key column="lkm_cust_id"/>
              <!--
                  one-to-many标签
                      * class	:多的一方的类的全路径
               -->
              <one-to-many class="com.huifer.hibernatebook.bean.LinkMan"/>
          </set>
      </class>
  
  
  </hibernate-mapping>
  ```

  

- LinkMan

  ```java
  package com.huifer.hibernatebook.bean;
  
  import lombok.AllArgsConstructor;
  import lombok.Data;
  import lombok.NoArgsConstructor;
  
  import java.util.Objects;
  
  /**
   * 描述:
   * 联系人
   *
   * @author huifer
   * @date 2019-02-11
   */
  @Data
  @NoArgsConstructor
  @AllArgsConstructor
  public class LinkMan {
      /***
       *联系人编号
       */
      private Long lkm_id;
      /***
       *联系人姓名
       */
      private String lkm_name;
      /***
       *联系人性别
       */
      private String lkm_gender;
      /***
       *联系人办公电话
       */
      private String lkm_phone;
      /***
       *联系人手机
       */
      private String lkm_mobile;
      /***
       *联系人邮箱
       */
      private String lkm_email;
      /***
       *联系人qq
       */
      private String lkm_qq;
      /***
       *联系人职位
       */
      private String lkm_position;
      /***
       *联系人备注
       */
      private String lkm_memo;
      // 通过ORM方式表示：一个联系人只能属于某一个客户。
      // 放置的是一的一方的对象。
      private Customer customer;
  
  
      @Override
      public boolean equals(Object o) {
          if (this == o) return true;
          if (o == null || getClass() != o.getClass()) return false;
          LinkMan linkMan = (LinkMan) o;
          return Objects.equals(lkm_name, linkMan.lkm_name) &&
                  Objects.equals(lkm_gender, linkMan.lkm_gender) &&
                  Objects.equals(lkm_phone, linkMan.lkm_phone) &&
                  Objects.equals(lkm_mobile, linkMan.lkm_mobile) &&
                  Objects.equals(lkm_email, linkMan.lkm_email) &&
                  Objects.equals(lkm_qq, linkMan.lkm_qq) &&
                  Objects.equals(lkm_position, linkMan.lkm_position) &&
                  Objects.equals(lkm_memo, linkMan.lkm_memo);
      }
  
      @Override
      public int hashCode() {
          return Objects.hash(lkm_name, lkm_gender, lkm_phone, lkm_mobile, lkm_email, lkm_qq, lkm_position, lkm_memo);
      }
  }
  
  
  ```

- LinkMan.hbm.xml.tld

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE hibernate-mapping PUBLIC
          "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
          "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
  <hibernate-mapping>
      <class name="com.huifer.hibernatebook.bean.LinkMan" table="cst_linkman">
          <!-- 建立OID与主键映射 -->
          <id name="lkm_id" column="lkm_id">
              <generator class="native"/>
          </id>
          <!-- 建立普通属性与表字段映射 -->
          <property name="lkm_name"/>
          <property name="lkm_gender"/>
          <property name="lkm_phone"/>
          <property name="lkm_mobile"/>
          <property name="lkm_email"/>
          <property name="lkm_qq"/>
          <property name="lkm_position"/>
          <property name="lkm_memo"/>
          <!-- 配置多对一的关系：放置的是一的一方的对象 -->
          <!--
              many-to-one标签
                  * name		:一的一方的对象的属性名称。
                  * class		:一的一方的类的全路径。
                  * column	:在多的一方的表的外键的名称。
           -->
          <many-to-one name="customer" class="com.huifer.hibernatebook.bean.Customer" column="lkm_cust_id"/>
      </class>
  </hibernate-mapping>
  ```

- 测试类

  ```java
  package com.huifer.hibernatebook.bean;
  
  import com.huifer.hibernatebook.utils.HibernateUtils;
  import org.hibernate.Session;
  import org.hibernate.Transaction;
  import org.junit.Test;
  
  /**
   * 描述:
   * hibernate 关系测试类
   *
   * @author huifer
   * @date 2019-02-11
   */
  public class RelationsTest {
  
      @Test
      public void demo1() {
          Session session = HibernateUtils.getCurrentSession();
          Transaction transaction = session.beginTransaction();
  
          // 创建两个客户
          Customer customer1 = new Customer();
          customer1.setCust_name("客户1");
          Customer customer2 = new Customer();
          customer2.setCust_name("客户2");
  
          // 创建三个联系人
          LinkMan linkMan1 = new LinkMan();
          linkMan1.setLkm_name("联系人1");
          LinkMan linkMan2 = new LinkMan();
          linkMan2.setLkm_name("联系人2");
          LinkMan linkMan3 = new LinkMan();
          linkMan3.setLkm_name("联系人3");
  
          // 设置关系:
          linkMan1.setCustomer(customer1);
          linkMan2.setCustomer(customer1);
          linkMan3.setCustomer(customer2);
          customer1.getLinkMans().add(linkMan1);
          customer1.getLinkMans().add(linkMan2);
          customer2.getLinkMans().add(linkMan3);
  
          // 保存数据:
          session.save(linkMan1);
          session.save(linkMan2);
          session.save(linkMan3);
          session.save(customer1);
          session.save(customer2);
  
          transaction.commit();
      }
  
      /**
       * 级联保存
       * * 保存客户级联联系人
       * * 客户 -> 联系人
       * *客户是主体，配置 cascade
       */
      @Test
      public void demo2() {
          Session session = HibernateUtils.getCurrentSession();
          Transaction transaction = session.beginTransaction();
  
          Customer customer = new Customer();
          customer.setCust_name("级联保存客户1");
  
          LinkMan linkMan = new LinkMan();
          linkMan.setLkm_name("级联联系人1");
  
          customer.getLinkMans().add(linkMan);
          linkMan.setCustomer(customer);
  
          session.save(customer);
  
          transaction.commit();
      }
  
  
      /**
       * 级联保存
       * * 保存联系人级联客户
       * * 联系人 ->客户
       * * 联系人是主体，配置 cascade
       */
      @Test
      public void demo3() {
          Session session = HibernateUtils.getCurrentSession();
          Transaction transaction = session.beginTransaction();
  
          Customer customer = new Customer();
          customer.setCust_name("级联保存客户2");
  
          LinkMan linkMan = new LinkMan();
          linkMan.setLkm_name("级联联系人2");
  
          customer.getLinkMans().add(linkMan);
          linkMan.setCustomer(customer);
  
          session.save(linkMan);
  
          transaction.commit();
      }
  
      /**
       * 对象导航
       * 双方都配置 cascade
       */
      @Test
      public void demo4() {
  
          Session session = HibernateUtils.getCurrentSession();
          Transaction transaction = session.beginTransaction();
          Customer customer = new Customer();
          customer.setCust_name("cust01");
  
          LinkMan linkMan1 = new LinkMan();
          linkMan1.setLkm_name("LM1");
          LinkMan linkMan2 = new LinkMan();
          linkMan2.setLkm_name("LM2");
          LinkMan linkMan3 = new LinkMan();
          linkMan3.setLkm_name("LM3");
  
          linkMan1.setCustomer(customer);
          customer.getLinkMans().add(linkMan2);
          customer.getLinkMans().add(linkMan3);
  
          // 双方都设置了cascade
  //		session.save(linkMan1); // 发送几条insert语句  4条
  //		session.save(customer); // 发送几条insert语句  3条
  //        session.save(linkMan2); // 发送几条insert语句  1条
          transaction.commit();
  
      }
  
      /**
       * 级联删除
       */
      @Test
      public void demo5() {
  
          demo1();
  
          Session session = HibernateUtils.getCurrentSession();
          Transaction transaction = session.beginTransaction();
  
          Customer customer = session.get(Customer.class, 1L);
          session.delete(customer);
  
          transaction.commit();
  
  
      }
  
  
      /**
       * 联系人2 改成客户2
       * 提交多次sql
       * 一方放弃外键维护权
       */
      @Test
      public void demo6(){
          demo1();
          Session session = HibernateUtils.getCurrentSession();
          Transaction transaction = session.beginTransaction();
  
          LinkMan linkMan = session.get(LinkMan.class, 2L);
          Customer customer = session.get(Customer.class, 2L);
  
          linkMan.setCustomer(customer);
          customer.getLinkMans().add(linkMan);
  
          transaction.commit();
      }
  
  
      /**
       * cascade & inverse 区别
       * cascade 关联对象操作
       * inverse 外键操作
       */
      @Test
      public void demo7() {
          Session session = HibernateUtils.getCurrentSession();
          Transaction transaction = session.beginTransaction();
  
          Customer customer = new Customer();
          customer.setCust_name("级联保存客户1");
  
          LinkMan linkMan = new LinkMan();
          linkMan.setLkm_name("级联联系人1");
  
          customer.getLinkMans().add(linkMan);
          linkMan.setCustomer(customer);
  
          //         <set name="linkMans" cascade="save-update,delete" inverse="true">
          session.save(customer); // 两个实体都会保存 但是没有外键
  
          transaction.commit();
      }
  
  }
  
  ```

  

### 多对多关系

- 一个学生多个课程，一个课程被多个学生选择

#### 建表原则

 - 学生
    - 学生id
    - 学生名称
 - 课程
    - 课程id
    - 课程名称
 - 中间表
    - 主键
    - 学生id
    - 课程id
 - **创建中间表，至少两个字段分别指向两个多的主键**

#### 实例

**一定要重写 hashCode方法将 关联字段去除**

**有一方必须放弃外键维护**，否则出错，一般被动方放弃

- 多对多配置说明

  set标签

  name ： 对方集合属性名 对应实体类属性

  table 使用的中间表表名​	

  key标签

  column ： 当前对象对应中间表的字段

  many-to-many标签

  class：对方类的全路径

  column：对方对象的在中间表中的外键名称

- User

  ```java
  package com.huifer.hibernatebook.bean;
  
  import lombok.AllArgsConstructor;
  import lombok.Data;
  import lombok.EqualsAndHashCode;
  import lombok.NoArgsConstructor;
  
  import javax.management.relation.Role;
  import java.util.HashSet;
  import java.util.Set;
  
  /**
   * 描述:
   * 用户
   *
   * @author huifer
   * @date 2019-02-11
   */
  @Data
  @NoArgsConstructor
  @AllArgsConstructor
  @EqualsAndHashCode
  public class User {
      /***
       *用户id
       */
      private Long user_id;
      /***
       *用户账号
       */
      private String user_code;
      /***
       *用户名称
       */
      private String user_name;
      /***
       *用户密码
       */
      private String user_password;
      /***
       * 用户状态
       *1:正常,0:暂停
       */
      private String user_state;
      // 设置多对多关系：表示一个用户选择多个角色？
      // 放置的是角色的集合
      /***
       * 角色
       */
      private Set<Role> roles = new HashSet<Role>();
  }
  
  ```

- User.hbm.xml.tld

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE hibernate-mapping PUBLIC
          "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
          "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
  <hibernate-mapping>
      <class name="com.huifer.hibernatebook.bean.User" table="sys_user">
          <!-- 建立OID与主键的映射 -->
          <id name="user_id" column="user_id">
              <generator class="native"/>
          </id>
          <!-- 建立普通属性与字段映射 -->
          <property name="user_code" column="user_code"/>
          <property name="user_name" column="user_name"/>
          <property name="user_password" column="user_password"/>
          <property name="user_state" column="user_state"/>
          <!-- 建立与角色的多对多的映射关系 -->
          <!--
              set标签
                  * name		：对方的集合的属性名称。
                  * table		：多对多的关系需要使用中间表，放的是中间表的名称。
           -->
          <set name="roles" table="sys_user_role" cascade="save-update,delete">
              <!--
                  key标签：
                      * column	：当前的对象对应中间表的外键的名称。
               -->
              <key column="user_id"/>
              <!--
                  many-to-many标签：
                      * class		：对方的类的全路径
                      * column	：对方的对象在中间表中的外键的名称。
               -->
              <many-to-many class="com.huifer.hibernatebook.bean.Role" column="role_id"/>
          </set>
      </class>
  </hibernate-mapping>
  ```

  

- Role

  ```java
  package com.huifer.hibernatebook.bean;
  
  import lombok.AllArgsConstructor;
  import lombok.Data;
  import lombok.EqualsAndHashCode;
  import lombok.NoArgsConstructor;
  
  import java.util.HashSet;
  import java.util.Set;
  
  /**
   * 描述:
   * 角色
   *
   * @author huifer
   * @date 2019-02-11
   */
  @Data
  @NoArgsConstructor
  @AllArgsConstructor
  @EqualsAndHashCode
  public class Role {
      /***
       * id
       */
      private Long role_id;
      /***
       *角色名称
       */
      private String role_name;
      /***
       *备注
       */
      private String role_memo;
      // 一个角色被多个用户选择：
      // 放置的是用户的集合
      /***
       * user列表
       */
      private Set<User> users = new HashSet<User>();
  
  }
  
  ```

- Role.hbm.xml.tld

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE hibernate-mapping PUBLIC
          "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
          "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
  <hibernate-mapping>
      <class name="com.huifer.hibernatebook.bean.Role" table="sys_role">
          <!-- 建立OID与主键的映射 -->
          <id name="role_id" column="role_id">
              <generator class="native"/>
          </id>
          <!-- 建立普通属性与字段的映射 -->
          <property name="role_name" column="role_name"/>
          <property name="role_memo" column="role_memo"/>
          <!-- 与用户的多对多的映射关系 -->
          <!--
              set标签
                  * name		：对方的集合的属性名称。
                  * table		：多对多的关系需要使用中间表，放的是中间表的名称。
           -->
          <set name="users" table="sys_user_role" cascade="save-update,delete" inverse="true">
              <!--
                  key标签：
                      * column	：当前的对象对应中间表的外键的名称。
               -->
              <key column="role_id"/>
              <!--
                  many-to-many标签：
                      * class		：对方的类的全路径
                      * column	：对方的对象在中间表中的外键的名称。
               -->
              <many-to-many class="com.huifer.hibernatebook.bean.User" column="user_id"/>
          </set>
      </class>
  </hibernate-mapping>
  ```

- 测试类

  ```java
  package com.huifer.hibernatebook.bean;
  
  import com.huifer.hibernatebook.utils.HibernateUtils;
  import org.hibernate.Session;
  import org.hibernate.Transaction;
  import org.junit.Test;
  
  /**
   * 描述:
   *
   * @author huifer
   * @date 2019-02-11
   */
  public class ManyToManyTest {
      @Test
      /**
       * 保存多条记录：保存多个用户和角色
       */
      public void demo1() {
          Session session = HibernateUtils.getCurrentSession();
          Transaction tx = session.beginTransaction();
  
          // 创建2个用户
          User user1 = new User();
          user1.setUser_name("用户1");
          User user2 = new User();
          user2.setUser_name("用户2");
  
          // 创建3个角色
          Role role1 = new Role();
          role1.setRole_name("角色A");
          Role role2 = new Role();
          role2.setRole_name("角色B");
          Role role3 = new Role();
          role3.setRole_name("角色C");
  
          // 设置双向的关联关系:
          user1.getRoles().add(role1);
          user1.getRoles().add(role1);
          user1.getRoles().add(role2);
          user2.getRoles().add(role2);
          user2.getRoles().add(role3);
          role1.getUsers().add(user1);
          role2.getUsers().add(user1);
          role2.getUsers().add(user2);
          role3.getUsers().add(user2);
  
          // 保存操作:多对多建立了双向的关系必须有一方放弃外键维护。
          // 一般是被动方放弃外键维护权。
          session.save(user1);
          session.save(user2);
          session.save(role1);
          session.save(role2);
          session.save(role3);
  
          tx.commit();
      }
  
      /**
       * 给用户选角色
       */
      @Test
      public void demo2(){
          demo1();
  
          Session session = HibernateUtils.getCurrentSession();
          Transaction tx = session.beginTransaction();
  
          // 给1号用户多选2号角色
          // 查询1号用户
          User user  = session.get(User.class, 1L);
          // 查询2号角色
          Role role = session.get(Role.class, 2L);
          user.getRoles().add(role);
  
          tx.commit();
      }
  
      /***
       *给用户改选角色
       */
      @Test
      public void demo3(){
          demo1();
          Session session = HibernateUtils.getCurrentSession();
          Transaction tx = session.beginTransaction();
  
          // 给2号用户将原有的2号角色改为3号角色
          // 查询2号用户
          User user  = session.get(User.class, 2L);
          // 查询2号角色
          Role role2 = session.get(Role.class, 2L);
          Role role3 = session.get(Role.class, 3L);
          user.getRoles().remove(role2);
          user.getRoles().add(role3);
  
          tx.commit();
      }
  }
  
  ```

  

### 一对一关系

-  一个家对应一个地址

#### 建表原则

- 家
  - 家id
  - 家庭住址
- 地址
  - 地址id
  - 地址描述
  - 外键：外键约束唯一 从家表格中获取
- 主键对应一对一

---

## 查询方式

### 对象导航查询

根据查询到的结果对象 ，获得相关联的对象， 用于一对多，多对一 关系中的查询

### OID查询

- get()
- load()

### **HQL查询**

#### 排序查询

```java
  /**
     * 排序查询
     */
    @Test
    public void demo3(){
        Session session = HibernateUtils.getCurrentSession();
        Transaction tx = session.beginTransaction();

//        List<Customer> customer1 = session.createQuery("from Customer  order by cust_id desc ").list();
        List<Customer> customer1 = session.createQuery("from Customer  order by cust_id asc ").list();

        for (Customer customer : customer1) {
            System.out.println(customer);
        }
        tx.commit();
    }
```



#### 条件查询

```java
    /**
     * 条件
     */
    @Test
    public void demo4(){
        Session session = HibernateUtils.getCurrentSession();
        Transaction tx = session.beginTransaction();
        Query query = session.createQuery("from Customer where cust_name = :cust_name ");
        query.setParameter("cust_name","ac");
        List<Customer> customer1 = query.list();

        for (Customer customer : customer1) {
            System.out.println(customer);
        }
        tx.commit();
    }
```



#### 投影查询

```java
    /**
     * 投影查询
     */
    @Test
    public void demo5() {
        Session session = HibernateUtils.getCurrentSession();
        Transaction tx = session.beginTransaction();
        Query query = session.createQuery("SELECT cust_name,cust_id FROM Customer ");
        List list = query.list();
        System.out.println(list);

        // 在实体类中编写构造方法，构造方法参数为你需要查询的属性
        Query query1 = session.createQuery("SELECT new Customer (cust_name) from Customer ");
        List list1 = query1.list();
        System.out.println(list1);
        tx.commit();
    }
```

#### 分组统计

```java
 /**
     * 分组查询
     */
    @Test
    public void demo7(){
        Session session = HibernateUtils.getCurrentSession();
        Transaction tx = session.beginTransaction();

        Query query = session.createQuery("SELECT l.lkm_name, COUNT(*) FROM LinkMan as l GROUP BY l.customer");
        List<Object[]> list = query.list();
        for (Object[] o : list) {
            System.out.println(Arrays.toString(o));
        }
        tx.commit();
    }
```



#### 分页查询

```java

    /**
     * 分页查询
     */
    @Test
    public void demo6(){
        Session session = HibernateUtils.getCurrentSession();
        Transaction tx = session.beginTransaction();

        Query query = session.createQuery("FROM LinkMan ");
        query.setFirstResult(0);
        query.setMaxResults(10);
        List list = query.list();
        for (Object o : list) {
            System.out.println(o);
        }
        tx.commit();
    }
```

#### 多表查询

##### 交叉连接

笛卡尔乘积

select * from table_a ,table_b



##### 内连接 

inner join  \ join 结果是交集，**公共部分**

- 隐式内连接

  - select from A,B where A.id = B.id;

- 显示内连接

  - select * from A inner join on A.id = B.id;

- 实例

  ```java
      /**
       * Hql多表查询
       */
      @Test
      public void demo8(){
          Session session = HibernateUtils.getCurrentSession();
          Transaction tx = session.beginTransaction();
  
          Query query = session.createQuery("FROM Customer  c inner join c.linkMans");
          List<Object[]> list = query.list();
          for (Object [] o : list) {
              System.out.println(Arrays.toString(o));
          }
          System.out.println("--------------------------------");
          // fetch 会将数据整合
          Query query1 = session.createQuery("select distinct  c FROM Customer  c inner join fetch c.linkMans");
          List<Customer> list1 = query1.list();
          for (Customer o : list1) {
              System.out.println(o);
          }
  
          tx.commit();
      }
  ```

  

##### 外连接

- 左外连接

  - 左侧表的全部数据 + 公共部分
  - select * from A left join B on A.id = B.id

- 右外连接

  - 右侧表的全部数据 + 公共部分
  - select * from A right join B on A.id = B.id

- 实例

  ```java
  @Test
      public void demo9(){
          Session session = HibernateUtils.getCurrentSession();
          Transaction tx = session.beginTransaction();
  
          Query query = session.createQuery("FROM Customer  c left join c.linkMans");
          List<Object[]> list = query.list();
          for (Object[] o : list) {
              System.out.println(Arrays.toString(o));
          }
  
          tx.commit();
      }
  ```

  



---

## 优化

### 延迟加载

类延迟加载默认开启

```xml
    <class name="com.huifer.hibernatebook.bean.Customer" table="cst_customer" lazy="true">

```

### 关联级别延迟加载

```xml
        <set name="linkMans" lazy="true" >
```

#### 抓取策略

set 标签 或者 many-to-one 配置   lazy + fetch

- SET
  - fetch 抓取策略 ，控制sql格式
    - select : 默认值  发送select 
    - join ： 发送 迫切左外连接 **lazy 失效**
    - subselect 发送 子查询关联对象
  - lazy 延迟加载 ，控制关联对象是否采用延迟
    - true ： 默认值 采用延迟加载
    - false ： 不采用延迟加载
    - extra：用什么发什么
- many-to-one
  - fetch 
    - select ： 默认值
    - join
  - lazy
    - proxy ： 默认值
    - false
    - no-proxy **不会使用**



