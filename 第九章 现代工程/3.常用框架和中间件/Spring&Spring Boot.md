# Spring & Spring Boot

写这篇文章主要是为了应对Java面试，我愿将其称之为面向面试学习——Interview-oriented learning。官网：https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/spring-core.html

## Bean

在Spring相关的技术文章中，`bean`是一个被反复提及的概念。这是一个不太好翻译的概念，就像`socket`一样。（我至今仍然觉得把`socket`翻译为`套接字`很蠢！）

以下是Spring官网对Bean的解释：

> In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC *container* are called *beans*. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container. Otherwise, a bean is simply one of many objects in your application. Beans, and the *dependencies* among them, are reflected in the *configuration metadata* used by a container.

简单来说：bean指的是一个由Spring IoC容器管理的对象。

### Bean的生命周期

Bean 的生命周期概括起来就是 4 个阶段：

1. 实例化（Instantiation）：实例化一个bean。
2. 属性赋值（Populate）：设置bean的属性和依赖。
3. 初始化（Initialization）：检查并配置`*Aware`相关接口->`BeanPostProcessor`的初始化前置方法->`InitializingBean`初始化方法和`init-method`自定义初始化方法->`BeanPostProcessor`的初始化后置方法。
4. 销毁（Destruction）：bean在使用前会注册销毁方法，使用结束后执行。

## 控制反转（IoC）

IoC是Spring中的一个思想。在常规的面向对象编程中，对象的创建、初始化等行为都是由软件开发人员控制。在Spring中，对象的创建、初始化和销毁等生命周期全部交给了Spring IoC容器去管理。

常见的IoC实现方式包括`依赖注入Dependency Injection，简称DI）`和`依赖查找（Dependency Lookup）`。Spring是通过`DI`来实现的。

Spring官网https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/beans.html

### 容器配置

有三种方法可以配置Spring IoC容器的元数据：

1. 基于XML：官网给出了如下示例代码。

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd">
   
     <bean id="..." class="...">
       <!-- collaborators and configuration for this bean go here -->
     </bean>
   
     <bean id="..." class="...">
       <!-- collaborators and configuration for this bean go here -->
     </bean>
   
     <!-- more bean definitions go here -->
   
   </beans>
   ```

   

2. 基于注解（Annotation-based）：使用`@Controller`、`@Service`、`@Repository`、`@Component`注解。

3. 基于Java（Java-based）：使用`@Configuration`注释类和 `@Bean`注释方法。

## 面向切面编程（AOP）

在实际业务开发过程中，我们常常需要对接口或者内部方法的入参和出参增加日志。在OOP的思想下只能对方法逐个改造，代码侵入性太强，这时就可以考虑使用AOP。（AOP待`第四章 编程语言和思想`中细讲）Spring 框架通过定义切面，拦截切点实现了不同业务模块的解耦。

### Spring AOP使用流程

以常用的注解式拦截为例：

1. 创建自定义注解；

   ```java
   /**
    * Action: 定义拦截规则的注解
    */
   @Target(ElementType.METHOD)
   @Retention(RetentionPolicy.RUNTIME)
   @Documented
   public @interface Action {
       // 拦截规则的名称
       String name();
   }
   ```

2. 在业务方法上增加自定义注解；

3. 使用@Aspect声明一个切面；

4. 为切面增加方法，使用`@Before`、`@Around`、`@After`等定义拦截规则；

5. 定义配置类，扫描包。

### Spring AOP类型

共五种类型：

1. 前置通知(@Before) 
2. 返回通知(@AfterReturning) 
3. 异常通知(@AfterThrowing)
4. 后置通知(@After)
5. 环绕通知(@Around) 

执行顺序：@Around -> @Before -> targetMethod -> @Around -> @After -> @AfterReturning/@AfterThrowing。可以看出`@Around`优先级最高。