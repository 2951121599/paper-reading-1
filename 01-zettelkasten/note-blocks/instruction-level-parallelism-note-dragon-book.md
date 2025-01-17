---
tags:
- compiler
- reading-notes
---

# Instruction-Level Parallelism

Created: 2022-05-14 15:14

阅读笔记 [Dragon Book](../paper-notes/2009-compilers-dragon-book.md)

## 处理器体系结构

指令级的并行，有 2 大类，对应 3 个常见概念：
1. multi issue
	1. 软件管理：VLIW（非常长指令字）
	2. 硬件管理：superscalar（超标量 ）
2. pipelining

数值应用往往具有很好的并行性。比如，科学计算、信号处理。

硬件指令调度器，只有有限的空间来缓冲那些必须被阻塞的指令。因此，静态调度（compiler）把相互独立的指令都放的比较近，可以更好的利用硬件设施。

## 代码调度约束

3 种约束：
1. 控制依赖约束：所有的运算，都要在优化后执行
2. 数据依赖约束：运送的结果相同
3. 资源约束：不能超额使用机器上的资源

### 数据依赖

1. 真依赖：写之后再读
2. 反依赖：读之后再写。可以把值存放到不同的位置从而消除依赖。
3. 输出依赖：写之后再写。


反依赖、输出依赖，成为：存储相关依赖（storage-related dependency）。不是“真”依赖，可以通过用不同的内存（寄存器）位置存放不同的值来消除依赖。

### 寻找内存访问之间的依赖关系

总的来说，数据依赖关系，在 compile 阶段，无法完全确定。除非，能够证明 2 个运算指向不同的位置，比如，`*p` 和 `*(p+4)`, 否则，compiler 必须假设他们可能指向同一个位置。


数据依赖分析，取决于编程语言。非类型安全的语言，比如 c 和 c++，一个指针可以被强制转换，指向任何类型的数据对象。分析的难度很高。Java 类型安全，不同类型的对象一定相互独立

## 基本块调度

最优解的复杂度，是 NP-完全的

实践中，基本块只有少量相互之间高度约束的运算。使用简单的调度算法就够了。

### 数据依赖图

data-dependence graph

### List Scheduling

带有优先级的拓扑排序。

依赖图里，不可能有环。所以，必然存在一个拓扑顺序。

有些拓扑顺序，可能比另外一些好。
