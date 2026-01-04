# 排序

## 排序



### 选择排序

```typescript
function selectionSort<T>(arr: T[], compareFn?: (a: T, b: T) => boolean): void {
  // 默认比较函数：升序（a < b）
  const less = compareFn ?? ((a: T, b: T) => a < b);
  const n = arr.length;

  for (let i = 0; i < n; i++) {
    let minIndex = i;
    for (let j = i + 1; j < n; j++) {
      if (less(arr[j], arr[minIndex])) {
        minIndex = j;
      }
    }
    // 交换
    [arr[i], arr[minIndex]] = [arr[minIndex], arr[i]];
  }
}
```

**为什么要选择o（n2）的排序算法？**

基础；编码简单，易于实现，是一些简单情景的首选；
在一些特殊情况下，简单的排序算法更有效；
简单的排序算法思想衍生出复杂的排序算法；
作为子过程，改进更复杂的排序算法

### 插入排序

基础插入排序

```typescript
function insertionSort<T>(arr: T[]): void {
  const n = arr.length;
  for (let i = 1; i < n; i++) {
    for (let j = i; j > 0 && arr[j] < arr[j - 1]; j--) {
      // 交换相邻元素
      [arr[j], arr[j - 1]] = [arr[j - 1], arr[j]];
    }
  }
}
```

swap比较耗时，如何进行改进？让其只交换一次

```typescript
function insertionSort2<T extends number | string>(arr: T[]): void {
  const n = arr.length;
  for (let i = 1; i < n; i++) {
    const e = arr[i]; // 当前要插入的元素
    let j = i;
    // 向后移动所有大于 e 的元素（升序）
    while (j > 0 && arr[j - 1] > e) {
      arr[j] = arr[j - 1];
      j--;
    }
    arr[j] = e; // 插入到正确位置
  }
}
```

#### 衡量算法性能函数

编写测试用例

```typescript
/**
 * 生成一个包含 n 个元素的随机整数数组
 * 每个元素的取值范围为 [rangeL, rangeR]（闭区间）
 */
function generateRandomArray(n: number, rangeL: number, rangeR: number): number[] {
  // 断言：确保 rangeL <= rangeR
  if (rangeL > rangeR) {
    throw new Error(`Invalid range: rangeL (${rangeL}) must be <= rangeR (${rangeR})`);
  }

  const arr: number[] = [];
  for (let i = 0; i < n; i++) {
    // 生成 [rangeL, rangeR] 之间的随机整数
    const randomNum = Math.floor(Math.random() * (rangeR - rangeL + 1)) + rangeL;
    arr.push(randomNum);
  }
  return arr;
}
```

看算法在特定的数据集上的执行时间

```typescript
// 1. 判断数组是否已排序（升序）
function isSorted<T>(arr: T[], compareFn?: (a: T, b: T) => boolean): boolean {
  const less = compareFn ?? ((a: T, b: T) => a < b);
  const n = arr.length;
  for (let i = 0; i < n - 1; i++) {
    // 如果 arr[i] > arr[i+1]，即 !(arr[i] <= arr[i+1])，等价于 less(arr[i+1], arr[i])
    if (less(arr[i + 1], arr[i])) {
      return false;
    }
  }
  return true;
}

// 2. 测试排序函数的正确性和性能
function testSort<T>(
  sortName: string,
  sortFn: (arr: T[], compareFn?: (a: T, b: T) => boolean) => void,
  arr: T[],
  compareFn?: (a: T, b: T) => boolean
): void {
  // 深拷贝数组（避免原数组被修改）
  const arrCopy = [...arr];

  const startTime = performance.now();
  sortFn(arrCopy, compareFn);
  const endTime = performance.now();

  // 断言：检查是否排序成功
  if (!isSorted(arrCopy, compareFn)) {
    throw new Error(`${sortName} failed: array is not sorted!`);
  }

  const duration = (endTime - startTime) / 1000; // 转为秒
  console.log(`${sortName}: ${duration.toFixed(4)} s`);
}

// 示例：选择排序（带泛型和比较函数）
function selectionSort<T>(
  arr: T[],
  compareFn: (a: T, b: T) => boolean = (a, b) => a < b
): void {
   // ....
}

const arr = generateRandomArray(10000, 0, 10000);
testSort("Selection Sort", selectionSort, arr);
```

#### 比较选择排序和插入排序

```typescript
const testArray = generateRandomArray(10000,0,10000);
testSort('selectionSort',selectionSort,testArray); // 0.0713s
testSort('insertionSort',insertionSort,testArray); // 0.0515s
```

插入排序时间少，插入排序可以**提前中止内层循环**（若数组已基本有序，提前中止内存循环会使效率非常高）

上文的数据集为10000个（0，10000）范围内的数组
若将数据集改为10000个（0,3）范围内的数组，随机数范围为0到3之前，对于10000个数，会存在大量的重复的数，由于有这些重复的数，整个数组的有序性要比之前的数据集强很多
再次运行

```typescript
const almostSortedTestArray = generateRandomArray(10000,0,3);
testSort('selectionSort',selectionSort,almostSortedTestArray); // 0.074s
testSort('insertionSort',insertionSort,almostSortedTestArray); // 0.038s
```

可见插入排序的效率提升的更高

为了更好地测试效率，我们创建一个新的生成**近似有序的随机数组**的方法
方法：先生成一个完全有序的随机数组，然后再随机挑选几对元素进行交换，这样得到的数组近乎有序

```typescript
/**
 * 生成一个近乎有序的整数数组（初始为 [0, 1, ..., n-1]），然后随机交换 swapTimes 次
 * @param n 数组长度
 * @param swapTimes 随机交换次数
 * @returns 生成的近乎有序数组
 */
function generateNearlyOrderedArray(n: number, swapTimes: number): number[] {
  // 边界处理
  if (n <= 0) return [];
  if (swapTimes < 0) swapTimes = 0;

  // 1. 初始化有序数组 [0, 1, 2, ..., n-1]
  const arr: number[] = Array.from({ length: n }, (_, i) => i);

  // 2. 执行 swapTimes 次随机交换
  for (let i = 0; i < swapTimes; i++) {
    const posx = Math.floor(Math.random() * n);
    const posy = Math.floor(Math.random() * n);
    
    // 交换 arr[posx] 和 arr[posy]
    [arr[posx], arr[posy]] = [arr[posy], arr[posx]];
  }

  return arr;
}
```

```typescript
const nearlySortedArray = generateNearlyOrderedArray(10000, 10);
testSort('selectionSort', selectionSort, nearlySortedArray); // 0.0691s
testSort('insertionSort', insertionSort, nearlySortedArray); // 0.00001s
```

可见插入排序虽然是o（n2）的算法，但在有些情况下却非常有效。

