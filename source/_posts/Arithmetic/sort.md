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
## 参考
[说说常见的排序算法有哪些？](https://www.developers.pub/wiki/1065322/1068379)

