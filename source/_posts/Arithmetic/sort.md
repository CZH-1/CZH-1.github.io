---
title: "基础排序算法"
date: 2024-02-29 15:59:46
categories:
- 数据结构&算法
tags:
- 数据结构&算法
toc: true # 是否显示目录
---
> 冒泡排序   
> 选择排序  
> 插入排序  
> 快速排序   
> 二分查找  
> 动态规划   
> 贪心算法
<!-- more -->

##  冒泡排序
```JavaScript
// 比较相邻的两个数，若大于交换位置，每一轮找出一个最大值
function bubbleSort(arr) {
  // 缓存数组长度
  const len = arr.length;
  // 外层循环用于控制从头到尾的比较+交换到底有多少轮
  for (let i = 0; i < len; i++) {
    // 内层循环用于完成每一轮遍历过程中的重复比较+交换
    for (let j = 0; j < len - 1; j++) {
      // 若相邻元素前面的数比后面的大
      if (arr[j] > arr[j + 1]) {
        // 交换两者
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
      }
    }
  }
  // 返回数组
  return arr;
}
console.log(bubbleSort([3, 6, 2, 4, 1]));
```
## 选择排序
* 在未排序序列中找到最小（大）元素，存放到排序序列的起始位置
* 从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。
* 重复第二步，直到所有元素均排序完毕

```js
function selectionSort(arr) {
  var len = arr.length;
  var minIndex, temp;
  for (var i = 0; i < len - 1; i++) {
      minIndex = i;
      for (var j = i + 1; j < len; j++) {
        // 寻找最小的数
        if (arr[j] < arr[minIndex]) {    
          // 将最小数的索引保存
          minIndex = j;                 
        }
      }
      temp = arr[i];
      arr[i] = arr[minIndex];
      arr[minIndex] = temp;
  }
  return arr;
}
```

## 插入排序
* 把待排序的数组分成已排序和未排序两部分，初始的时候把第一个元素认为是已排好序的
* 从第二个元素开始，在已排好序的子数组中寻找到该元素合适的位置并插入该位置（如果待插入的元素与有序序列中的某个元素相等，则将待插入元素插入到相等元素的后面。）
* 重复上述过程直到最后一个元素被插入有序子数组中
```js
function insertionSort(arr) {
  const len = arr.length;
  let preIndex, current;
  for (let i = 1; i < len; i++) {
      preIndex = i - 1;
      current = arr[i];
      while(preIndex >= 0 && arr[preIndex] > current) {
          arr[preIndex+1] = arr[preIndex];
          preIndex--;
      }
      arr[preIndex+1] = current;
  }
  return arr;
}
```
## 快速排序
```JS
// 找一个基点，小于放左边，大于放右边
function quickSort(arr,startIndex = 0) {
  // console.log(startIndex)
  if (arr.length <= 1) return arr
  const startNum = arr.splice(startIndex, 1)[0];
    console.log(startNum);
  const right = [],
    left= []
    
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] < startNum) {
      left.push(arr[i]);
    } else {
      right.push(arr[i]);
    }
  }
  return [...quickSort(left), startNum, ...quickSort(right)];
}
const arr = [6, 4, 7, 5, 1, 2, 9, 8]
console.log(arr)
console.log(quickSort(arr))
```

## 二分查找
### 使用前提
想要应用二分查找法，则有如下特性：
* 存储在数组中
* 有序排序

搜索过程从数组的中间元素开始，如果中间元素正好是要查找的元素，则搜索过程结束，如果某一特定元素大于或者小于中间元素，则在数组大于或小于中间元素的那一半中查找，而且跟开始一样从中间元素开始比较，如果在某一步骤数组为空，则代表找不到，这种搜索算法每一次比较都使搜索范围缩小一半

```js
function BinarySearchFirst(arr, target) {
  if (arr.length <= 1) return -1
  // 低位下标
  let lowIndex = 0
  // 高位下标
  let highIndex = arr.length - 1
  while (lowIndex <= highIndex) {
    // 中间下标
    const midIndex = Math.floor((lowIndex + highIndex) / 2)
    if (target < arr[midIndex]) {
        highIndex = midIndex - 1
    } else if (target > arr[midIndex]) {
        lowIndex = midIndex + 1
    } else {
      // 数组中存在重复项，而我们需要找出第一个符合的值
      // 当 target 与 arr[midIndex] 相等的时候，如果 midIndex 为0或者前一个数比 target 小那么就找到了第一个等于给定值的元素，直接返回
      if (midIndex === 0 || arr[midIndex - 1] < target) return midIndex
      // 否则高位下标为中间下标减1，继续查找
      highIndex = midIndex - 1
    }
  }
  return -1
}
```

## 动态规划
简单来说，动态规划其实就是，给定一个问题，我们把它拆成一个个子问题，直到子问题可以直接解决，然后呢，把子问题答案保存起来，以减少重复计算。再根据子问题答案反推，得出原问题解的一种方法。  
一般这些子问题很相似，可以通过函数关系式递推出来，例如斐波那契数列，我们可以得到公式：当 n 大于 2的时候，F(n) = F(n-1) + F(n-2) ，
f(10)= f(9)+f(8),f(9) = f(8) + f(7)...是重叠子问题，当n = 1、2的时候，对应的值为2，这时候就通过可以使用一个数组记录每一步计算的结果，以此类推，减少不必要的重复计算

### 适用场景
如果一个问题，可以把所有可能的答案穷举出来，并且穷举出来后，发现存在重叠子问题，就可以考虑使用动态规划，比如一些求最值的场景，如最长递增子序列、最小编辑距离、背包问题、凑零钱问题等等，都是动态规划的经典应用场景。
关于动态规划题目解决的步骤，一般如下：
* 描述最优解的结构
* 递归定义最优解的值
* 按自底向上的方式计算最优解的值
* 由计算出的结果构造一个最优解

## 参考
* [说说常见的排序算法有哪些？](https://www.developers.pub/wiki/1065322/1068379)

