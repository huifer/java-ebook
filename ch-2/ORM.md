# 对象关系映射 ORM

常用ORM 框架 **MyBatis** 、 **Hibernate**

# MyBatis & mybatis-generator

使用MyBatis 经常需要编写相关的Mapper.xml所以使用一个 mybatis-generator 来生成 （根据数据库表生成）

依赖

```xml
   <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.0</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-core</artifactId>
            <version>1.3.5</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.13</version>
        </dependency>

    <plugins>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.7</version>
                <executions>
                    <execution>
                        <id>Generate MyBatis Artifacts</id>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                    </execution>
                </executions>
                <dependencies>
                    <dependency>
                        <groupId>org.hsqldb</groupId>
                        <artifactId>hsqldb</artifactId>
                        <version>2.3.4</version>
                    </dependency>
                </dependencies>
            </plugin>


        </plugins>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.*</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/webapp</directory>
                <targetPath>META-INF/resources</targetPath>
                <includes>
                    <include>**/*.*</include>
                </includes>
            </resource>
        </resources>
    </build>
```

JDBC链接配置

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/dy?serverTimezone=UTC
jdbc.loginName=root
jdbc.pwd=root
```

mybatis配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>


    <!-- 读取配置文件 -->
    <properties resource="jdbc.properties"></properties>
    <!-- 配置链接数据库的环境 -->
    <environments default="mysql">
        <environment id="mysql">
            <!-- 按照jdbc 的方式进行事务的提交和回滚 -->
            <transactionManager type="JDBC" />
            <!-- POOLED 使用数据库连接池 -->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}" />
                <property name="url" value="${jdbc.url}" />
                <property name="username" value="${jdbc.loginName}" />
                <property name="password" value="${jdbc.pwd}" />
            </dataSource>
        </environment>
    </environments>
    <!-- 导入mapper文件 -->
    <mappers>
        <mapper resource="com/wang/mybatis/mapper/DouYuMapper.xml" />
    </mappers>

</configuration>
```

generatorConfig 配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE generatorConfiguration PUBLIC
        "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd" >
<generatorConfiguration>
    <!-- 本地数据库驱动程序jar包的全路径 -->
    <classPathEntry
            location="D:\dev\JAR\mysql\mysql-connector-java\8.0.13\mysql-connector-java-8.0.13.jar"/>
    <context id="context" targetRuntime="MyBatis3">
        <commentGenerator>
            <property name="suppressAllComments" value="false"/>
            <property name="suppressDate" value="true"/>
        </commentGenerator>
        <!-- 数据库的相关配置 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver" connectionURL="jdbc:mysql://localhost:3306/dy?serverTimezone=UTC" userId="root"
                        password="root"/>
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!-- 实体类生成的位置 -->
        <javaModelGenerator targetPackage="com.wang.mybatis.model" targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- *Mapper.xml 文件的位置 -->
        <sqlMapGenerator targetPackage="com.wang.mybatis.mapper" targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
        <!-- Mapper 接口文件的位置 -->
        <javaClientGenerator targetPackage="com.wang.mybatis.mapper" targetProject="src/main/java"
                             type="XMLMAPPER">
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>
        <!-- 相关表的配置-->
        <table tableName="douyu"
               domainObjectName="DouYu"
               enableCountByExample="false"
               enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false"
               selectByExampleQueryId="false"
        />

    </context>
</generatorConfiguration>
```

Maven插件使用

![](/ch-2/pic/mybatis插件.png)

双击执行即可获得相关文件

![](/ch-2/pic/mapper.png)

利用mybatis-config.xml 链接查询

```java
package com.wang.mybatis.service;

import com.wang.mybatis.mapper.DouYuMapper;
import com.wang.mybatis.model.DouYu;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;

/**
 * 描述:
 *
 * @author huifer
 * @date 2019-02-04
 */
public class MyBatis {

    public static void main(String[] args) throws Exception {
        InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession session = sqlSessionFactory.openSession(true);

        DouYuMapper mapper = session.getMapper(DouYuMapper.class);
        DouYu douYu = mapper.selectByPrimaryKey(1);
        System.out.println(douYu);
        System.out.println();
    }
}

```



## 缓存

一级缓存默认开启 sqlSession级别 ， session结束缓存清空

二级缓存Mapper 级别 需要手动开启

​	开启方式

```xml
useCache="true"
```