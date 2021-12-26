---
title: 简单算法学习--快速排序
date: 2021-12-26
tags:
- algorithm
header-img: https://img-blog.csdnimg.cn/2020050614115438.png
catalog: true
toc_nav_num: true
---


## 学习内容
> 学习分而治之；学习快速排序

## 分而治之
> 一种著名的递归式解决问题的办法（D&C：divide and conquer)

使用分治需要2个步骤：
    1、找出基线条件，且这种条件必须尽可能的简单
    2、不断将问题分解，直到符合基线条件

TIP：
    编写涉及数组的递归函数时，基线条件通常是数组为空或只包含一个元素。
    陷入困境时，请检查基线条件是不是这样。

交一发Q实现一个简单的分治事例：

```js
// 分治实现一个数组累加
function sum(arr, index) {
    if (index < arr.length - 1) {
        return arr[index] + sum(arr, ++index)
    }
    return arr[index];
}
sum([1,2,3], 0)
```

## 快速排序

实现思路：
    取数组随机一个值（一般是第一个）piovt。
    然后从尾部比较大小，如果小于piovt则放置左边（想成左数组），大于放置右边（想成右数组）。
    依次继续执行左右数组，而piovt值则置换到中间位置不参与下一次迭代。
    基线条件是左<右指针。

直接交大，一个快排实现：
```js
// 时间复杂度 Ologn
function reverse(arr, left, right) {
  const cache = arr[left];
  arr[left] = arr[right];
  arr[right] = cache;
}

function quickSort(arr, lessIndex, greaterIndex) {
  if (lessIndex < greaterIndex) {
    let left = lessIndex + 1;
    let right = greaterIndex;
    const piovt = arr[lessIndex];
    while(left <= right) {
      const greater = arr[right];
      if (piovt > greater) {
        reverse(arr, left, right);
        left++;
      } else {
        right--;
      }
    }
    reverse(arr, lessIndex, left - 1);
    quickSort(arr, lessIndex, left - 2);
    quickSort(arr, left, greaterIndex);
  }
  return arr;
}
```