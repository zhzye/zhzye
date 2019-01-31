---
title: 数据结构
tags:
  - 数据结构
  - 数组
originContent: "\n## 数组\n光说不能行，先扔个代码：https://github.com/zhzye/data-structure/tree/master/array\n\n然后我们再来BB：\n\nint[] arr = new int[10];\nint[] arr = new int[]{1, 2, 3, 4};\n\n- 数组最大的优点\n\t- 快速查询\n\t\t- arr[2]\n\t- 数组最好引用于“索引有语义”的情况\n\t- 不是所有的语义索引都适用\n\t\t- 身份证号\n\t\t\t- 开辟的空间太大\n\t- 封装一个数组\n\t\t- ![屏幕快照 20190131 下午5.30.16.png](/images/2019/01/31/ee82b340-255d-11e9-ae81-ed119688c9cb.png)\n\n- 问题\n\t- 索引没有语意，如何表示没有元素？\n\t- 如何添加元素？如何删除元素？\n- Array\n\t- capacity\n\t\t- 容量\n\t- size\n\t\t- 实际元素个数\n- 实现\n\t- 创建元素和删除元素在指定位置是难点\n\t\t- 要考虑验证边界\n\t\t- 在做偏移移动出一个空位或者删除一个空位，要做n次移动\n\t- 当用泛型的时候，如果new一个数组\n\t\t- (E[])new Object[length];\n\t\t\t- 依赖Object做强制类型转换的方式\n\t\t- 当用泛型的时候，如果判断相等\n\t\t\t- 不能像是基础数据类型一样，使用==，要使用equals~~中划线~~"
categories:
  - 数据结构
toc: false
date: 2019-01-31 13:16:43
---


## 数组
光说不能行，先扔个代码：https://github.com/zhzye/data-structure/tree/master/array

然后我们再来BB：

int[] arr = new int[10];
int[] arr = new int[]{1, 2, 3, 4};

- 数组最大的优点
	- 快速查询
		- arr[2]
	- 数组最好引用于“索引有语义”的情况
	- 不是所有的语义索引都适用
		- 身份证号
			- 开辟的空间太大
	- 封装一个数组
		- ![屏幕快照 20190131 下午5.30.16.png](/images/2019/01/31/ee82b340-255d-11e9-ae81-ed119688c9cb.png)

- 问题
	- 索引没有语意，如何表示没有元素？
	- 如何添加元素？如何删除元素？
- Array
	- capacity
		- 容量
	- size
		- 实际元素个数
- 实现
	- 创建元素和删除元素在指定位置是难点
		- 要考虑验证边界
		- 在做偏移移动出一个空位或者删除一个空位，要做n次移动
	- 当用泛型的时候，如果new一个数组
		- (E[])new Object[length];
			- 依赖Object做强制类型转换的方式
		- 当用泛型的时候，如果判断相等
			- 不能像是基础数据类型一样，使用==，要使用equals~~中划线~~