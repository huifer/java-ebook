# MVC spring mvc

* 模型 model : 代表业务数据和业务逻辑
* 试图 view  :对模型的展示
* 控制器 controller:定义应用的各种行为

Spring Mvc 请求流程

![](/ch-2/pic/springMvc流程.png)

1. 用户发送请求到DispatchServler
2. 从Handler mapping 中匹配相关信息的Handler，
   1. 匹配内容有：
      1. 请求路径
      2. header
      3. 请求方法
   2. 常用Handler Mapping
      1. SimpleUrlHandlerMapping : url映射到Handler
      2. RequestMappingHandlerMapping : 通过@RequestMapping 注解匹配
3. 获取到Handler 调用相关controller 方法
   1. 常用HandlerAdapter
      1. SimpleControllerHandlerAdapter: 处理实现Controller的Controller
      2. RequestMappingHandlerAdapter: 处理HandlerMethod 的Handler ，用@RequestMapping 注解的Controller就是一种HandlerMethod 
4. Handler 执行完成返回相应的ModelAndView
5. ViewResolver 解析结果
   1. 常用ViewResolver
      1. UrlBasedViewResolver : 通过配置文件根据URL把一个视图名交给一个View来处理
      2. InternalResourceViewResolver: 根据资源路径解析成JSP视图
6. 将视图返回给用户
   1. 常用视图
      1. MappingJackson2JsonView : 使用mappingJackson 输出JSON
      2. FreeMarkerView: 使用FreeMarker 模板引擎的视图

## 数据校验

@Null : 验证对象是否空

@NotNull : 验证对象是否非空

@AssertTrue : 验证boolean对象是否true

@AssertFalse：验证boolean对象是否False

@Min: 验证是否大于等于指定值

@Max: 验证是否小于等于最小值

