---
title: 简单算法学习--递归篇
date: 2021-12-05
tags:
- algorithm
header-img: https://img-blog.csdnimg.cn/2020050614115438.png
catalog: true
toc_nav_num: true
---


## 学习内容
> 如何将问题分为基线条件和递归条件

先展示两段代码：
```js
// 迭代：while
function iteration(box) {
    const inner = [box]
    while(inner.length) {
        const item = inner[0];
        if(item.children) {
            item.children.map(child => {
                inner.push(child);
            })
        }
        inner.pop();
    }
}
// 递归：
function recursion(box) {
    if (box.children) {
        box.children.map(child => {
            recursion(child);
        })
    }
}
```

总结：如果使用迭代，程序性能可能更高，但是递归可以让代码更容易阅读。

### 基线条件和递归条件
> 基线条件是指函数不再调用自己，而递归条件是指函数调用自己

### 栈
> 先进后出

##### 调用栈
计算机在内部使用被称为调用栈的栈。
    

##### 递归调用栈（p37）
函数调用另一个函数时，当前函数暂停处于未完成状态，并且该函数的所有变量值都还在内存中。
等最后一个函数执行完成后才依次出栈，释放内存。过程中你始终不知道还需要查找哪些盒子。

而使用迭代方法时，计算机会创建一个堆，你始终知道你还有哪些（盒子）需要查找。更像是一个队列，先进先出。

### 尾递归
> 如果一个函数中所有的递归形式的调用都出现在末尾，我们称这个递归函数是尾递归的。
当递归调用时整个函数体中最后执行的语句，且他的返回值不属于表达式的一部分时，这个函数就是尾递归。
尾递归的特点就是回归过程中不存在任何操作

上代码：
```js
// 尾递归
function fact(n ,res) {
    if(n === 1) {
        return res;
    } else {
        return fact(n - 1, n * res);
    }
}
// 递归
function fact(n) {
    if (n === 1) {
        return 1;
    } else {
        return n * fact(n - 1);
    }
}

对比：
尾递归实现了一个函数的重载，达到了参数传递的结果，不压栈。
也就是说（代码1）的栈内存只存在一个占用，因为栈针没有事情可做时，编译器会实现覆盖的能力。
而（代码2）则会根据n的大小一直扩展内存占用
```
