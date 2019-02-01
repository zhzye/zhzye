---
title: java-时间复杂度
tags:
  - java
  - 时间复杂度
originContent: "## 时间复杂度\n\n有区分吗？\n\n有\n\n- O(1)\n- O(n)\n- O(lgn)\n- O(nlgn)\n- O(n^2)\n\n大O描述了算法运行时间和输入数据的关系\n\n\n看一段代码\n``` java\npublic int sum(int[] data) {\n\tint sum = 0;\n        for (int i = 0; i < data.length; i++) {\n            sum += data[i];\n        }\n        return sum;    \n}\n```\n\n- 时间复杂度是o(n)\n\t- n是data中元素的个数\n\t- 实际的算法应该是线性的，实际时间T = c1*n + c2\n\t\t- 忽略常数c1 c2\n\t\t\t- 都是o(n)，因为不同语言生成指令不一样，不同cpu执行效率也不一样\n\t\t\t- 所以我们要忽律常数\n- 比较\n\t- 渐进时间复杂度：描述的是n趋近于无穷的情况\n\t\t- 在数据量n小的时候，常数也可以起作用，在优化具体量级算法时候可以考虑进去\n\t- 算法复杂度分析是关注最坏的情况"
categories:
  - java
toc: false
date: 2019-02-01 08:53:03
---

## 时间复杂度

有区分吗？

有

- O(1)
- O(n)
- O(lgn)
- O(nlgn)
- O(n^2)

大O描述了算法运行时间和输入数据的关系


看一段代码
``` java
public int sum(int[] data) {
	int sum = 0;
        for (int i = 0; i < data.length; i++) {
            sum += data[i];
        }
        return sum;    
}
```

- 时间复杂度是o(n)
	- n是data中元素的个数
	- 实际的算法应该是线性的，实际时间T = c1*n + c2
		- 忽略常数c1 c2
			- 都是o(n)，因为不同语言生成指令不一样，不同cpu执行效率也不一样
			- 所以我们要忽律常数
- 比较
	- 渐进时间复杂度：描述的是n趋近于无穷的情况
		- 在数据量n小的时候，常数也可以起作用，在优化具体量级算法时候可以考虑进去
	- 算法复杂度分析是关注最坏的情况

### 均摊时间复杂度

动态数组的resize（因为要动态改变数组的capacity）的时间复杂度是o(n)，但是对整个性能影响大吗？

均摊复杂度是将resize中带来的操作次数，平均到每个add操作中，这样，我么相当于操作了2次基本操作，平均下来，时间复杂度还是o（1）

均摊在这种案例中，比计算最坏情况更有意义

### 复杂度震荡

我们是不是只是需要考虑resize什么时候触发就可以知道，每一次add操作应该平均分摊几次操作，是2次，对的，一般情况是这样的，但是我们要考虑resize的临界情况，因为add和delete被频繁触发，这样分摊到每一次的次数会发生变化，一直在上升，这样因为震荡，当一起考虑add和delete的时候，复杂度又成为了o(n)

怎么解决？

eager 变为 lazy，当resize时候，缩小的时候请lazy进行resize，留有buffer去缓冲触发的频率
