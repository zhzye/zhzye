---
title: spring-AspectJ
tags:
  - spring
  - aop
  - AspectJ
originContent: "# 使用方式\n\n- xml\n- 注解\n\n# what\n\n- 基于java语言的aop框架\n- spring2.0 整合aspectJ\n- @AspectJ是aspectJ1.5新增功能，通过JDK5注解技术，允许直接在Bean类中定义切面\n\n# 依赖\n\n``` xml\n    <dependency>\n      <groupId>org.springframework</groupId>\n      <artifactId>spring-aop</artifactId>\n      <version>${spring.version}</version>\n    </dependency>\n    <dependency>\n      <groupId>aopalliance</groupId>\n      <artifactId>aopalliance</artifactId>\n      <version>1.0</version>\n    </dependency>\n\t<dependency>\n      <groupId>org.springframework</groupId>\n      <artifactId>spring-aspects</artifactId>\n      <version>${spring.version}</version>\n    </dependency>\n    <dependency>\n      <groupId>org.aspectj</groupId>\n      <artifactId>aspectjweaver</artifactId>\n      <version>1.9.2</version>\n    </dependency>\n```\n\n# 环境准备\n\n## xml\n\n``` xml\n<aop:aspectj-autoproxy/>\n```\n\n注解一个切面`@Aspect`，用下述标签定义通知（advice）\n\n\n# 通知类型\n\n- @Before\n\t- 前置通知\n\t\t- BeforeAdvice\n- @AfterReturning\n\t- 后置通知\n\t\t- AfterReturningAdvice\n- @Around\n\t- 环绕通知\n\t\t- MethodIntercepter\n- @AfterThrowing\n\t- 异常抛出通知\n\t\t- ThrowAdvice\n- @After\n\t- 最终执行\n- @DeclareParents\n\t- 引介通知\n\t\t- IntroductionIntercepter\n\n# aspectj提供execution表达式，必须精通\n\n语法：execution(<访问修饰符>?<返回类型><方法名>(<参数>)<异常>)\n\n- 所有类public方法\n\t- execution(public * *(..))\n- 指定包所有方法\n\t- execution(* com.imooc.dao.*(..)) `不含子包`\n- 包和子包下所有类\n\t- execution(* com.imooc.dao..*(..))`包和子包下所有类`\n- 指定类所有方法\n\t- execution(* com.imooc.service.UserService.*(..))\n- 指定特定接口(GenericDao)\n\t- execution(* com.imooc.dao.GenericDao+.*(..))\n- 匹配所有save\n\t- execution(* save*(..))\n\n# 为advice增加注入joinpoint参数\n\n``` java\n    @Before(value = \"execution(* sa*(..))\")\n    public void before(JoinPoint joinPoint) {\n        System.out.println(\"before注入\" + joinPoint);\n    }\n```\n\n# 后置通知，通过配置返回值参数，可以获取执行后的return\n\n``` java\n    @AfterReturning(value = \"execution(* sa*(..))\", returning = \"result\")\n    public void after(JoinPoint joinPoint, Object result) {\n        System.out.println(\"after注入 \" + joinPoint + \" \" + result);\n    }\n```\n\n# 环绕通知\n\n如果`ProceedingJoinPoint`不被调用proceed()方法，则被阻止运行了\n\n``` java\n    @Around(value = \"execution(* hi(..))\")\n    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {\n        System.out.println(\"环绕===before\");\n        Object object = proceedingJoinPoint.proceed();\n        System.out.println(\"环绕===after\");\n        return object;\n    }\n```\n\n# 异常抛出通知\n\n如果存在运行时异常，会被触发，如:`1/0`\n\n``` java\n    @AfterThrowing(value = \"execution(* exeThrowException(..))\", throwing = \"e\")\n    public void afterThrow(Throwable e) {\n        System.out.println(\"异常抛出通知 e \" + e.getMessage());\n    }\n```\n\n# 最终通知\n\n无论是否包含异常都会被执行\n\n``` java\n    @After(value = \"execution(* exeThrowException(..))\")\n    public void finalDO() {\n        System.out.println(\"最终通知\");\n    }\n```\n\n# @Pointcut为切点命名\n\n在aspect切面中定义切点不是不可以，但是维护起来比较麻烦，不可复用，所以用@Poincut注解支持\n\n## 定义\n切点方法：\n\nprivate void xx()\n\nxx即为切点名\n\nJUST ACTION!\n定义切点:\n\n``` java\n    @Pointcut(value = \"execution(* say(..))\")\n    private void sayPointcut() {}\n```\n\n使用切点：\n``` java\n    @Before(value = \"sayPointcut()\")\n    public void before(JoinPoint joinPoint) {\n        System.out.println(\"before注入...\" + joinPoint);\n    }\n```\n\n## 运算\n\n多个切点，可以使用`||`连接\n\n"
categories:
  - spring
toc: false
date: 2019-02-03 20:46:33
---

话不多说，先扔个代码库，能直接运行，看不懂这个wiki，去testcase调试：https://github.com/zhzye/spring/tree/master/aspectJ
# 使用方式

- xml
- 注解

# what

- 基于java语言的aop框架
- spring2.0 整合aspectJ
- @AspectJ是aspectJ1.5新增功能，通过JDK5注解技术，允许直接在Bean类中定义切面

# 依赖

``` xml
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>aopalliance</groupId>
      <artifactId>aopalliance</artifactId>
      <version>1.0</version>
    </dependency>
	<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.9.2</version>
    </dependency>
```

# 环境准备

## xml

``` xml
<aop:aspectj-autoproxy/>
```

注解一个切面`@Aspect`，用下述标签定义通知（advice）


# 通知类型

- @Before
	- 前置通知
		- BeforeAdvice
- @AfterReturning
	- 后置通知
		- AfterReturningAdvice
- @Around
	- 环绕通知
		- MethodIntercepter
- @AfterThrowing
	- 异常抛出通知
		- ThrowAdvice
- @After
	- 最终执行
- @DeclareParents
	- 引介通知
		- IntroductionIntercepter

# aspectj提供execution表达式，必须精通

语法：execution(<访问修饰符>?<返回类型><方法名>(<参数>)<异常>)

- 所有类public方法
	- execution(public * *(..))
- 指定包所有方法
	- execution(* com.imooc.dao.*(..)) `不含子包`
- 包和子包下所有类
	- execution(* com.imooc.dao..*(..))`包和子包下所有类`
- 指定类所有方法
	- execution(* com.imooc.service.UserService.*(..))
- 指定特定接口(GenericDao)
	- execution(* com.imooc.dao.GenericDao+.*(..))
- 匹配所有save
	- execution(* save*(..))

# 为advice增加注入joinpoint参数

``` java
    @Before(value = "execution(* sa*(..))")
    public void before(JoinPoint joinPoint) {
        System.out.println("before注入" + joinPoint);
    }
```

# 后置通知，通过配置返回值参数，可以获取执行后的return

``` java
    @AfterReturning(value = "execution(* sa*(..))", returning = "result")
    public void after(JoinPoint joinPoint, Object result) {
        System.out.println("after注入 " + joinPoint + " " + result);
    }
```

# 环绕通知

如果`ProceedingJoinPoint`不被调用proceed()方法，则被阻止运行了

``` java
    @Around(value = "execution(* hi(..))")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("环绕===before");
        Object object = proceedingJoinPoint.proceed();
        System.out.println("环绕===after");
        return object;
    }
```

# 异常抛出通知

如果存在运行时异常，会被触发，如:`1/0`

``` java
    @AfterThrowing(value = "execution(* exeThrowException(..))", throwing = "e")
    public void afterThrow(Throwable e) {
        System.out.println("异常抛出通知 e " + e.getMessage());
    }
```

# 最终通知

无论是否包含异常都会被执行

``` java
    @After(value = "execution(* exeThrowException(..))")
    public void finalDO() {
        System.out.println("最终通知");
    }
```

# @Pointcut为切点命名

在aspect切面中定义切点不是不可以，但是维护起来比较麻烦，不可复用，所以用@Poincut注解支持

## 定义
切点方法：

private void xx()

xx即为切点名

JUST ACTION!
定义切点:

``` java
    @Pointcut(value = "execution(* say(..))")
    private void sayPointcut() {}
```

使用切点：
``` java
    @Before(value = "sayPointcut()")
    public void before(JoinPoint joinPoint) {
        System.out.println("before注入..." + joinPoint);
    }
```

## 运算

多个切点，可以使用`||`连接


# xml方式

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"> <!-- bean definitions here -->
    <bean id="person" class="com.zhzye.aspectj.Person"/>
    <bean id="personAspect" class="com.zhzye.aspectj.PersonBeforeAdviceXml"/>
    <aop:config>
        <!--切点-->
        <aop:pointcut id="personAdviceBefore" expression="execution(* hi(..))"/>
        <aop:pointcut id="personAdviceRound" expression="execution(* say(..))"/>
        <aop:pointcut id="personAdviceAfterReturn" expression="execution(* hi(..))"/>
        <aop:pointcut id="personAdviceAfterThrow" expression="execution(* exeThrowException(..))"/>
        <!--切面-->
        <aop:aspect ref="personAspect">
            <aop:before method="before" pointcut-ref="personAdviceBefore"/>
            <aop:around method="around" pointcut-ref="personAdviceRound"/>
            <aop:after-returning method="after" pointcut-ref="personAdviceAfterReturn" returning="result"/>
            <aop:after-throwing method="afterThrow" pointcut-ref="personAdviceAfterThrow" throwing="e"/>
            <aop:after method="finalDO" pointcut-ref="personAdviceAfterThrow"/>
        </aop:aspect>
    </aop:config>
</beans>
```

这种方式与注解方式使用都是一致的，这里不概述了。