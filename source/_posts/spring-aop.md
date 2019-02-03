---
title: spring aop
tags:
  - spring
  - aop
originContent: "## aop\t\n\n代码库：https://github.com/zhzye/spring/tree/master/aop\n\n- 概述\n\t- aspect oriented programming\n\t- 预编译方式和运行期动态代理实现程序功能的统一维护的一种技术\n\t- oop延续，函数式变成的一种衍生范型\n\t- 隔离业务逻辑各个部分，降低耦合度，提升程序的可重用性\n\t- 采取横向抽取机制，取代了传统纵向继承体系重复性代码（性能检测、事务管理、安全检查、缓存）\n\t- 使用横向抽取代替纵向继承\n\t- Spring AOP使用java实现，不需要专门的编译过程和类加载器，在运行期通过代理方式向目标类织入增强代码\n- 底层实现\n- 传统AOP SPRING\n\t- 不带切入点的切面\n\t- 带切入点的切面\n- 传统AOP自动代理\n\t- 基于Bean名称的自动代理\n\t- 基于切面信息的自动代理\n\n\n## 新的概念\n\n- 类加载器\n- 代理模式\n- jdk动态代理\n- 织入\n- 横向抽取\n- 函数式变成\n- aspect\n- 运行期动态代理\n\n## 实现\n\n总的需要注意的，无论使用jdk还是cglib来实现动态代理，都是对目标类生成子类的方式，所以不可以在类或者方法前面使用`final修饰`\n\n### jdkproxy动态代理\n\n实现proxy核心代码如下：\n``` java\npublic class UserDaoProxy implements InvocationHandler {\n    private UserDao userDao;\n    public UserDaoProxy(UserDao userDao) {\n        this.userDao = userDao;\n    }\n    public Object createProxy() {\n        return Proxy.newProxyInstance(userDao.getClass().getClassLoader(), userDao.getClass().getInterfaces(), this);\n    }\n\n    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {\n\t// !!!!这里最容易出错，看好第一个参数是userDao，而不是proxy\n        return method.invoke(userDao, args);\n    }\n}\n```\n使用proxy代码\n``` java\nUserDao userDao = new UserDaoImpl();\nUserDao proxy = (UserDao) new UserDaoProxy(userDao).createProxy();\nproxy.saveUser();\nproxy.deleteUser();\nproxy.updateUser();\nproxy.getUser();\n```\n### cglib\n区别于jdk动态代理方式，必须要面向接口编程，存在接口，我们这里用的是一个实现类没有实现任何接口的实现类\n\n实现类\n\n``` java\npackage com.zhzye.aop;\n\npublic class UserDaoWithoutInterface {\n    public void saveUser() {\n        System.out.println(\"save\");\n    }\n\n    public void getUser() {\n        System.out.println(\"get user\");\n    }\n}\n\n```\n\ncglib实现\n\n``` java\npackage com.zhzye.aop;\n\nimport org.springframework.cglib.proxy.Enhancer;\nimport org.springframework.cglib.proxy.MethodInterceptor;\nimport org.springframework.cglib.proxy.MethodProxy;\n\nimport java.lang.reflect.Method;\n\n/**\n * 这种proxy使用了cglib\n * 属于字节码增强技术\n * 目前cglib的支持在spring组件中\n */\npublic class UserDaoCGLibProxy implements MethodInterceptor {\n    private final Object object;\n\n    public UserDaoCGLibProxy(Object object) {\n        this.object = object;\n    }\n\n    public Object createProxy() {\n        Enhancer enhancer = new Enhancer();\n        enhancer.setSuperclass(object.getClass());\n        enhancer.setCallback(this);\n        Object proxy = enhancer.create();\n        return proxy;\n    }\n\n    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {\n        if (method.getName().equals(\"saveUser\")) {\n            System.out.println(\"save user 增强\");\n        }\n        return methodProxy.invokeSuper(o, objects);\n    }\n}\n\n\n```\n\n`return methodProxy.invokeSuper(o, objects);`这里注意是第一个参数，而不是接口的实现调用，区别jdk dynamic proxy模式\n\n使用：\n\n``` java\nUserDaoWithoutInterface userDao = new UserDaoWithoutInterface();\nUserDaoWithoutInterface proxy = (UserDaoWithoutInterface) new UserDaoCGLibProxy(userDao).createProxy();\nproxy.saveUser();\nproxy.getUser();\n```\n\n## 增强 advice\n\n- 类型\n\t- 前置通知\n\t\t- 在目标方法执行前实施增强\n\t\t- MethodBeforeAdvice\n\t- 后置通知\n\t- 环绕通知\n\t\t- 在前后实施增强\n\t\t- MethodIntercepter\n\t- 异常通知\n\t\t- 跑出异常实施增强\n\t- 引介通知`暂时不需要了解`\n\t\t- 在`目标类`中添加一些新的方法和属性\n\n\n## aop切面类型\n\n- advisor\n\t- 一般切面，advice本身就是一个切面，对目标类所有方法进行拦截inspect\n- PointcutAdvisor\n\t- 具有切点的切面，可以指定拦截目标类的方法 \n- IntroductionAdvisor`暂时不用了解`\n\t- 引介切面，对引介通知而使用切面\n\n## 使用spring内部定义aop\n\n### 依赖\n\n- aopalliance\n\t- aopalliance\n\t\t- 1.0\n\t\t- aop联盟\n- org.springframework\n\t- spring-aop\n\t\t- 5.1.4.RELEASE\n\t- spring-test\n\t\t- 5.1.4.RELEASE\n\n\n### Spring传统aop使用\n\n\n#### 一般切面\n\n普通advice作为切面，将目标类所有方法进行拦截，不够灵活，不能指定目标类的特定连接点\n\n如何使用增强？先来记住几个关键词\n\n- ProxyFactoryBean\n\t- 代理类创建的工厂类\n\t- DI属性\n\t\t- target\n\t\t- proxyInterfaces\n\t\t- interceptorNames\n\t\t\t- 需要weaving目标的advice\n\t\t- proxyTargetClass\n\t\t\t- true使用cglib\n\t\t\t- false使用jdk\n\t\t- singleton\n\t\t\t- 返回代理对象，是否为单例，默认为单例\n\t\t- optimize\n\t\t\t- true强制使用cglib\n\n\n``` java\npackage com.zhzye.aop.springway;\n\nimport org.springframework.aop.MethodBeforeAdvice;\n\nimport java.lang.reflect.Method;\n\npublic class PersonBeforeAdvice implements MethodBeforeAdvice {\n    public void before(Method method, Object[] objects, Object o) throws Throwable {\n        System.out.println(\"before advice增强\");\n    }\n}\n\n```\n\n\n\n### PointcutAdvisor\n\n切点切面，对目标类指定连接点进行切面\n\n- PointcutAdvisor\n\t- 实现类\n\t\t- DefaultPointcutAdvisor\n\t\t\t- 最常用切面，组合Pointcut和Advice组合定义切面\n\t\t\t\t- MethodIntercepter\n\t\t\t\t\t- invocation.proceed()\n\t\t\t- DI属性\n\t\t\t\t- target\n\t\t\t\t- proxyTargetClass\n\t\t\t\t\t- true\n\t\t\t\t- intercepterNames\n\t\t\t\t\t- 不能是MethodIntercepter接口实现类了\n\t\t\t\t\t\t- 因为一般的切面是使用通知作为切面，我们要对某个目标类方法增强，需要配置带切入点的切面\n\t\t\t\t\t\t\t- JdkRegexpMethodPointcut\n\t\t\t\t\t\t\t\t- 正则表达式切点\n\t\t\t\t\t\t\t\t\t- DI属性\n\t\t\t\t\t\t\t\t\t\t- pattern\n\t\t\t\t\t\t\t\t\t\t\t- `.*user.*,.*delete.*`\n\t\t\t\t\t\t\t\t\t\t- advice\n\t\t\t\t\t\t\t\t\t\t\t- MethodIntercepter接口子类\n\n\n## 自动创建代理\n\n之前介绍的方式，每个代理都需要配置一大堆的关于ProxyFactoryBean的工厂信息，如果代理多后，开发维护成本都非常的高\n\n- BeanNameAutoProxyCreator\n\t- Bean名称\n- DefaultAdvisorAutoProxyCreator\n\t- 切面信息创建的代理\n\t\t- Advisor本身信息\n- AnnotationAwareAspectJAutoProxyCreator\n\t- 基于Bean中AspectJ注解\n\n### BeanNameAutoProxyCreator 基于Bean名称给自动代理机制\n\n所有以DAO结尾的Bean产生代理，其实底层依据`BeanPostProcessor`在Bean创建时候自动创建了代理\n\n- BeanNameAutoProxyCreator\n\t- DI属性\n\t\t- beanNames\n\t\t\t- *Dao\n\t- interceptorNames\n\t\t- <property name=\"\" value=\"\"/>\n\n### DefaultAdvisorAutoProxyCreator\n\n基于切面信息代理 \n\n- RegexpMethodPointcutAdvisor\n\t- pattern\n\t- advice\n- DefaultAdvisorAutoProxyCreator"
categories:
  - spring
toc: false
date: 2019-01-31 11:05:29
---

## aop	

代码库：https://github.com/zhzye/spring/tree/master/aop

- 概述
	- aspect oriented programming
	- 预编译方式和运行期动态代理实现程序功能的统一维护的一种技术
	- oop延续，函数式变成的一种衍生范型
	- 隔离业务逻辑各个部分，降低耦合度，提升程序的可重用性
	- 采取横向抽取机制，取代了传统纵向继承体系重复性代码（性能检测、事务管理、安全检查、缓存）
	- 使用横向抽取代替纵向继承
	- Spring AOP使用java实现，不需要专门的编译过程和类加载器，在运行期通过代理方式向目标类织入增强代码
- 底层实现
- 传统AOP SPRING
	- 不带切入点的切面
	- 带切入点的切面
- 传统AOP自动代理
	- 基于Bean名称的自动代理
	- 基于切面信息的自动代理


## 新的概念

- 类加载器
- 代理模式
- jdk动态代理
- 织入
- 横向抽取
- 函数式变成
- aspect
- 运行期动态代理

## 实现

总的需要注意的，无论使用jdk还是cglib来实现动态代理，都是对目标类生成子类的方式，所以不可以在类或者方法前面使用`final修饰`

### jdkproxy动态代理

实现proxy核心代码如下：
``` java
public class UserDaoProxy implements InvocationHandler {
    private UserDao userDao;
    public UserDaoProxy(UserDao userDao) {
        this.userDao = userDao;
    }
    public Object createProxy() {
        return Proxy.newProxyInstance(userDao.getClass().getClassLoader(), userDao.getClass().getInterfaces(), this);
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
	// !!!!这里最容易出错，看好第一个参数是userDao，而不是proxy
        return method.invoke(userDao, args);
    }
}
```
使用proxy代码
``` java
UserDao userDao = new UserDaoImpl();
UserDao proxy = (UserDao) new UserDaoProxy(userDao).createProxy();
proxy.saveUser();
proxy.deleteUser();
proxy.updateUser();
proxy.getUser();
```
### cglib
区别于jdk动态代理方式，必须要面向接口编程，存在接口，我们这里用的是一个实现类没有实现任何接口的实现类

实现类

``` java
package com.zhzye.aop;

public class UserDaoWithoutInterface {
    public void saveUser() {
        System.out.println("save");
    }

    public void getUser() {
        System.out.println("get user");
    }
}

```

cglib实现

``` java
package com.zhzye.aop;

import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.MethodInterceptor;
import org.springframework.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

/**
 * 这种proxy使用了cglib
 * 属于字节码增强技术
 * 目前cglib的支持在spring组件中
 */
public class UserDaoCGLibProxy implements MethodInterceptor {
    private final Object object;

    public UserDaoCGLibProxy(Object object) {
        this.object = object;
    }

    public Object createProxy() {
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(object.getClass());
        enhancer.setCallback(this);
        Object proxy = enhancer.create();
        return proxy;
    }

    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        if (method.getName().equals("saveUser")) {
            System.out.println("save user 增强");
        }
        return methodProxy.invokeSuper(o, objects);
    }
}


```

`return methodProxy.invokeSuper(o, objects);`这里注意是第一个参数，而不是接口的实现调用，区别jdk dynamic proxy模式

使用：

``` java
UserDaoWithoutInterface userDao = new UserDaoWithoutInterface();
UserDaoWithoutInterface proxy = (UserDaoWithoutInterface) new UserDaoCGLibProxy(userDao).createProxy();
proxy.saveUser();
proxy.getUser();
```

## 增强 advice

- 类型
	- 前置通知
		- 在目标方法执行前实施增强
		- MethodBeforeAdvice
	- 后置通知
	- 环绕通知
		- 在前后实施增强
		- MethodIntercepter
	- 异常通知
		- 跑出异常实施增强
	- 引介通知`暂时不需要了解`
		- 在`目标类`中添加一些新的方法和属性


## aop切面类型

- advisor
	- 一般切面，advice本身就是一个切面，对目标类所有方法进行拦截inspect
- PointcutAdvisor
	- 具有切点的切面，可以指定拦截目标类的方法 
- IntroductionAdvisor`暂时不用了解`
	- 引介切面，对引介通知而使用切面

## 使用spring内部定义aop

### 依赖

- aopalliance
	- aopalliance
		- 1.0
		- aop联盟
- org.springframework
	- spring-aop
		- 5.1.4.RELEASE
	- spring-test
		- 5.1.4.RELEASE


### Spring传统aop使用


#### 一般切面

普通advice作为切面，将目标类所有方法进行拦截，不够灵活，不能指定目标类的特定连接点

如何使用增强？先来记住几个关键词

- ProxyFactoryBean
	- 代理类创建的工厂类
	- DI属性
		- target
		- proxyInterfaces
		- interceptorNames
			- 需要weaving目标的advice

		- 选参数
			- proxyTargetClass
				- true使用cglib
				- false使用jdk
			- singleton
				- 返回代理对象，是否为单例，默认为单例
			- optimize
				- true强制使用cglib

一般切面代码


``` java
package com.zhzye.aop.springway;

import org.springframework.aop.MethodBeforeAdvice;

import java.lang.reflect.Method;

public class PersonBeforeAdvice implements MethodBeforeAdvice {
    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println("before advice增强");
    }
}

```

ProxyFactoryBean使用
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="person" class="com.zhzye.aop.springway.PersonImpl"/>
    <bean id="myBeforeAdvice" class="com.zhzye.aop.springway.PersonBeforeAdvice"/>
    <bean id="personProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="person"/>
	<!-- jdk方式 -->
        <property name="proxyInterfaces" value="com.zhzye.aop.springway.Person"/>
	<!-- 这里是名称，不是引用 -->
        <property name="interceptorNames" value="myBeforeAdvice"/>
    </bean>
</beans>
```


### PointcutAdvisor

切点切面，对目标类指定连接点进行切面

- PointcutAdvisor
	- 实现类
		- DefaultPointcutAdvisor
			- 最常用切面，组合Pointcut和Advice组合定义切面
				- MethodIntercepter
					- invocation.proceed()
			- DI属性
				- target
				- proxyTargetClass
					- true
				- intercepterNames
					- 不能是MethodIntercepter接口实现类了
						- 因为一般的切面是使用通知作为切面，我们要对某个目标类方法增强，需要配置带切入点的切面
							- JdkRegexpMethodPointcut
								- 正则表达式切点
									- DI属性
										- pattern
											- `.*user.*,.*delete.*`
										- advice
											- MethodIntercepter接口子类


## 自动创建代理

之前介绍的方式，每个代理都需要配置一大堆的关于ProxyFactoryBean的工厂信息，如果代理多后，开发维护成本都非常的高

- BeanNameAutoProxyCreator
	- Bean名称
- DefaultAdvisorAutoProxyCreator
	- 切面信息创建的代理
		- Advisor本身信息
- AnnotationAwareAspectJAutoProxyCreator
	- 基于Bean中AspectJ注解

### BeanNameAutoProxyCreator 基于Bean名称给自动代理机制

所有以DAO结尾的Bean产生代理，其实底层依据`BeanPostProcessor`在Bean创建时候自动创建了代理

- BeanNameAutoProxyCreator
	- DI属性
		- beanNames
			- *Dao
	- interceptorNames
		- <property name="" value=""/>

### DefaultAdvisorAutoProxyCreator

基于切面信息代理 

- RegexpMethodPointcutAdvisor
	- pattern
	- advice
- DefaultAdvisorAutoProxyCreator