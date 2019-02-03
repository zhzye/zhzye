---
title: spring-RunWith
tags:
  - spring
  - RunWith
originContent: ''
categories:
  - spring
toc: false
date: 2019-02-03 20:06:10
---

## @RunWith

如果单测想拿到Spring容器中的测试用例，那么最简单的方式就是加一个注解

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")

这个相当于执行了ApplicationContext的工厂方法。