---
title: xml xmlns xsi schemaLocation
tags:
  - xml
originContent: "\nspring中xml代码：\n\n``` xml\n<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<beans xmlns=\"http://www.springframework.org/schema/beans\"\n       xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"\n       xsi:schemaLocation=\"http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd\">\n</beans>\n```\n\nxml先来看看几个概念，java项目中作为xml文件头部开头的标签，我们在好多处都可以看到，下面分别来看看代表的含义\n\n- xmlns\n\t- xml namespaces\n\t\t- 防止相同的标签名称在同一个xml中冲突\n- xsi\n\t- xml schema instance\n\t\t- 业内默认用于xsd文件的命名空间\n- xsd\n\t- xml schema definition\n\t\t- 定义xml文档结构\n- schemaLocation\n\t- schema 具体引用的资源\n\t\t- 由key value组成\n"
categories:
  - xml
toc: false
date: 2019-02-05 00:54:02
---


spring中xml代码：

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```

xml先来看看几个概念，java项目中作为xml文件头部开头的标签，我们在好多处都可以看到，下面分别来看看代表的含义

- xmlns
	- xml namespaces
		- 防止相同的标签名称在同一个xml中冲突
- xsi
	- xml schema instance
		- 业内默认用于xsd文件的命名空间
- xsd
	- xml schema definition
		- 定义xml文档结构
- schemaLocation
	- schema 具体引用的资源
		- 由key value组成


参考：

https://blog.csdn.net/lengxiao1993/article/details/77914155