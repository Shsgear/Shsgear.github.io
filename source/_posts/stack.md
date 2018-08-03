---
title: JavaScript中的变量对象与堆栈
date: 2018-08-03 15:47:05
tags: 
      - javascript
      - stack
---

# JavaScript中的变量对象与堆栈

JavaScript中并没有严格意义上区分栈内存(FILO)与堆内存(FIFO)。

javascript执行上下文时会产生变量对象。一般变量对象存在堆内存中。为便于理解，将其与堆内存区别开来

<!-- more -->

## 1.变量对象与基本数据类型

基本数据类型(`undefined`, `Null`, `Number`, `String`, `Boolean`)往往直接保存在变量对象中。基础数据类型都是按值访问，因此我们可以直接操作保存在变量中的实际的值。

``` javascript
let a = 1;
let b = a;
b = 2;
```

![image](/images/stack1.png)

## 2.引用数据类型与堆内存

![image](/images/stack2.png)