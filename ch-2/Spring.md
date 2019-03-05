# Spring

## Ioc

### 依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>5.1.5.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>5.1.5.RELEASE</version>
</dependency>
```

#### 简单操作

- 定义学生类 student 和 老师类 teacher 并且交给spring 管理

```JAVA
@Data
public class Student {

    private String sname;
    private int age;
    private Teacher teacher;
}

```

```java
@Data
public class Teacher {
    private String tname;

}

```

- spring-config.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:p="http://www.springframework.org/schema/p"
         xmlns:c="http://www.springframework.org/schema/c"
         xsi:schemaLocation=" http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd">
  
  
      <bean id="teacher" class="com.huifer.bean.Teacher">
          <property name="tname" value="Mr.huifer"/>
      </bean>
  
      <bean id="student" class="com.huifer.bean.Student">
          <property name="age" value="21"/>
          <property name="sname" value="wang"/>
          <property name="teacher" ref="teacher"/>
      </bean>
  </beans>
  ```

- 每一个bean标签都是一个键值对形式表述 ， 在Ioc容器中。利用id属性获取

- 测试类

  ```java
  public class SpringIoc {
  
      @Test
      public void testIocDemo01(){
          ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("spring_config.xml");
  
          Student stu = (Student) context.getBean("student");
          Teacher tea = (Teacher) context.getBean("teacher");
  
          System.out.println();
      }
  
  }
  ```

  可以看到在Spring-config.xml 中定义的beans 在BeanDefinition中存放 是一个map对象

![![1551500140955](E:\gitbook\Import\java_ji_neng\ch-2\assets\1551500140955.png)](/ch-2/assets/1551500140955.png)

---

#### 模拟一个beans获取

- beans标签的储存

  ```java
  
  /**
   * 描述:
   * beans 标签的存储
   *
   * @author huifer
   * @date 2019-03-02
   */
  @Data
  public class BeanDefined {
      /**
       * bean id
       */
      private String  beanId;
      /**
       * bean对应的全路径
       */
      private String classPath;
  }
  
  ```

  

- BeanFactory bean工厂

  - 方法getBeans(String beanId) 用来获取bean

  ```java
  /**
   * 描述:
   * bean 工厂
   *
   * @author huifer
   * @date 2019-03-02
   */
  @Data
  public class BeanFactory {
      /**
       * beans标签列表
       */
      private List<BeanDefined> beanDefineds;
  
      /**
       * 模仿 Spring 中的getBeans
       *
       * @param beanId beans 标签的id
       * @return object 类
       */
      public   Object getBean(String beanId) {
          Object obj = null;
          // 从bean标签集合中获取id一样的
          for (BeanDefined beanDefined : beanDefineds) {
              if (beanDefined.getBeanId().equals(beanId)) {
                  // 构造出正确的类
                  String classPath = beanDefined.getClassPath();
                  try {
                      Class classFile = Class.forName(classPath);
                      obj = classFile.newInstance();
                      return obj;
                  } catch (ClassNotFoundException e) {
                      System.err.println("当前类文件不存在");
                      e.printStackTrace();
                  } catch (Exception e) {
                      e.printStackTrace();
                  }
              }
          }
          return obj;
  
      }
  
  }
  
  ```

- 测试用例

  ```java
  public class BeanFactoryTest {
  
  
      @Test
      public void beanTest() {
          // 1.声明 bean
          BeanDefined beanDefined = new BeanDefined();
          beanDefined.setBeanId("teacher");
          beanDefined.setClassPath("com.huifer.bean.Teacher");
          // 2.将bean放到工厂中
          List<BeanDefined> beanDefinedList = new ArrayList<BeanDefined>();
          beanDefinedList.add(beanDefined);
          BeanFactory beanFactory = new BeanFactory();
          beanFactory.setBeanDefineds(beanDefinedList);
          // 3.从工厂中获取具体实例
  
          Teacher teacher = (Teacher) beanFactory.getBean("teacher");
  
          System.out.println();
      }
  
  }
  ```

---

### spring scope  

- singleton 
  -  单例模式， spring容器启动就创建，每次都是同一个实例对象
- prototype : 
  - 在调用getBeans 创建， 每次都是全新实例

#### 修改 beansFactory

- BeanFactory

  -  getBeanScope

  ```java
  package com.huifer.util;
  
  import lombok.Data;
  import lombok.NoArgsConstructor;
  
  import java.util.HashMap;
  import java.util.List;
  import java.util.Map;
  
  /**
   * 描述:
   * bean 工厂
   *
   * @author huifer
   * @date 2019-03-02
   */
  @Data
  @NoArgsConstructor
  public class BeanFactory {
      /**
       * beans标签列表
       */
      private List<BeanDefined> beanDefineds;
  
      /**
       * 模仿 Spring 中的getBeans
       *
       * @param beanId beans 标签的id
       * @return object 类
       */
      public Object getBean(String beanId) {
          Object obj = null;
          // 从bean标签集合中获取id一样的
          for (BeanDefined beanDefined : beanDefineds) {
              if (beanDefined.getBeanId().equals(beanId)) {
                  // 构造出正确的类
                  String classPath = beanDefined.getClassPath();
                  try {
                      Class classFile = Class.forName(classPath);
                      obj = classFile.newInstance();
                      return obj;
                  } catch (ClassNotFoundException e) {
                      System.err.println("当前类文件不存在");
                      e.printStackTrace();
                  } catch (Exception e) {
                      e.printStackTrace();
                  }
              }
          }
          return obj;
  
      }
  
      /**
       * id-> 具体对象
       */
      private Map<String, Object> springSingleton;
  
      public BeanFactory(List<BeanDefined> beanDefineds) throws Exception {
          this.beanDefineds = beanDefineds;
  
          springSingleton = new HashMap<>();
          for (BeanDefined beans : beanDefineds) {
              if (beans.getScope().equals("singleton")) {
                  Object o = Class.forName(beans.getClassPath()).newInstance();
                  springSingleton.put(beans.getBeanId(), Class.forName(beans.getClassPath()).newInstance());
              }
          }
  
      }
  
      public Object getBeanScope(String beanId) throws Exception {
          Object obj = null;
          for (BeanDefined beanDefined : beanDefineds) {
              if (beanDefined.getBeanId().equals(beanId)) {
                  String classPath = beanDefined.getClassPath();
                  Class<?> classFile = Class.forName(classPath);
                  String scope = beanDefined.getScope();
                  if (scope.equals("prototype")) {
                      // 每次做一个新的对象返回
                      obj = classFile.newInstance();
                  } else {
                      // 每次返回同一个对象
                      springSingleton.get(beanDefined.getBeanId());
                  }
                  return obj;
  
              }
          }
          return obj;
      }
  
  
  }
  
  ```

- 测试用例

  ```java
      @Test
      public void beanTest02() throws Exception {
          // 1.声明 bean
          BeanDefined bean01 = new BeanDefined();
          bean01.setBeanId("teacher");
          bean01.setClassPath("com.huifer.bean.Teacher");
          bean01.setScope("prototype");
  
  
          BeanDefined bean02 = new BeanDefined();
          bean02.setBeanId("student");
          bean02.setClassPath("com.huifer.bean.Student");
  
  
          // 2.将bean放到工厂中
          List<BeanDefined> beanDefinedList = new ArrayList<BeanDefined>();
          beanDefinedList.add(bean01);
          beanDefinedList.add(bean02);
          BeanFactory beanFactory = new BeanFactory(beanDefinedList);
          // 3.从工厂中获取具体实例
  
          Teacher teacher1 = (Teacher) beanFactory.getBean("teacher");
          Teacher teacher2 = (Teacher) beanFactory.getBean("teacher");
  
          System.out.println(teacher1 == teacher2);
          Assert.assertFalse(teacher1==teacher2);
          Student stu = (Student) beanFactory.getBeanScope("student");
          Student st2 = (Student) beanFactory.getBeanScope("student");
          System.out.println(stu == st2);
          Assert.assertTrue(stu == st2);
  
          System.out.println();
      }
  
  ```

---

### 动态工厂

- 老师的自定义工厂

  ```java
  /**
   * 描述:
   * 老师工厂
   *
   * @author huifer
   * @date 2019-03-02
   */
  public class TeacherFactory {
      /**
       * 简单工厂
       *
       * @return
       */
      public Teacher createTeacher() {
          System.out.println("自定义老师工厂 简单工厂");
          return new Teacher();
      }
  }
  
  ```

- 添加配置

  ```xml
     <!--自定义工厂 ， 动态工厂-->
      <bean id="tFactory" class="com.huifer.util.TeacherFactory"></bean>
      <bean id="teacher1" factory-bean="tFactory" factory-method="createTeacher"></bean>
  
  ```

  

- 测试类

  ```java
  
      @Test
      public void testIocDemo02() {
          Teacher tea = (Teacher) context.getBean("teacher1");
  
      }
  ```

  

控制台输出
  自定义老师工厂 简单工厂

- 后期修改实例创建方式直接修改工厂类即可



#### 修改 beansFactory

```java
public Object getBeanFactory(String beanId) throws Exception {
        Object obj = null;
        for (BeanDefined beanDefined : beanDefineds) {

            if (beanDefined.getBeanId().equals(beanId)) {
                String classPath = beanDefined.getClassPath();
                Class<?> classFile = Class.forName(classPath);
                String scope = beanDefined.getScope();
                String factoryBean = beanDefined.getFactoryBean();
                String factoryMethod = beanDefined.getFactoryMethod();
                if (scope.equals("prototype")) {
                    // 每次做一个新的对象返回

                    if (factoryBean != null && factoryMethod != null) {
                        // 自己根据 factoryBean +  factoryMethod创建一个实例对象

                        Object oc = classFile.newInstance();
                        Method method = classFile.getDeclaredMethod(factoryMethod, null);
                        method.setAccessible(true);
                        obj = method.invoke(oc, null);
                    } else {
                        obj = classFile.newInstance();
                    }
                } else {
                    // 每次返回同一个对象
                    Ioc.get(beanDefined.getBeanId());
                }
                return obj;

            }
        }

        return obj;
    }
```

- 测试用例

  ```java
  @Test
      public void beanTest03() throws Exception {
          BeanDefined bean01 = new BeanDefined();
          bean01.setBeanId("teacher");
          bean01.setClassPath("com.huifer.bean.TeacherFactory");
          bean01.setFactoryBean("tFactory");
          bean01.setFactoryMethod("createTeacher");
          bean01.setScope("prototype");
  
  
  
          List<BeanDefined> beanDefinedList = new ArrayList<BeanDefined>();
          beanDefinedList.add(bean01);
  
          BeanFactory beanFactory = new BeanFactory(beanDefinedList);
  
  
          Teacher teacher1 = (Teacher) beanFactory.getBeanFactory("teacher");
          System.out.println(teacher1);
  
      }
  
  ```

- 控制台输出

  ```
  自定义老师工厂 简单工厂
  Teacher(tname=null)
  ```

  

---

### 静态工厂

static 关键字

- 静态工厂的创建

  ```java
  /**
       * 静态工厂
       *
       * @return
       */
      public static Teacher createStaticTeacher() {
          System.out.println("静态工厂的创建");
          return new Teacher();
      }
  ```

  

- 配置

  ```xml
   <!--自定义工厂 ，  静态工厂-->
      <bean id="teacherStatic" class="com.huifer.bean.TeacherFactory" factory-method="createStaticTeacher"></bean>
  
  ```

  

- 测试用例

  ```java
          Teacher tea = (Teacher) context.getBean("teacherStatic");
  
  ```

  

---

### BeanPostProcessor 接口

对bean创建前创建后的应用

- 需求 返回doSome() 的大写

- 一个简单接口的实现

  ```java
  public class BaseServiceImpl implements BaseService {
      @Override
      public String  doSome() {
          return "implimplimplimpl";
      }
  }
  
  ```

  

- 实现自己的BeanPostProcessor

  ```java
  /**
   * 描述:
   *
   * @author huifer
   * @date 2019-03-02
   */
  public class MyBeanPostPro implements BeanPostProcessor {
      @Override
      public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
          System.out.println("初始化前");
          return bean;
      }
  
      @Override
      public Object postProcessAfterInitialization(final Object bean, String beanName) throws BeansException {
          System.out.println("初始化后");
  
          Class<?> aClass = bean.getClass();
          if (aClass == BaseServiceImpl.class) {
              Object proxyInstance = Proxy.newProxyInstance(bean.getClass().getClassLoader(),
                      bean.getClass().getInterfaces(),
                      new InvocationHandler() {
                          @Override
                          public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                              // 具体操作内容
                              Object invoke = null;
                              if (method.getName().equals("doSome")) {
                                  invoke = (String) method.invoke(bean, args);
                                  return ((String) invoke).toUpperCase();
                              } else {
                                  return invoke;
                              }
  
                          }
                      }
              );
              return proxyInstance;
          }
          return bean;
      }
  }
  ```

- 配置

  ```xml
  <!-- bean 的处理-->
  <bean id="tec" class="com.huifer.proxy.impl.BaseServiceImpl"></bean>
  <bean class="com.huifer.util.MyBeanPostPro"></bean>
  ```

- 测试用例

  ```java
   @Test
      public void testIocDemo04(){
          BaseService so = (BaseService) context.getBean("tec");
          String s = so.doSome();
          System.out.println(s);
      }
  ```

## 思考

利用bean实例化来监控每一个接口，数据的增删改查的全局监控

---

## DI

- 实体

  ```java
  @Data
  @NoArgsConstructor
  public class Di {
  
      private String diName;
      private int[] ints;
      private List<Integer> integerList;
  }
  
  ```

  

- 配置

```xml
  <!--Di-->
    <bean id="di01" class="com.huifer.bean.Di">
        <property name="diName" value="dididididi"></property>
        <property name="ints" value="1,2,3,4,5"/>
        <property name="integerList" >
            <array>
                <value>1</value>
                <value>1</value>
                <value>1</value>
                <value>1</value>
            </array>
        </property>

    </bean>

```

- 测试

  ```java
   @Test
      public void testIocDemo05() {
          Di di = (Di) context.getBean("di01");
          System.out.println();
      }
  ```



---

## AOP

### 术语

- Aspect : 切面
- Target ： 目标对象
- Weaving : 织入
- JoinPoint : 连接点
- PointCut: 切入点
- Advice : 通知
  - 前置通知
    - 执行切面->切入点
  - 后置通知
    - 切入点-> 执行切面
  - 环绕通知
    - 执行切面-> 切入点-> 执行切面->切入点 往复循环
  - 异常通知
    - 切入点异常的切面操作
- Advisor: 顾问





### 前置通知



- 被监控方法接口编写 ， 基本接口

  ```java
  public interface BaseAopPointCut {
      /**
       * 吃饭
       */
      void eat();
  
      /**
       * 上厕所
       */
      void wc();
  }
  
  ```

  

- 基本接口的实现类

  ```java
  public class Person implements BaseAopPointCut {
      @Override
      public void eat() {
          System.out.println("吃饭了");
      }
  
      @Override
      public void wc() {
          System.out.println("上厕所了");
      }
  }
  
  ```

  

- Advice 通知，实现org.springframework.aop.MethodBeforeAdvice 即可

  ```java
  public class MyBeforAdvice implements MethodBeforeAdvice {
      /***
       * 切面
       * @param method 被监听的方法
       * @param args 方法参数
       * @param o 代理对象
       * @throws Throwable
       */
      @Override
      public void before(Method method, Object[] args, Object o) throws Throwable {
  
  
          System.out.println("洗手！！！");
  
  
  
      }
  }
  
  ```

  

- 配置

  ```xml
  <!--注册被监控的实现类 -->
  <bean id="person" class="com.huifer.aop.advice.Person"></bean>
  <!--注册通知的实现类-->
  <bean id="befor" class="com.huifer.aop.advice.MyBeforAdvice"/>
  <!--注册代理监控 对象-->
  <bean id="personProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
      <property name="target"  ref="person"></property>
      <property name="interceptorNames" value="befor"></property>
  </bean>
  ```

- 测试用例

  ```java
   @Test
      public void testIocDemo06(){
          BaseAopPointCut cut = (BaseAopPointCut) context.getBean("personProxy");
          cut.eat();
      }
  
  ```

  

- 控制台输出

  ```latex
  洗手！！！
  吃饭了
  ```

- 注意点 getBean获取的是基本接口 ，这种写法通用性高，不能针对每一个方法进行分别操作，一次操作这个接口的所有方法
- 后置顾问  org.springframework.aop.AfterReturningAdvice

---

### 顾问

通知的灵活性体现方式。

- 一般实现思路
  1. 实现org.springframework.aop.ClassFilter 来判断具体执行类
  2. 实现 org.springframework.aop.MethodMatcher 来判断具体方法
  3. 实现切入点org.springframework.aop.Pointcut
  4. 实现一个顾问 org.springframework.aop.PointcutAdvisor
  5. 实现通知 ， 此处和前置通知相同不做重复描述

- 案例说明

  ```
   男性上厕所不用带纸巾 ， 女性上厕所需要带纸巾
  ```

#### 具体实现

- 基本被监控方法

  ```java
  public interface BaseAopPointCut {
      /**
       * 吃饭
       */
      void eat();
  
      /**
       * 上厕所
       */
      void wc();
  }
  
  ```

- 具体实现类

  - 男性

    ```java
    public class Man implements BaseAopPointCut {
        @Override
        public void eat() {
            System.out.println("吃饭了");
        }
    
        @Override
        public void wc() {
            System.out.println("上厕所");
        }
    }
    
    ```

    

  - 女性

    ```java
    public class WoMan implements BaseAopPointCut {
        @Override
        public void eat() {
            System.out.println("吃饭了");
        }
    
        @Override
        public void wc() {
            System.out.println("上厕所");
        }
    }
    
    ```

    

- 类过滤器

  ```java
  /**
   * 描述:
   * 类过滤器
   *
   * @author huifer
   * @date 2019-03-03
   */
  public class MyClassFilter implements ClassFilter {
      /***
       * 判断当前实现类是否是我们的织入所相关的类
       * 本次案例中 男性上厕所不需要带纸巾，女性上厕所需要带纸巾，
       * 那么BaseAopPointCut接口应该要对女性进行判断，完成此步骤后还需要一个方法匹配器 ，
       * 再次我们只要对上厕所匹配 吃饭不需要匹配
       *
       * @param aClass 当前被拦截的类
       * @return boolean
       */
      @Override
      public boolean matches(Class<?> aClass) {
          if (aClass == WoMan.class) {
  
              return true;
          }
          return false;
      }
  }
  ```

- 方法过滤器

  ```java
  
  /**
   * 描述:
   * 方法匹配器
   *
   * @author huifer
   * @date 2019-03-03
   */
  public class MyMethodMatcher implements MethodMatcher {
  
      /**
       * 被监控接口的方法都是唯一的 使用这个方法，只根据名称判断！！！
       * {@link MyClassFilter}
       *
       * @param method 待判断的方法
       * @param aClass 目标类
       * @return boolean
       */
      @Override
      public boolean matches(Method method, Class<?> aClass) {
          if (method.getName().equals("wc")) {
              return true;
          }
          return false;
      }
  
      @Override
      public boolean isRuntime() {
          return false;
      }
  
      @Override
      public boolean matches(Method method, Class<?> aClass, Object... objects) {
          return false;
      }
  }
  
  ```

  

- 通知

  ```java
  public class MyBeforAdvice implements MethodBeforeAdvice {
      /***
       * 切面
       * @param method 被监听的方法
       * @param args 方法参数
       * @param o 代理对象
       * @throws Throwable
       */
      @Override
      public void before(Method method, Object[] args, Object o) throws Throwable {
  
  
          System.out.println("带纸巾！！！");
  
  
  
      }
  }
  
  ```

- 切点

  ```java
  @Setter
  public class MyPointCut implements Pointcut {
  
      /**
       * 类型过滤器
       */
      private ClassFilter classFilter;
      /**
       * 方法过滤器
       */
      private MethodMatcher matcher;
  
  
      /**
       * @return 返回类过滤器
       */
      @Override
      public ClassFilter getClassFilter() {
          return this.classFilter;
      }
  
  
      /**
       * @return 返回方法过滤器
       */
      @Override
      public MethodMatcher getMethodMatcher() {
          return this.matcher;
      }
  }
  
  ```

  

- 自定义顾问

  ```java
  /**
   * 描述:
   * 自定义顾问
   *
   * @author huifer
   * @date 2019-03-03
   */
  public class MyPointCutAdvisor implements PointcutAdvisor {
      /**
       * 次要业务主要业务 + 执行顺序
       * 本次案例 上厕所 + 带纸巾
       */
      private Advice advice;
      /**
       * 拦截对象+ 主要业务
       * 本次案例 女人 + 上厕所
       */
      private Pointcut pointcut;
  
      public void setAdvice(Advice advice) {
          this.advice = advice;
      }
  
      public void setPointcut(Pointcut pointcut) {
          this.pointcut = pointcut;
      }
  
      @Override
      public Pointcut getPointcut() {
          return this.pointcut;
      }
  
      @Override
      public Advice getAdvice() {
          return this.advice;
      }
  
      @Override
      public boolean isPerInstance() {
          return false;
      }
  }
  ```

- 配置

  ```xml
  <!--顾问-->
  
  <!--顾问注册-->
  <!--被监控实现类-->
  <bean id="woman" class="com.huifer.aop.advisor.WoMan"></bean>
  <!--注册 类型过滤器-->
  <bean id="classFilter" class="com.huifer.aop.advisor.MyClassFilter"/>
  <!--注册 方法过滤器-->
  <bean id="methodFilter" class="com.huifer.aop.advisor.MyMethodMatcher"/>
  <!--注册通知-->
  <bean id="wcBefor" class="com.huifer.aop.advisor.MyBeforAdvice"></bean>
  <!--注册切入点 pointCut-->
  <bean id="pointCut" class="com.huifer.aop.advisor.MyPointCut">
      <property name="classFilter" ref="classFilter"/>
      <property name="matcher" ref="methodFilter"/>
  </bean>
  <!--注册自定义顾问-->
  <bean id="myPointCutAdvisor" class="com.huifer.aop.advisor.MyPointCutAdvisor">
      <property name="pointcut" ref="pointCut"/>
      <property name="advice" ref="wcBefor"/>
  </bean>
  <!--注册代理对象工厂-->
  <bean id="woManProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
      <property name="target" ref="woman"></property>
      <property name="proxyTargetClass" value="true"/>
      <property name="interceptorNames" value="myPointCutAdvisor"/>
  </bean>
  ```

- 测试用例、

  ```java
     @Test
      public void testAopDemo02() {
          System.out.println("************************************************");
          WoMan woMan = (WoMan) context.getBean("woManProxy");
          System.out.println("==================女性吃饭==================");
          woMan.eat();
          System.out.println("==================女性上厕所==================");
          woMan.wc();
  
      }
  ```

- 控制台输出

  ```
  ==================女性吃饭==================
  吃饭了
  ==================女性上厕所==================
  带纸巾！！！
  上厕所
  ```

  

- **注意**

  - 注册的代理工厂只有woman一个target 男性不可以用类型强制住哪换获取 



### aspects

- 通知类

  ```java
  public class MyAdvice {
  
      public void log(){
          System.out.println("log");
      }
  
  }
  
  ```

- 配置

  ```xml
    <!--配置通知-->
      <bean id="myAdvice2" class="com.huifer.aop.aspects.MyAdvice"/>
  
  
  
      <!--aop配置-->
      <aop:config>
          <aop:aspect ref="myAdvice2">
              <aop:before method="log"
                          pointcut="execution(void com.huifer.comment.RoleServiceImpl.saveRole())"></aop:before>
          </aop:aspect>
  
      </aop:config>
  ```

  

- 测试类

  ```java
  
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration(locations = {"classpath:spring_comment_config.xml"})
  public class MyAdviceTest {
      @Autowired
      private RoleService roleService;
  
  
      @Test
      public void demo(){
          roleService.saveRole();
      }
  }
  ```

  

aspects 代码量可能比spring-aop 的代码量少一些

#### 注解形式

- 自定义注解

  ```java
  @Aspect
  @Component(value = "myAspect")
  public class MyAspect {
  
  
      @Before(value = "execution(* *..*.*ServiceImpl.*(..))")
      public void asBefor(){
          System.out.println("注解形式的前置通知");
      }
  
  
  
  }
  ```

  

- 配置

  ```xml
     <!--注解切面-->
      <!--扫描切面类-->
      <context:component-scan base-package="com.huifer.aop.aspects.comment"/>
  <!--开启AOP自动代理-->
      <aop:aspectj-autoproxy ></aop:aspectj-autoproxy>
  ```

- 测试类

  ```java
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration(locations = {"classpath:spring_comment_config.xml"})
  public class MyAdviceTest {
      @Autowired
      private RoleService roleService;
  
  
      @Test
      public void demo(){
          roleService.saveRole();
      }
  }
  ```



### 源码查看

#### 命名空间的源码


- org.springframework.context.support.AbstractApplicationContext
   - 	refresh方法
    	- 	ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

- org.springframework.context.support.AbstractRefreshableApplicationContext
  ```java
  @Override
  protected final void refreshBeanFactory() throws BeansException {
  	if (hasBeanFactory()) {
  		destroyBeans();
  		closeBeanFactory();
  	}
  	try {
  		DefaultListableBeanFactory beanFactory = createBeanFactory();
  		beanFactory.setSerializationId(getId());
  		customizeBeanFactory(beanFactory);
  		loadBeanDefinitions(beanFactory);
  		synchronized (this.beanFactoryMonitor) {
  			this.beanFactory = beanFactory;
  		}
  	}
  	catch (IOException ex) {
  		throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);
  	}
  }
  ```


  - loadBeanDefinitions(beanFactory)

- org.springframework.context.support.AbstractXmlApplicationContext


   - loadBeanDefinitions方法

      ```java
      	@Override
      	protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {
      		// Create a new XmlBeanDefinitionReader for the given BeanFactory.
      		XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);
      
      		// Configure the bean definition reader with this context's
      		// resource loading environment.
      		beanDefinitionReader.setEnvironment(this.getEnvironment());
      		beanDefinitionReader.setResourceLoader(this);
      		beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));
      
      		// Allow a subclass to provide custom initialization of the reader,
      		// then proceed with actually loading the bean definitions.
      		initBeanDefinitionReader(beanDefinitionReader);
      		loadBeanDefinitions(beanDefinitionReader);
      	}
      
      ```

- 一路跟踪下去找到org.springframework.beans.factory.xml.XmlBeanDefinitionReader

   ```java
       public int registerBeanDefinitions(Document doc, Resource resource) throws BeanDefinitionStoreException {
           BeanDefinitionDocumentReader documentReader = this.createBeanDefinitionDocumentReader();
           int countBefore = this.getRegistry().getBeanDefinitionCount();
           documentReader.registerBeanDefinitions(doc, this.createReaderContext(resource));
           return this.getRegistry().getBeanDefinitionCount() - countBefore;
       }
   
       protected BeanDefinitionDocumentReader createBeanDefinitionDocumentReader() {
           return (BeanDefinitionDocumentReader)BeanUtils.instantiateClass(this.documentReaderClass);
       }
   
       public XmlReaderContext createReaderContext(Resource resource) {
           return new XmlReaderContext(resource, this.problemReporter, this.eventListener, this.sourceExtractor, this, this.getNamespaceHandlerResolver());
       }
   
       public NamespaceHandlerResolver getNamespaceHandlerResolver() {
           if (this.namespaceHandlerResolver == null) {
               this.namespaceHandlerResolver = this.createDefaultNamespaceHandlerResolver();
           }
   
           return this.namespaceHandlerResolver;
       }
   
       protected NamespaceHandlerResolver createDefaultNamespaceHandlerResolver() {
           ClassLoader cl = this.getResourceLoader() != null ? this.getResourceLoader().getClassLoader() : this.getBeanClassLoader();
           return new DefaultNamespaceHandlerResolver(cl);
       }
   ```

- 源码的META-INF中有这些配置文件,里面的内容和xml中的头部benas 一样

- 

   ![1551791665379](assets/1551791665379.png)

   ![1551791682902](assets/1551791682902.png)

- 源码中说明了这些文件的使用过程不在细读

![1551791708324](assets/1551791708324.png)





- AOP相关的一些配置
  - ![1551791936202](assets/1551791936202.png)
- NamespaceHandlerSupport
  - ![1551791988617](assets/1551791988617.png)
- org.springframework.beans.factory.xml.DefaultNamespaceHandlerResolver 中的resolve方法解析具体的NamespaceHandler
  - ![1551792113426](assets/1551792113426.png)
- AopNamespaceHandler 
  - 这些elementName 就相当于我们的标签名称
  - ![1551792218841](assets/1551792218841.png)



---

## 初始化

- web容器
  - servletContext (servlet 上想问) 
- web服务器
  - servlet容器 
- spring 容器
  - application (spring 上下文 beanFectory)



### spring-web容器初始化过程

```mermaid
graph TD
 
A[web容器启动创建ContextLoaderListener , 执行 contextInitialized方法] --> B[执行ContextLoader.initWebApplicationContext 方法初始化spring 容器]
B --> C[WebApplicationContext 创建成功]
C--> D[refresh方法读取xml配置]
D --> E[spring 容器保存到 ServletContext 和 ContextLoaderListener]


```

```
	org.springframework.web.context.ContextLoaderListener.contextInitialized()

    org.springframework.web.context.ContextLoader
    initWebApplicationContext()
    configureAndRefreshWebApplicationContext()
    customizeContext()
    createWebApplicationContext() // 创建了整个web的工厂
    
    org.springframework.context.support.AbstractApplicationContext.refresh()
```





### WebApplication 初始化过程

```mermaid
graph TD
A[读取web.xml] --> B[contextClass 标签]
B--yes-->C[spring 中的关系创建具体的class 对象]
B--no -->D[读取ContextLoader.properties配置创建具体class对象]
D --> E[根据Class 对象创建实例]
```



### spring 容器初始化过程

```mermaid
graph TD
a[读取spring-config.xml] --> b[创建beans]
b --> c[读取web.xml]
c --> d[设置applicationContext]
d --> e[解析xml]
e --> f[注册]

```

---

##  注解

#### demo

- 配置

```xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c" xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation=" http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!--组建扫描器 -->
    <!--组建扫描器：扫描带有spring注解的类，加载到 BeanDefinition中-->
    <context:component-scan base-package="com.huifer.comment"/>

</beans>
```



- bean

```java
package com.huifer.comment;

import org.springframework.stereotype.Component;

/**
 * 描述:
 *
 * @author huifer
 * @date 2019-03-03
 */
@Component(value = "userService")
public class UserServiceImpl implements UserService {


    @Override
    public void saveUser() {
        System.out.println("注解配置");
    }

}

```

- 测试用例

```java
public class SpringComment {
private ClassPathXmlApplicationContext context;

    @Before
    public void init() {
    	context = new ClassPathXmlApplicationContext("spring_comment_config.xml");
    }


    @Test
    public void testCommentDemo01() {
   	 	UserService bean = context.getBean(UserService.class);
    	bean.saveUser();

    }

}

```

  

####  常用注解

- @Component 
  - 组件注解
  - 属性value 含义：唯一标识

- @Controller
  - 表现层注解
- @Service
  - 业务层注解
- @Repository
  - 持久层注解

#### di

- @Autowired
  - 默认按类型装配 (byType)
- @Qualifier
  - 配合 @Autowired
- @Resource
  - 默认按名称装配(byName)
  - 也可以按照类型寻找
- @value

```java
  
@Component(value = "bt")
public class BeanT {
    public double i = 10;
}


@Autowired
    private BeanT bt;


    @Resource(name = "bt")
    private BeanT btt;

    @Value(value = "1222")
    private int dc;

 @Value(value = "${data.pc}")
    private String pc;
```

```xml
    <context:property-placeholder location="classpath:data.properties"></context:property-placeholder>

```

```properties
data.pc=win10
```

#### context 的源码

```java
public class ContextNamespaceHandler extends NamespaceHandlerSupport {

	@Override
	public void init() {
		registerBeanDefinitionParser("property-placeholder", new PropertyPlaceholderBeanDefinitionParser());
		registerBeanDefinitionParser("property-override", new PropertyOverrideBeanDefinitionParser());
		registerBeanDefinitionParser("annotation-config", new AnnotationConfigBeanDefinitionParser());
		registerBeanDefinitionParser("component-scan", new ComponentScanBeanDefinitionParser());
		registerBeanDefinitionParser("load-time-weaver", new LoadTimeWeaverBeanDefinitionParser());
		registerBeanDefinitionParser("spring-configured", new SpringConfiguredBeanDefinitionParser());
		registerBeanDefinitionParser("mbean-export", new MBeanExportBeanDefinitionParser());
		registerBeanDefinitionParser("mbean-server", new MBeanServerBeanDefinitionParser());
	}

}

```

---

## junit整合

```java
 <!--spring junit-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
        </dependency>
```

注意点如果这样配置会出现一个异常

Caused by: java.lang.IllegalStateException: SpringJUnit4ClassRunner requires JUnit 4.12 or higher.

```java

java.lang.ExceptionInInitializerError
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
	at org.junit.internal.builders.AnnotatedBuilder.buildRunner(AnnotatedBuilder.java:29)
	at org.junit.internal.builders.AnnotatedBuilder.runnerForClass(AnnotatedBuilder.java:21)
	at org.junit.runners.model.RunnerBuilder.safeRunnerForClass(RunnerBuilder.java:59)
	at org.junit.internal.builders.AllDefaultPossibilitiesBuilder.runnerForClass(AllDefaultPossibilitiesBuilder.java:26)
	at org.junit.runners.model.RunnerBuilder.safeRunnerForClass(RunnerBuilder.java:59)
	at org.junit.internal.requests.ClassRequest.getRunner(ClassRequest.java:26)
	at org.junit.internal.requests.FilterRequest.getRunner(FilterRequest.java:31)
	at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:49)
	at com.intellij.rt.execution.junit.IdeaTestRunner$Repeater.startRunnerWithArgs(IdeaTestRunner.java:47)
	at com.intellij.rt.execution.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:242)
	at com.intellij.rt.execution.junit.JUnitStarter.main(JUnitStarter.java:70)
Caused by: java.lang.IllegalStateException: SpringJUnit4ClassRunner requires JUnit 4.12 or higher.
	at org.springframework.util.Assert.state(Assert.java:73)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.<clinit>(SpringJUnit4ClassRunner.java:104)
	... 15 more
```

查看一下 spring-test-version.pom文件中有这么一段，所以修改pom文件

```xml
   <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>compile</scope>
      <optional>true</optional>
    </dependency>
    <dependency>
```

- 修改后正确的配置

  ```xml
   <!--spring junit-->
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-test</artifactId>
              <version>5.1.5.RELEASE</version>
          </dependency>
  
          <dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>4.12</version>
          </dependency>
  ```

  

- 测试类编写

  ```java
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration(locations = {"classpath:spring_comment_config.xml"})
  public class SpringJunit {
  
  
      @Resource
      ApplicationContext ctx;
  
      @Autowired
      private UserService userService;
  
  
      @Test
      public void demo(){
          userService.saveUser();
      }
  }
  
  ```

  





---

## spring 事务



