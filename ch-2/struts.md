# Struts2



### 环境搭建

- 依赖

  ```xml
   <dependency>
              <groupId>org.apache.struts</groupId>
              <artifactId>struts2-core</artifactId>
              <version>2.5.20</version>
          </dependency>
  ```

  ![](E:\gitbook\Import\java_ji_neng\ch-2\pic\sttuts2配置1.png)

  ![](E:\gitbook\Import\java_ji_neng\ch-2\pic\sttuts2配置2.png)
  ![](E:\gitbook\Import\java_ji_neng\ch-2\pic\sttuts2配置3.png)

- 代码

  web.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
  
      <!--struts 过滤器-->
      <filter>
          <filter-name>struts2</filter-name>
          <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
      </filter>
      <filter-mapping>
          <filter-name>struts2</filter-name>
          <url-pattern>/*</url-pattern>
      </filter-mapping>
  </web-app>
  ```

  struts.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE struts PUBLIC
  	"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
  	"http://struts.apache.org/dtds/struts-2.3.dtd">
  <struts>
  	<!--配置struts包-->
  	<package name="com.huifer.struts.action.HelloAction" extends="struts-default" namespace="/">
  <!--配置action-->
  		<action name="hello" class="com.huifer.struts.action.HelloAction">
  
  		</action>
  	</package>
  
  
  
  </struts>
  
  ```

  index.jsp

  ```jsp
  <%--
    Created by IntelliJ IDEA.
    User: huifer
    Date: 2019/2/19
    Time: 20:54
    To change this template use File | Settings | File Templates.
  --%>
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <head>
      <title>Title</title>
  </head>
  <body>
  <h1>hello</h1>
  <h3><a href="${pageContext.request.contextPath}/hello.action">struts</a></h3>
  </body>
  </html>
  
  ```

  