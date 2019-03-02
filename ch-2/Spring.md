# Spring

## 依赖

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

## 简单操作

### beans

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

### 模拟一个beans获取

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

## spring scope  

- singleton 
  -  单例模式， spring容器启动就创建，每次都是同一个实例对象
- prototype : 
  - 在调用getBeans 创建， 每次都是全新实例

### 修改 beansFactory

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

## 动态工厂

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



### 修改 beansFactory

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

## 静态工厂

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

## BeanPostProcessor 接口

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

### 思考

利用bean实例化来监控每一个接口，数据的增删改查的全局监控

---

