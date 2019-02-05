---
title: spring-jdbctemplate
tags:
  - spring
  - jdbc
  - jdbc-template
originContent: "# jdbctemplate\n\n## what?\n\njdbc是为了操作方便，在jdbc api之上封装的，所以我们要先明白jdbc api都做了什么？\n\n这就是大名鼎鼎的mysql-connector-J\n\n## Mysql Connector/J\n\nmaven 依赖：\n\n``` xml\n<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->\n<dependency>\n    <groupId>mysql</groupId>\n    <artifactId>mysql-connector-java</artifactId>\n    <version>8.0.15</version>\n</dependency>\n```\n\n代码实现：https://github.com/zhzye/spring/tree/master/jdbc\n\n## how?\n\n代码库：https://github.com/zhzye/spring/tree/master/jdbctemplate\n\n- spring jdbc template依赖\n\t- mysql驱动 \n\t- Spring组件\n\t\t- core\n\t\t- beans\n\t\t- context\n\t\t- aop\n\t- spring template\n\t\t- spring jdbc\n\t\t- spring tx\n\n依赖xml\n``` xml\n    <dependencies>\n        <!--junit-->\n        <dependency>\n            <groupId>junit</groupId>\n            <artifactId>junit</artifactId>\n            <version>${junit.version}</version>\n        </dependency>\n        <dependency>\n            <groupId>org.springframework</groupId>\n            <artifactId>spring-test</artifactId>\n            <version>${spring.version}</version>\n        </dependency>\n        <!--spring start-->\n        <dependency>\n            <groupId>org.springframework</groupId>\n            <artifactId>spring-core</artifactId>\n            <version>${spring.version}</version>\n        </dependency>\n        <dependency>\n            <groupId>org.springframework</groupId>\n            <artifactId>spring-beans</artifactId>\n            <version>${spring.version}</version>\n        </dependency>\n        <dependency>\n            <groupId>org.springframework</groupId>\n            <artifactId>spring-expression</artifactId>\n            <version>${spring.version}</version>\n        </dependency>\n        <dependency>\n            <groupId>org.springframework</groupId>\n            <artifactId>spring-context</artifactId>\n            <version>${spring.version}</version>\n        </dependency>\n        <!--spring end-->\n        <!--aop start-->\n        <dependency>\n            <groupId>org.springframework</groupId>\n            <artifactId>spring-aop</artifactId>\n            <version>${spring.version}</version>\n        </dependency>\n        <!--aop end-->\n        <!--jdbc start-->\n        <dependency>\n            <groupId>mysql</groupId>\n            <artifactId>mysql-connector-java</artifactId>\n            <version>${mysql.connectorJ.version}</version>\n        </dependency>\n        <!--spring jdbc template start-->\n        <dependency>\n            <groupId>org.springframework</groupId>\n            <artifactId>spring-jdbc</artifactId>\n            <version>${spring.version}</version>\n        </dependency>\n        <dependency>\n            <groupId>org.springframework</groupId>\n            <artifactId>spring-tx</artifactId>\n            <version>${spring.version}</version>\n        </dependency>\n        <!--spring jdbc template end-->\n    </dependencies> \n```\n\napplicationContext\n\n``` xml\n<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<beans xmlns=\"http://www.springframework.org/schema/beans\"\n       xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"\n       xmlns:aop=\"http://www.springframework.org/schema/aop\"\n       xmlns:tx=\"http://www.springframework.org/schema/tx\"\n       xmlns:context=\"http://www.springframework.org/schema/context\"\n       xsi:schemaLocation=\"http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd\n       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd\n       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd\n       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd\">\n    <bean id=\"dataSource\" class=\"org.springframework.jdbc.datasource.DriverManagerDataSource\">\n        <property name=\"driverClassName\" value=\"com.mysql.jdbc.Driver\"/>\n        <property name=\"url\" value=\"jdbc:mysql://localhost:3306/school?useUnicode=true&amp;characterEncoding=utf-8\"/>\n        <property name=\"username\" value=\"root\"/>\n        <property name=\"password\" value=\"\"/>\n    </bean>\n    <bean id=\"jdbcTemplate\" class=\"org.springframework.jdbc.core.JdbcTemplate\">\n        <property name=\"dataSource\" ref=\"dataSource\"/>\n    </bean>\n    <context:component-scan base-package=\"com.zhzye\"/>\n</beans>\n```"
categories:
  - spring
toc: false
date: 2019-02-04 18:57:31
---

# jdbctemplate

## what?

jdbc是为了操作方便，在jdbc api之上封装的，所以我们要先明白jdbc api都做了什么？

这就是大名鼎鼎的mysql-connector-J

## Mysql Connector/J

maven 依赖：

``` xml
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.15</version>
</dependency>
```

代码实现：https://github.com/zhzye/spring/tree/master/jdbc

## how?

代码库：https://github.com/zhzye/spring/tree/master/jdbctemplate

- spring jdbc template依赖
	- mysql驱动 
	- Spring组件
		- core
		- beans
		- context
		- aop
	- spring template
		- spring jdbc
		- spring tx

依赖xml
``` xml
    <dependencies>
        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>${junit.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!--spring start-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-expression</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!--spring end-->
        <!--aop start-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!--aop end-->
        <!--jdbc start-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.connectorJ.version}</version>
        </dependency>
        <!--spring jdbc template start-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!--spring jdbc template end-->
    </dependencies> 
```

applicationContext

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/school?useUnicode=true&amp;characterEncoding=utf-8"/>
        <property name="username" value="root"/>
        <property name="password" value=""/>
    </bean>
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <context:component-scan base-package="com.zhzye"/>
</beans>
```