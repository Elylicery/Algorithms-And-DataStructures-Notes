# 10. 递归

## 1. 递归基础与递归的宏观语意

递归：

* 本质上，将原来的问题，转换为更小的同一问题

* 举例：数组求和

  Sum (arr [0…n-1] ) = arr [0] + Sum ( arr [1…n-1] ) ← 更小的同一问题

  Sum ( arr [1…n-1] ) = arr [1] + Sum ( arr [2…n-1] ) ← 更小的同一问题

  ……

  Sum ( arr [n-1…n-1] ) = arr [n-1] + Sum ( [] )

* 注意递归函数的 “宏观” 语意

* 递归函数就是一个函数，完成一个功能

```ts
// 计算 arr[l..n) 范围里的数字和
function sum(arr: number[], l: number): number {
    if (l === arr.length) {
        return 0; // 求解最基本问题
    }
    return arr[l] + sum(arr, l + 1); // 把原问题转化成更小的问题
}
```

对于写一个递归算法只有两部分

- 问题规模最小的情况的解
- 分成多个小问题,构建递归过程