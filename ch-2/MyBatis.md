# MyBatis

## 配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.huifer</groupId>
    <artifactId>mybatisBook</artifactId>
    <version>1.0-SNAPSHOT</version>


    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.0</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.13</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.4</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <inherited>true</inherited>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/dy_java?serverTimezone=UTC&amp;rewriteBatchedStatements=true&amp;useUnicode=true&amp;characterEncoding=utf8"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="DeptMapper.xml"/>
    </mappers>
</configuration>
```

### 测试

DeptMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="Dept">
    <insert id="insertDept" >
        INSERT INTO dept (dname, loc)
        VALUES (#{dname} , #{loc});
  </insert>
</mapper>
```

Dept.java

```java
package com.huifer.mybatis;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

/**
 * 描述:
 *
 * @author huifer
 * @date 2019-02-21
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Dept {
    private Long id;
    private String dname;
    private String loc;
}
```

测试类

```java
public class Demo {

    public static void main(String[] args) throws Exception {

        Dept dept = new Dept();
        dept.setDname("技术部");
        dept.setLoc("oc");
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(is);
        SqlSession session = factory.openSession();

        session.insert("insertDept", dept);
        session.commit();

        session.close();
    }

}
```

查询表

![](/ch-2/pic/mybatis/0001.png)

至此初步配置完成

---

## 执行过程

```mermaid
graph TD
    start[获取mybatis.config.xml] -->   conditionA[SqlSessionFactory]
    conditionA --> |xml 解析成 org.apache.ibatis.session.Configuration | conditionB[接收SqlSessionFactory] 
    conditionB --> |org.apache.ibatis.session.defaults.DefaultSqlSessionFactory.openSessionFromDataSource| conditionC[ SqlSession]
    conditionC --> |org.apache.ibatis.session.defaults.DefaultSqlSession insert方法 update方法| conditionD[insert方法]
    conditionD --> |  org.apache.ibatis.mapping.MappedStatement sqlSource属性 | conditionE[mapper 定位]
    conditionE --> | org.apache.ibatis.executor.SimpleExecutor doUpdate方法 stmt|conditionF[数据绑定到具体sql]
    conditionF --> |org.apache.ibatis.executor.statement.PreparedStatementHandler update方法 提交| conditionG[传输sql]

    conditionG -->|commit| stop
```

### session

获取mybatils-config.xml 配置 解析xml标签

* * org.apache.ibatis.session.defaults.DefaultSqlSessionFactory

    ```java
      private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level, boolean autoCommit) {
            Transaction tx = null;

            DefaultSqlSession var8;
            try {
                Environment environment = this.configuration.getEnvironment();
                TransactionFactory transactionFactory = this.getTransactionFactoryFromEnvironment(environment);
                tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);
                Executor executor = this.configuration.newExecutor(tx, execType);
                var8 = new DefaultSqlSession(this.configuration, executor, autoCommit);
            } catch (Exception var12) {
                this.closeTransaction(tx);
                throw ExceptionFactory.wrapException("Error opening session.  Cause: " + var12, var12);
            } finally {
                ErrorContext.instance().reset();
            }

            return var8;
        }
    ```

    ![](/ch-2/pic/mybatis/0002.png)

### insert

org.apache.ibatis.session.defaults.DefaultSqlSession

```java
 public int insert(String statement, Object parameter) {
        return this.update(statement, parameter);
    }

    public int update(String statement, Object parameter) {
        int var4;
        try {
            this.dirty = true;
            MappedStatement ms = this.configuration.getMappedStatement(statement);
            var4 = this.executor.update(ms, this.wrapCollection(parameter));
        } catch (Exception var8) {
            throw ExceptionFactory.wrapException("Error updating database.  Cause: " + var8, var8);
        } finally {
            ErrorContext.instance().reset();
        }

        return var4;
    }
```

ms 对象

![](/ch-2/pic/mybatis/0003.png)

### doUpdate

org.apache.ibatis.executor.SimpleExecutor

```java
public int doUpdate(MappedStatement ms, Object parameter) throws SQLException {
        Statement stmt = null;

        int var6;
        try {
            Configuration configuration = ms.getConfiguration();
            StatementHandler handler = configuration.newStatementHandler(this, ms, parameter, RowBounds.DEFAULT, (ResultHandler)null, (BoundSql)null);
            stmt = this.prepareStatement(handler, ms.getStatementLog());
            var6 = handler.update(stmt);
        } finally {
            this.closeStatement(stmt);
        }

        return var6;
    }
```

![](/ch-2/pic/mybatis/0004.png)

### update

```
org.apache.ibatis.executor.statement.PreparedStatementHandler
```

```java
public int update(Statement statement) throws SQLException {
        PreparedStatement ps = (PreparedStatement)statement;
        ps.execute();
        int rows = ps.getUpdateCount();
        Object parameterObject = this.boundSql.getParameterObject();
        KeyGenerator keyGenerator = this.mappedStatement.getKeyGenerator();
        keyGenerator.processAfter(this.executor, this.mappedStatement, ps, parameterObject);
        return rows;
    }
```

---



---

## Mapper

```xml
  <mappers>
        <!--<mapper resource="DeptMapper.xml"/>-->
        <package name="com.huifer.mybatis.dao"/>
    </mappers>
```


### 获取过程源码

- org.apache.ibatis.builder.xml.XMLConfigBuilder 使用parseConfiguration 方法将 mybatis-config.xml 中的mappers 标签内容获取到 mapperElement具体执行获取内容 ，作用将mappers添加到configuration 中

  ```java
  private void parseConfiguration(XNode root) {
          try {
              this.propertiesElement(root.evalNode("properties"));
              Properties settings = this.settingsAsProperties(root.evalNode("settings"));
              this.loadCustomVfs(settings);
              this.loadCustomLogImpl(settings);
              this.typeAliasesElement(root.evalNode("typeAliases"));
              this.pluginElement(root.evalNode("plugins"));
              this.objectFactoryElement(root.evalNode("objectFactory"));
              this.objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
              this.reflectorFactoryElement(root.evalNode("reflectorFactory"));
              this.settingsElement(settings);
              this.environmentsElement(root.evalNode("environments"));
              this.databaseIdProviderElement(root.evalNode("databaseIdProvider"));
              this.typeHandlerElement(root.evalNode("typeHandlers"));
              this.mapperElement(root.evalNode("mappers"));
          } catch (Exception var3) {
              throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + var3, var3);
          }
      }
  
  
  private void mapperElement(XNode parent) throws Exception {
          if (parent != null) {
              Iterator var2 = parent.getChildren().iterator();
  
              while(true) {
                  while(var2.hasNext()) {
                      XNode child = (XNode)var2.next();
                      String resource;
                      if ("package".equals(child.getName())) {
                          resource = child.getStringAttribute("name");
                          this.configuration.addMappers(resource);
                      } else {
                          resource = child.getStringAttribute("resource");
                          String url = child.getStringAttribute("url");
                          String mapperClass = child.getStringAttribute("class");
                          XMLMapperBuilder mapperParser;
                          InputStream inputStream;
                          if (resource != null && url == null && mapperClass == null) {
                              ErrorContext.instance().resource(resource);
                              inputStream = Resources.getResourceAsStream(resource);
                              mapperParser = new XMLMapperBuilder(inputStream, this.configuration, resource, this.configuration.getSqlFragments());
                              mapperParser.parse();
                          } else if (resource == null && url != null && mapperClass == null) {
                              ErrorContext.instance().resource(url);
                              inputStream = Resources.getUrlAsStream(url);
                              mapperParser = new XMLMapperBuilder(inputStream, this.configuration, url, this.configuration.getSqlFragments());
                              mapperParser.parse();
                          } else {
                              if (resource != null || url != null || mapperClass == null) {
                                  throw new BuilderException("A mapper element may only specify a url, resource or class, but not more than one.");
                              }
  
                              Class<?> mapperInterface = Resources.classForName(mapperClass);
                              this.configuration.addMapper(mapperInterface);
                          }
                      }
                  }
  
                  return;
              }
          }
      }
  ```

  ![](\ch-2\pic\mybatis\0005.png)

  当前Configuration 下的mappedStatements 属性

  ![](\ch-2\pic\mybatis\0006.png)

- org.apache.ibatis.binding.MapperRegistry     addMapper 方法，作用将解析到的接口对象放到configuration 中 ，一个接口只能注册一次

  ```java
  
      public <T> void addMapper(Class<T> type) {
          if (type.isInterface()) {
              if (this.hasMapper(type)) {
                  throw new BindingException("Type " + type + " is already known to the MapperRegistry.");
              }
  
              boolean loadCompleted = false;
  
              try {
                  this.knownMappers.put(type, new MapperProxyFactory(type));
                  MapperAnnotationBuilder parser = new MapperAnnotationBuilder(this.config, type);
                  parser.parse();
                  loadCompleted = true;
              } finally {
                  if (!loadCompleted) {
                      this.knownMappers.remove(type);
                  }
  
              }
          }
  
      }
  ```

- org.apache.ibatis.builder.annotation.MapperAnnotationBuilder    parse方法

  loadXmlResource 来确认加载具体的xml文件

  

  ```java
  public void parse() {
          String resource = this.type.toString();
          if (!this.configuration.isResourceLoaded(resource)) {
              this.loadXmlResource();
              this.configuration.addLoadedResource(resource);
              this.assistant.setCurrentNamespace(this.type.getName());
              this.parseCache();
              this.parseCacheRef();
              Method[] methods = this.type.getMethods();
              Method[] var3 = methods;
              int var4 = methods.length;
  
              for(int var5 = 0; var5 < var4; ++var5) {
                  Method method = var3[var5];
  
                  try {
                      if (!method.isBridge()) {
                          this.parseStatement(method);
                      }
                  } catch (IncompleteElementException var8) {
                      this.configuration.addIncompleteMethod(new MethodResolver(this, method));
                  }
              }
          }
  
          this.parsePendingMethods();
      }
  
  
      private void loadXmlResource() {
          if (!this.configuration.isResourceLoaded("namespace:" + this.type.getName())) {
              String xmlResource = this.type.getName().replace('.', '/') + ".xml";
              InputStream inputStream = this.type.getResourceAsStream("/" + xmlResource);
              if (inputStream == null) {
                  try {
                      inputStream = Resources.getResourceAsStream(this.type.getClassLoader(), xmlResource);
                  } catch (IOException var4) {
                  }
              }
  
              if (inputStream != null) {
                  XMLMapperBuilder xmlParser = new XMLMapperBuilder(inputStream, this.assistant.getConfiguration(), xmlResource, this.configuration.getSqlFragments(), this.type.getName());
                  xmlParser.parse();
              }
          }
  
      }
  
  ```
  **根据 loadXmlResource 中下面这行得知 ， PojoMapper.xml 要和 PojoMapperInterface 放在一个路径下**

  ```
  String xmlResource = this.type.getName().replace('.', '/') + ".xml";
  ```

  

  ![](\ch-2\pic\mybatis\0007.png)

### 具体sql

- org.apache.ibatis.builder.xml.XMLStatementBuilder    parseStatementNode 方法解析sql语句

  ```xml
    <select id="deptFindById" resultType="com.huifer.mybatis.pojo.Dept" parameterType="java.lang.Long">
          select * from dept where dept.id=#{deptno};
      </select>
  
  ```

  ```java
  public void parseStatementNode() {
          String id = this.context.getStringAttribute("id");
          String databaseId = this.context.getStringAttribute("databaseId");
          if (this.databaseIdMatchesCurrent(id, databaseId, this.requiredDatabaseId)) {
              Integer fetchSize = this.context.getIntAttribute("fetchSize");
              Integer timeout = this.context.getIntAttribute("timeout");
              String parameterMap = this.context.getStringAttribute("parameterMap");
              String parameterType = this.context.getStringAttribute("parameterType");
              Class<?> parameterTypeClass = this.resolveClass(parameterType);
              String resultMap = this.context.getStringAttribute("resultMap");
              String resultType = this.context.getStringAttribute("resultType");
              String lang = this.context.getStringAttribute("lang");
              LanguageDriver langDriver = this.getLanguageDriver(lang);
              Class<?> resultTypeClass = this.resolveClass(resultType);
              String resultSetType = this.context.getStringAttribute("resultSetType");
              StatementType statementType = StatementType.valueOf(this.context.getStringAttribute("statementType", StatementType.PREPARED.toString()));
              ResultSetType resultSetTypeEnum = this.resolveResultSetType(resultSetType);
              String nodeName = this.context.getNode().getNodeName();
              SqlCommandType sqlCommandType = SqlCommandType.valueOf(nodeName.toUpperCase(Locale.ENGLISH));
              boolean isSelect = sqlCommandType == SqlCommandType.SELECT;
              boolean flushCache = this.context.getBooleanAttribute("flushCache", !isSelect);
              boolean useCache = this.context.getBooleanAttribute("useCache", isSelect);
              boolean resultOrdered = this.context.getBooleanAttribute("resultOrdered", false);
              XMLIncludeTransformer includeParser = new XMLIncludeTransformer(this.configuration, this.builderAssistant);
              includeParser.applyIncludes(this.context.getNode());
              this.processSelectKeyNodes(id, parameterTypeClass, langDriver);
              SqlSource sqlSource = langDriver.createSqlSource(this.configuration, this.context, parameterTypeClass);
              String resultSets = this.context.getStringAttribute("resultSets");
              String keyProperty = this.context.getStringAttribute("keyProperty");
              String keyColumn = this.context.getStringAttribute("keyColumn");
              String keyStatementId = id + "!selectKey";
              keyStatementId = this.builderAssistant.applyCurrentNamespace(keyStatementId, true);
              Object keyGenerator;
              if (this.configuration.hasKeyGenerator(keyStatementId)) {
                  keyGenerator = this.configuration.getKeyGenerator(keyStatementId);
              } else {
                  keyGenerator = this.context.getBooleanAttribute("useGeneratedKeys", this.configuration.isUseGeneratedKeys() && SqlCommandType.INSERT.equals(sqlCommandType)) ? Jdbc3KeyGenerator.INSTANCE : NoKeyGenerator.INSTANCE;
              }
  
              this.builderAssistant.addMappedStatement(id, sqlSource, statementType, sqlCommandType, fetchSize, timeout, parameterMap, parameterTypeClass, resultMap, resultTypeClass, resultSetTypeEnum, flushCache, useCache, resultOrdered, (KeyGenerator)keyGenerator, keyProperty, keyColumn, databaseId, langDriver, resultSets);
          }
      }
  ```

  ![](\ch-2\pic\mybatis\0008.png)

- 还原sql方法

  ```
  SqlSource sqlSource = langDriver.createSqlSource(this.configuration, this.context, parameterTypeClass);
  ```

- org.apache.ibatis.scripting.defaults.RawSqlSource  RawSqlSource方法

  ```java
  public RawSqlSource(Configuration configuration, String sql, Class<?> parameterType) {
          SqlSourceBuilder sqlSourceParser = new SqlSourceBuilder(configuration);
          Class<?> clazz = parameterType == null ? Object.class : parameterType;
          this.sqlSource = sqlSourceParser.parse(sql, clazz, new HashMap());
      }
  ```

- org.apache.ibatis.builder.SqlSourceBuilder  parse方法还原成sql语句

  ```java
      public SqlSource parse(String originalSql, Class<?> parameterType, Map<String, Object> additionalParameters) {
          SqlSourceBuilder.ParameterMappingTokenHandler handler = new SqlSourceBuilder.ParameterMappingTokenHandler(this.configuration, parameterType, additionalParameters);
          GenericTokenParser parser = new GenericTokenParser("#{", "}", handler);
          String sql = parser.parse(originalSql);
          return new StaticSqlSource(this.configuration, sql, handler.getParameterMappings());
      }
  ```

  ![](\ch-2\pic\mybatis\0009.png)

- 最后看一下 sqlSource

  ![](\ch-2\pic\mybatis\0010.png)

---