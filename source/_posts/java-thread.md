---
title: java线程
date: 2019-01-30 22:47:45
tags: ["java", "thread"]
---

## points

- 进程
- 线程
- cpu时间片轮转，达到同时运行多个进程、多个线程
- 什么是多线程
- 线程的创建
- 线程的状态和生命周期
- 线程调度
- 同步和死锁

## 线程的创建

- 创建Thread类，或者一个Thread子类的对象
- 创建一个实现Runnable接口的类的对象


### Thread@java.lang

- 创建的方法
	- Thread()
	- Thread(String name)
	- Thread(Runnable target)
	- Thread(Runnable target, String name) 
- 重要方法
	- public void run()
		- 线程执行体
	- public void start()
		- 启动
	- public static void sleep(long m)
		- 休眠
			- 毫秒
	- public void join() - 优先执行，抢占资源
