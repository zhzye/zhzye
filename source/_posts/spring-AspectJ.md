---
title: spring-AspectJ
tags:
  - spring
  - aop
  - AspectJ
originContent: "# 使用方式\n\n- xml\n- 注解\n\n# what\n\n- 基于java语言的aop框架\n- spring2.0 整合aspectJ\n- @AspectJ是aspectJ1.5新增功能，通过JDK5注解技术，允许直接在Bean类中定义切面\n\n# 依赖\n\n``` xml\n    <dependency>\n      <groupId>org.springframework</groupId>\n      <artifactId>spring-aop</artifactId>\n      <version>${spring.version}</version>\n    </dependency>\n    <dependency>\n      <groupId>aopalliance</groupId>\n      <artifactId>aopalliance</artifactId>\n      <version>1.0</version>\n    </dependency>\n\t<dependency>\n      <groupId>org.springframework</groupId>\n      <artifactId>spring-aspects</artifactId>\n      <version>${spring.version}</version>\n    </dependency>\n    <dependency>\n      <groupId>org.aspectj</groupId>\n      <artifactId>aspectjweaver</artifactId>\n      <version>1.9.2</version>\n    </dependency>\n```\n\n# 环境准备\n\n## xml\n\n``` xml\n<aop:aspectj-autoproxy/>\n```\n\n注解一个切面`@Aspect`，用下述标签定义通知（advice）\n\n\n# 通知类型\n\n- @Before\n\t- 前置通知\n\t\t- BeforeAdvice\n- @AfterReturning\n\t- 后置通知\n\t\t- AfterReturningAdvice\n- @Around\n\t- 环绕通知\n\t\t- MethodIntercepter\n- @AfterThrowing\n\t- 异常抛出通知\n\t\t- ThrowAdvice\n- @After\n\t- 最终执行\n- @DeclareParents\n\t- 引介通知\n\t\t- IntroductionIntercepter\n\n# aspectj提供execution表达式，必须精通\n\n语法：execution(<访问修饰符>?<返回类型><方法名>(<参数>)<异常>)\n\n- 所有类public方法\n\t- execution(public * *(..))\n- 指定包所有方法\n\t- execution(* com.imooc.dao.*(..)) `不含子包`\n- 包和子包下所有类\n\t- execution(* com.imooc.dao..*(..))`包和子包下所有类`\n- 指定类所有方法\n\t- execution(* com.imooc.service.UserService.*(..))\n- 指定特定接口(GenericDao)\n\t- execution(* com.imooc.dao.GenericDao+.*(..))\n- 匹配所有save\n\t- execution(* save*(..))\n\n# 为advice增加注入joinpoint参数\n\n``` java\n    @Before(value = \"execution(* sa*(..))\")\n    public void before(JoinPoint joinPoint) {\n        System.out.println(\"before注入\" + joinPoint);\n    }\n```\n\n# 后置通知，通过配置返回值参数，可以获取执行后的return\n\n``` java\n@AfterReturning(value = \"execution(* sa*(..))\", returning = \"result\")\n    public void after(JoinPoint joinPoint, Object result) {\n        System.out.println(\"after注入 \" + joinPoint + \" \" + result);\n    }\n```\n"
categories:
  - spring
toc: false
date: 2019-02-03 20:46:33
---

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
