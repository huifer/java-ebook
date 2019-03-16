#  	SpringMvc

[【源码仓库】](https://github.com/wt1187982580/javaBook-src/tree/master/mySpringMvcBook)

## 三层结构

- 表现层
  - MVC模型

- 业务层
  - service 

- 持久层
  - dao

## 工作流程

```sequence
用户->前端控制器:用户发送请求
前端控制器-> 后端控制器:根据用户请求查询具体控制器
后端控制器-->前端控制器:处理后结果
前端控制器--> 视图:视图渲染
视图-->前端控制器:返回视图
前端控制器--> 用户:响应结果

```

## 简单案例

依赖

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.huifer</groupId>
    <artifactId>mySpringMvcBook</artifactId>
    <version>1.0-SNAPSHOT</version>

    <packaging>war</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring.version>5.1.5.RELEASE</spring.version>
        <junit.version>4.12</junit.version>
    </properties>

<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-expression</artifactId>
        <version>${spring.version}</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>aopalliance</groupId>
        <artifactId>aopalliance</artifactId>
        <version>1.0</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>${spring.version}</version>
    </dependency>


    <!-- https://mvnrepository.com/artifact/javax.servlet.jsp.jstl/jstl -->
    <dependency>
        <groupId>javax.servlet.jsp.jstl</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.4</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.9.3</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-core</artifactId>
        <version>2.9.3</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-annotations -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-annotations</artifactId>
        <version>2.9.3</version>
    </dependency>
</dependencies>

    <build>
        <resources>
            <!--千千万万别忘记-->
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

spring-config

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.huifer.springmvc"/>


    <!-- 配置注解的适配器和映射器，同时还注入了很多其他的bean -->
    <mvc:annotation-driven/>


</beans>
```

web.xml

```JAVA
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">


    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc-config.xml</param-value>
        </init-param>
        <!--跟随tomcat启动-->
        <load-on-startup>2</load-on-startup>

    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

- controller

  ```JAVA
  @Controller
  @RequestMapping("item")
  public class ItemController {
      @ResponseBody
      @GetMapping("/query")
      public ModelAndView query() throws Exception {
  
          List<Item> itemList = new ArrayList<>();
          itemList.add(new Item("吃的", 3.3, new Date()));
          itemList.add(new Item("玩的", 3.3, new Date()));
          ModelAndView modelAndView = new ModelAndView();
          modelAndView.addObject("itemList", itemList);
          modelAndView.setViewName("/WEB-INF/jsp/item.jsp");
          return modelAndView;
      }
  
  }
  ```

  

- item.JSP

  ```jsp
  <%--
    Created by IntelliJ IDEA.
    User: huifer
    Date: 2019/3/10
    Time: 11:26
    To change this template use File | Settings | File Templates.
  --%>
  <%@ page language="java" contentType="text/html; charset=UTF-8"
           pageEncoding="UTF-8" isELIgnored="false" %>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
      <title>ItemList</title>
  </head>
  <body>
  <h1>ITEM</h1>
  <p>${itemList}</p>
  </body>
  </html>
  ```

  ![1552202506265](assets/1552202506265.png)

## web.xml 中的加载顺序

context-param->listener->filter->servlet

## url-parttern 匹配顺序

1. 精确匹配 ，如/baidu
2. 目录匹配，如/*
3. 拓展名匹配，如*.jpg
4. 默认匹配 ，如/

### tomcat中的url-parttern

- 文件在 ${tomcatHome}/conf/web.xml中

- 发布到tomcat的web应用共享下面两个配置

  ```xml
     <!-- The mapping for the default servlet -->
      <servlet-mapping>
          <servlet-name>default</servlet-name>
          <url-pattern>/</url-pattern>
      </servlet-mapping>
  
      <!-- The mappings for the JSP servlet -->
      <servlet-mapping>
          <servlet-name>jsp</servlet-name>
          <url-pattern>*.jsp</url-pattern>
          <url-pattern>*.jspx</url-pattern>
      </servlet-mapping>
  ```

  

### 此处面试题

为什么在自己的项目中web.xml配置/* 报错。如图

![1552204164914](assets/1552204164914.png)

- 根据tomcat的web.xml配置可以看到 **/** 和**jsp**的拦截是分开的 ，而我们直接用一个**/***来拦截那么tomcat将不知道用什么来处理

## spring-mvc大致流程源码翻阅

- 从配置文件中知道前端控制器DispatcherServlet

```xml
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-mvc-config.xml</param-value>
    </init-param>
    <!--跟随tomcat启动-->
    <load-on-startup>2</load-on-startup>

</servlet>
```

在org.springframework.web.servlet.DispatcherServlet 看下面两个方法

 ![1552204896179](assets/1552204896179.png)

- doService 将访问的数据接收到交给doDispatch
- doDispatch 具体调度

```JAVA
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
        HttpServletRequest processedRequest = request;
        HandlerExecutionChain mappedHandler = null;
        boolean multipartRequestParsed = false;
        WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

        try {
            try {
                ModelAndView mv = null;
                Object dispatchException = null;

                try {
                    processedRequest = this.checkMultipart(request);
                    multipartRequestParsed = processedRequest != request;
                   	// 加载handler
                    mappedHandler = this.getHandler(processedRequest);
                    if (mappedHandler == null) {
                        this.noHandlerFound(processedRequest, response);
                        return;
                    }
					// handler适配器
                    HandlerAdapter ha = this.getHandlerAdapter(mappedHandler.getHandler());
                    String method = request.getMethod();
                    boolean isGet = "GET".equals(method);
                    if (isGet || "HEAD".equals(method)) {
                        long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
                        if ((new ServletWebRequest(request, response)).checkNotModified(lastModified) && isGet) {
                            return;
                        }
                    }

                    if (!mappedHandler.applyPreHandle(processedRequest, response)) {
                        return;
                    }
					// 适配器执行操作
                    mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
                    if (asyncManager.isConcurrentHandlingStarted()) {
                        return;
                    }

                    this.applyDefaultViewName(processedRequest, mv);
                    mappedHandler.applyPostHandle(processedRequest, response, mv);
                } catch (Exception var20) {
                    dispatchException = var20;
                } catch (Throwable var21) {
                    dispatchException = new NestedServletException("Handler dispatch failed", var21);
                }
				// 操作结果返回
                this.processDispatchResult(processedRequest, response, mappedHandler, mv, (Exception)dispatchException);
            } catch (Exception var22) {
                this.triggerAfterCompletion(processedRequest, response, mappedHandler, var22);
            } catch (Throwable var23) {
                this.triggerAfterCompletion(processedRequest, response, mappedHandler, new NestedServletException("Handler processing failed", var23));
            }

        } finally {
            if (asyncManager.isConcurrentHandlingStarted()) {
                if (mappedHandler != null) {
                    mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
                }
            } else if (multipartRequestParsed) {
                this.cleanupMultipart(processedRequest);
            }

        }
    }
```

- handler怎么来

  - initHandlerMappings(context)

  ```java
  protected void initStrategies(ApplicationContext context) {
          this.initMultipartResolver(context);
          this.initLocaleResolver(context);
          this.initThemeResolver(context);
          this.initHandlerMappings(context);
          this.initHandlerAdapters(context);
          this.initHandlerExceptionResolvers(context);
          this.initRequestToViewNameTranslator(context);
          this.initViewResolvers(context);
          this.initFlashMapManager(context);
      }
  ```

  ```java
      private void initHandlerMappings(ApplicationContext context) {
          this.handlerMappings = null;
          if (this.detectAllHandlerMappings) {
              Map<String, HandlerMapping> matchingBeans = BeanFactoryUtils.beansOfTypeIncludingAncestors(context, HandlerMapping.class, true, false);
              if (!matchingBeans.isEmpty()) {
                  this.handlerMappings = new ArrayList(matchingBeans.values());
                  AnnotationAwareOrderComparator.sort(this.handlerMappings);
              }
          } else {
              try {
                  HandlerMapping hm = (HandlerMapping)context.getBean("handlerMapping", HandlerMapping.class);
                  this.handlerMappings = Collections.singletonList(hm);
              } catch (NoSuchBeanDefinitionException var3) {
              }
          }
  
          if (this.handlerMappings == null) {
              // 读取默认配置文件
              this.handlerMappings = this.getDefaultStrategies(context, HandlerMapping.class);
              if (this.logger.isTraceEnabled()) {
                  this.logger.trace("No HandlerMappings declared for servlet '" + this.getServletName() + "': using default strategies from DispatcherServlet.properties");
              }
          }
  
      }
  ```

  
  - 默认配置文件

  ![1552205750348](assets/1552205750348.png)

  ![1552205845954](assets/1552205845954.png)

- handler适配器也在配置文件中

  ```java
  
      protected HandlerAdapter getHandlerAdapter(Object handler) throws ServletException {
          if (this.handlerAdapters != null) {
              Iterator var2 = this.handlerAdapters.iterator();
  
              while(var2.hasNext()) {
                  HandlerAdapter adapter = (HandlerAdapter)var2.next();
                	// 是否能够适配
                  if (adapter.supports(handler)) {
                      return adapter;
                  }
              }
          }
  
          throw new ServletException("No adapter for handler [" + handler + "]: The DispatcherServlet configuration needs to include a HandlerAdapter that supports this handler");
      }
  ```

  ![1552206256235](assets/1552206256235.png)

  - HttpRequestHandlerAdapter

    ```java
    public class HttpRequestHandlerAdapter implements HandlerAdapter {
        public HttpRequestHandlerAdapter() {
        }
    // 判断是否是当前类支持的适配器
        public boolean supports(Object handler) {
            return handler instanceof HttpRequestHandler;
        }
    
    // 适配器执行操作
        @Nullable
        public ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
            ((HttpRequestHandler)handler).handleRequest(request, response);
            return null;
        }
    
        public long getLastModified(HttpServletRequest request, Object handler) {
            return handler instanceof LastModified ? ((LastModified)handler).getLastModified(request) : -1L;
        }
    }
    
    ```

    

- 视图解析

  ```java
   private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, @Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv, @Nullable Exception exception) throws Exception {
          boolean errorView = false;
          if (exception != null) {
              if (exception instanceof ModelAndViewDefiningException) {
                  this.logger.debug("ModelAndViewDefiningException encountered", exception);
                  mv = ((ModelAndViewDefiningException)exception).getModelAndView();
              } else {
                  Object handler = mappedHandler != null ? mappedHandler.getHandler() : null;
                  mv = this.processHandlerException(request, response, handler, exception);
                  errorView = mv != null;
              }
          }
  
          if (mv != null && !mv.wasCleared()) {
            	// 这个地方在做渲染
              this.render(mv, request, response);
              if (errorView) {
                  WebUtils.clearErrorRequestAttributes(request);
              }
          } else if (this.logger.isTraceEnabled()) {
              this.logger.trace("No view rendering, null ModelAndView returned.");
          }
  
          if (!WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
              if (mappedHandler != null) {
                  mappedHandler.triggerAfterCompletion(request, response, (Exception)null);
              }
  
          }
      }
  ```

  ```JAVA
  protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
          Locale locale = this.localeResolver != null ? this.localeResolver.resolveLocale(request) : request.getLocale();
          response.setLocale(locale);
          String viewName = mv.getViewName();
          View view;
          if (viewName != null) {
              // 视图解析器
              view = this.resolveViewName(viewName, mv.getModelInternal(), locale, request);
              if (view == null) {
                  throw new ServletException("Could not resolve view with name '" + mv.getViewName() + "' in servlet with name '" + this.getServletName() + "'");
              }
          } else {
              view = mv.getView();
              if (view == null) {
                  throw new ServletException("ModelAndView [" + mv + "] neither contains a view name nor a View object in servlet with name '" + this.getServletName() + "'");
              }
          }
  
          if (this.logger.isTraceEnabled()) {
              this.logger.trace("Rendering view [" + view + "] ");
          }
  
          try {
              if (mv.getStatus() != null) {
                  response.setStatus(mv.getStatus().value());
              }
  			// 视图的渲染函数渲染到页面上
              view.render(mv.getModelInternal(), request, response);
          } catch (Exception var8) {
              if (this.logger.isDebugEnabled()) {
                  this.logger.debug("Error rendering view [" + view + "]", var8);
              }
  
              throw var8;
          }
      }
  ```

  ```java
  @Nullable
  protected View resolveViewName(String viewName, @Nullable Map<String, Object> model, Locale locale, HttpServletRequest request) throws Exception {
      if (this.viewResolvers != null) {
          Iterator var5 = this.viewResolvers.iterator();
  
          while(var5.hasNext()) {
              // 视图解析器
              ViewResolver viewResolver = (ViewResolver)var5.next();
              View view = viewResolver.resolveViewName(viewName, locale);
              if (view != null) {
                  return view;
              }
          }
      }
  
      return null;
  }
  ```

![1552206766881](assets/1552206766881.png)

![1552206858246](assets/1552206858246.png)

## spring-mvc请求具体流程图

```sequence
用户->前端控制器\n org.springframework.web.servlet.DispatcherServlet\n 用来接收响应以及返回响应结果:1.用户发送请求 
前端控制器\n org.springframework.web.servlet.DispatcherServlet\n 用来接收响应以及返回响应结果 -> HandlerMapper : 2.根据url进行处理
HandlerMapper -> 前端控制器\n org.springframework.web.servlet.DispatcherServlet\n 用来接收响应以及返回响应结果:3.将handlerMapper处理结果给前端控制器
前端控制器\n org.springframework.web.servlet.DispatcherServlet\n 用来接收响应以及返回响应结果-> HandlerAdapter:4.给适配器确认具体的适配器，比如我们这里给了HttpRequestHandlerAdapter
HandlerAdapter ->ModelAndView:5.用来执行业务操作
ModelAndView ->HandlerAdapter: 6.执行完成给HandlerAdapter返回
HandlerAdapter->前端控制器\n org.springframework.web.servlet.DispatcherServlet\n 用来接收响应以及返回响应结果:7.返回一个modelAndView
前端控制器\n org.springframework.web.servlet.DispatcherServlet\n 用来接收响应以及返回响应结果-> ViewResolver 视图解析器 :8.将第7步中的view进行解析
ViewResolver 视图解析器->前端控制器\n org.springframework.web.servlet.DispatcherServlet\n 用来接收响应以及返回响应结果:9.解析结果返回
前端控制器\n org.springframework.web.servlet.DispatcherServlet\n 用来接收响应以及返回响应结果 -> View : 9.将model渲染到view中
View->前端控制器\n org.springframework.web.servlet.DispatcherServlet\n 用来接收响应以及返回响应结果: 10.view 结果返回给前端控制器
前端控制器\n org.springframework.web.servlet.DispatcherServlet\n 用来接收响应以及返回响应结果 --> 用户:查看到完整的网页
```

---

## SSM 整合单表操作

- 查询item表格

- 详细查看[【仓库】](https://github.com/wt1187982580/javaBook-src/tree/master/ssm)

- **注：本项目全部返回json**

### 请求参数绑定



- 请求参数格式为**k/v**
- 请求参数数据类型都是**字符串类型**

- 绑定的目标类型
  - controller中的形式参数
  - 简单类型
  - pojo类型
  - 集合类型
- spring-mvc内置解析组件 

### servlet API

- HttpServletRequest

- HttpServletResponse

- HttpSession

### 简单类型

key 和controller方法形式参数**名称一致**

http://localhost:8082/item/query?id=1&name=%E7%8E%8B

```java
@GetMapping("query")
public ResponseEntity query(Integer id, String name) {
    HashMap<Integer, String> has = new HashMap<>();
    has.put(id, name);
    return ResponseEntity.ok(has);

}
```

![1552474408436](assets/1552474408436.png)

- 注解绑定

- http://localhost:8082/item/query?id=3

- ```java
  
      @GetMapping("query")
  // required 是否必填， defaultValue = 默认值
      public ResponseEntity query(Integer id, @RequestParam(value = "name", required = false, defaultValue = 默认值 = "awsl") String name) {
          HashMap<Integer, String> has = new HashMap<>();
          has.put(id, name);
          return ResponseEntity.ok(has);
  
      }
  ```

### POJO类型

- http://localhost:8082/item/pojo?id=3&name=lkjl

- ```java
  @GetMapping("pojo")
  public ResponseEntity queryPojo(Integer id, String name, Item item) {
      return ResponseEntity.ok(item);
  
  }
  ```

  ![1552475091174](assets/1552475091174.png)

- 同名接收参数方法

  - http://localhost:8082/item/tm?item.id=1&item.name=abc&user.id=3&user.name=lkjljljlk

  - 利用搜索类 QueryModel 来进行类的包装 ，url 里面用类属性.具体属性的方式编写
  - ![1552475626315](assets/1552475626315.png)



### 数组类型

- http://localhost:8082/item/double?id=1&id=2

- ```java
  @GetMapping("double")
  public ResponseEntity array(String[] id) {
  //http://localhost:8082/item/double?id=1&id=2
      return ResponseEntity.ok(id);
  }
  ```

![1552475877836](assets/1552475877836.png)





## 异常

### 编译时异常

- 编译不通过，直接处理 ，用try  ， throws处理 
- 继承Exception 

### 运行时异常

- 空指针， 数组越界
  - 大量数据测试

- 继承RunTimeException



### SpringMvc 异常处理器HandlerExceptionResolver

- 制作一个ExceptionResolve

```java
public class MyExcetionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("/jsp/OC/error.jsp");
        return modelAndView;
    }
}
```

- 增加一个spring-mvc配置，将自定义的ExceptionResolve 注入

  ```xml
  <bean class="com.huifer.ssm.exception.MyExceptionResolver"></bean>
  ```



## Spring-Mvc 上传

- 依赖

  ```xml
      <!--文件上传-->
          <dependency>
              <groupId>commons-fileupload</groupId>
              <artifactId>commons-fileupload</artifactId>
              <version>1.4</version>
          </dependency> 
  ```

- org.springframework.web.servlet.DispatcherServlet  

 ```java
  public static final String MULTIPART_RESOLVER_BEAN_NAME = "multipartResolver";
 ```



- java 代码编写

  ```java
   @PostMapping("file")
      public void file(MultipartFile picFile) throws IOException {
          if (picFile != null) {
              // 保存到本地
              String uploadFile = picFile.getOriginalFilename();
              if (uploadFile != null && !"".contentEquals(uploadFile)) {
                  String ext = uploadFile.substring(uploadFile.lastIndexOf("."));
                  String bf = UUID.randomUUID().toString() + ext;
                  String baseDir = "E:\\work-java\\2019-java\\ssm\\upload\\";
                  File dirFile = new File(baseDir);
                  if (!dirFile.exists()) {
                      dirFile.mkdir();
                  }
                  picFile.transferTo(new File(baseDir + bf));
  
              }
  
          }
          return;
      }
  ```

  ![1552483541192](assets/1552483541192.png)

![1552483551629](assets/1552483551629.png)

**上传成功**

---

## 拦截器

```java
package com.huifer.ssm.HandlerIntercepter;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 描述:
 *
 * @author huifer
 * @date 2019-03-16
 */
public class MyHandlerInt implements HandlerInterceptor {
    /**
     * 应用场景 ： 登录 ， 授权
     * @param request
     * @param response
     * @param handler
     * @return
     * @throws Exception
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        return true;
    }

    /**
     * 应用场景：对公用数据模型发送给前台
     * @param request
     * @param response
     * @param handler
     * @param modelAndView
     * @throws Exception
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }

    /**
     * 应用场景：统一异常处理， 统一日志处理
     * @param request
     * @param response
     * @param handler
     * @param ex
     * @throws Exception
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}

```

- 拦截器配置

  ```xml
      <!--拦截器配置-->
      <mvc:interceptors>
          <!--全局拦截器配置-->
          <bean class="com.huifer.ssm.HandlerIntercepter.MyHandlerInt"/>
          <!--单一拦截器配置-->
          <mvc:interceptor>
              <mvc:mapping path="/test/**"/>
              <bean class="com.huifer.ssm.HandlerIntercepter.MyHandlerInt"/>
          </mvc:interceptor>
      </mvc:interceptors>
  ```

- 拦截器最先写的先被拦截





---

## CORS 跨域

- jsonp 不支持 POST请求

- IE>10
- 响应头：Access-Control-Allow-Origin
- 请求分类
  - 简单请求，一下两个条件同时满足
    - 请求方式属于下列内容
      - head
      - get
      - post
    - http头信息不超过下列内容
      - Accept
      - Accept-Language
      - Last-Event-Id
      - Context-language
      - Content-Type(text/plain,multipart/form-data,application/x-www-form-urlencoded)
  - 非简单请求
    - 除了上述简单请求外都是非简单请求



- Cros拦截器

  ```java
  package com.huifer.ssm.interceptor;
  
  import org.springframework.web.servlet.HandlerInterceptor;
  
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  
  /**
   * 描述:
   *
   * @author huifer
   * @date 2019-03-16
   */
  public class CrossInter implements HandlerInterceptor {
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          if (request.getHeader("Origin") != null) {
              response.setContentType("text/html;charset=UTF-8");
              // 允许那个url
              response.setHeader("Access-Control-Allow-Origin", request.getHeader("Origin"));
              // 允许的请求方法
              response.setHeader("Access-Control-Allow-Methods", "POST,GET,DELETE");
              // 允许请求头参数列表
              response.setHeader("Access-Control-Allow-Headers",
                      "Origin, No-Cache, X-Requested-With, If-Modified-Since, Pragma, Last-Modified, Cache-Control, Expires, Content-Type, X-E4M-With,userId,token");
              // 带 cookie 访问
              response.setHeader("Access-Control-Allow-Credentials", "true");
              response.setHeader("XDomainRequestAllowed", "1");
  
              System.out.println("CROS doing");
  
          }
          return true;
      }
  
  }
  
  ```

- js访问代码

  ```HTML
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.js"></script>
  </head>
  <body>
  <button onclick="ajaxbutton()">ajax</button>
  <script>
      function ajaxbutton() {
          $.ajax({
              url: "http://localhost:8082/item/queryItem",
              method: "GET",
              xhrFields: {withCredentials: true},
              success: function (data) {
                  console.log(data);
              },
              error: function () {
                  console.log("error")
              }
          })
  
      }
  </script>
  </body>
  </html>
  ```

  

- Cros 没有注入的时候

  ![1552704628521](assets/1552704628521.png)

- 使用jquery ajax访问

  ![1552704665827](assets/1552704665827.png)

  没有办法正常获取数据

- 将cros配置开放

  ```xml
      <mvc:interceptors>
          <bean class="com.huifer.ssm.interceptor.CrossInter"/>
          <!--全局拦截器配置-->
          <bean class="com.huifer.ssm.interceptor.MyHandlerInt"/>
          <!--单一拦截器配置-->
          <mvc:interceptor>
              <mvc:mapping path="/test/**"/>
              <bean class="com.huifer.ssm.interceptor.MyHandlerInt"/>
          </mvc:interceptor>
      </mvc:interceptors>
  
  ```

  就可以正常访问数据了

- ![1552704758284](assets/1552704758284.png)

---

## 源码阅读

### 问题1 :spring 如何将外部文件properties对应填充到value中

```properties
db.driver=com.mysql.cj.jdbc.Driver
db.url=jdbc:mysql://localhost:3306/dy_java?serverTimezone=UTC&rewriteBatchedStatements=true&useUnicode=true&characterEncoding=utf8
db.username=root
db.password=root
```

```xml
<context:property-placeholder location="classpath:db.properties"/>
<!--配置数据源-->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="${db.driver}"/>
    <property name="url" value="${db.url}"/>
    <property name="username" value="${db.username}"/>
    <property name="password" value="${db.password}"/>
</bean>
```

- 根据之前的阅读可以知道 在spring中有org.springframework.context.config.ContextNamespaceHandler 这个类可以来解析xml标签的内容

- 进入ContextNamespaceHandler  可以看到字符串标识 property-placeholder

  ![1552708428602](assets/1552708428602.png)

- org.springframework.context.config.PropertyPlaceholderBeanDefinitionParser 类中的doParse方法

  ![1552708482436](assets/1552708482436.png)

- 进入 org.springframework.context.config.AbstractPropertyLoadingBeanDefinitionParser 具体查看doParse()方法

  - location获取
  - ![1552708614174](assets/1552708614174.png)

  ![1552709141841](assets/1552709141841.png)

  

- 找到具体的 bean初始化 org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory 目前还是没有内容填写的

  - ![1552709495576](assets/1552709495576.png)

- 接下来的读取properties 以及bean属性赋值 就和springDI相关。

### 问题2 :**RequestMappingHandlerMapping** 注解RequestMapping

#### 注册

- org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter 

  - initHandlerMethods

    - ```java
      // 用来初始化handler方法
      protected void initHandlerMethods() {
          String[] var1 = this.getCandidateBeanNames();
          int var2 = var1.length;
      
          for(int var3 = 0; var3 < var2; ++var3) {
              String beanName = var1[var3];
              if (!beanName.startsWith("scopedTarget.")) {
                  this.processCandidateBean(beanName);
              }
          }
      
          this.handlerMethodsInitialized(this.getHandlerMethods());
      }
      ```

  - processCandidateBean

    - ```JAVA
      // 过滤不是handler的方法 
      protected void processCandidateBean(String beanName) {
          Class beanType = null;
      
          try {
              // 获取bean的类型
              beanType = this.obtainApplicationContext().getType(beanName);
          } catch (Throwable var4) {
              if (this.logger.isTraceEnabled()) {
                  this.logger.trace("Could not resolve type for bean '" + beanName + "'", var4);
              }
          }
      
          // 判断是不是handler 
          if (beanType != null && this.isHandler(beanType)) {
              this.detectHandlerMethods(beanName);
          }
      
      }
      ```

    - ```java
      protected boolean isHandler(Class<?> beanType) {
          return AnnotatedElementUtils.hasAnnotation(beanType, Controller.class) || AnnotatedElementUtils.hasAnnotation(beanType, RequestMapping.class);
      }
      ```

  - detectHandlerMethods

    - ```java
      // 构造具体的映射关系
      protected void detectHandlerMethods(Object handler) {
          Class<?> handlerType = handler instanceof String ? this.obtainApplicationContext().getType((String)handler) : handler.getClass();
          if (handlerType != null) {
              Class<?> userType = ClassUtils.getUserClass(handlerType);
              // 带有controller注解的方法过滤 method：
              Map<Method, T> methods = MethodIntrospector.selectMethods(userType, (method) -> {
                  try {
                      return this.getMappingForMethod(method, userType);
                  } catch (Throwable var4) {
                      throw new IllegalStateException("Invalid mapping on handler class [" + userType.getName() + "]: " + method, var4);
                  }
              });
              if (this.logger.isTraceEnabled()) {
                  this.logger.trace(this.formatMappings(userType, methods));
              }
      
              methods.forEach((method, mapping) -> {
                  Method invocableMethod = AopUtils.selectInvocableMethod(method, userType);
                  this.registerHandlerMethod(handler, invocableMethod, mapping);
              });
          }
      
      }
      ```

- org.springframework.web.servlet.handler.AbstractHandlerMethodMapping  initHandlerMethods将注解全部注册到handlermapping中
  - ![1552711323182](assets/1552711323182.png)
  - ![1552711362365](assets/1552711362365.png)
  - 

### 处理