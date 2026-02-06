# 10. 递归

## 1. 递归基础与递归的宏观语意

递归：

* 本质上，将原来的问题，转换为更小的同一问题
* 注意递归函数的 “宏观” 语意
* 递归函数就是一个函数，完成一个功能

**对于写一个递归算法只有两部分**

- **问题规模最小的情况的解**
- **分成多个小问题,构建递归过程**

## 2. 举例

### 求和与阶乘

**数组求和**

Sum (arr [0…n-1] ) = arr [0] + Sum ( arr [1…n-1] ) ← 更小的同一问题

Sum ( arr [1…n-1] ) = arr [1] + Sum ( arr [2…n-1] ) ← 更小的同一问题

……

Sum ( arr [n-1…n-1] ) = arr [n-1] + Sum ( [] )

```ts
// 计算 arr[l..n) 范围里的数字和
function sum(arr: number[], l: number): number {
    if (l === arr.length) {
        return 0; // 求解最基本问题
    }
    return arr[l] + sum(arr, l + 1); // 把原问题转化成更小的问题
}
```

**阶乘**

阶乘 n! = 1 * 2 * 3 * ... * n，用函数 fact(n)表示，可以看出：
fact(n) = n! = 1 * 2 * 3 * ... * (n-1) * n = (n-1)! * n = fact(n-1) * n
于是，fact(n)用递归的方式写出来就是：

```typescript
function fact(n):number{
    if(n==1) return 1;
	return n * fact(n - 1)
}
```

以fact(5)为例

```typescript
==> fact(5)
==> 5 * fact(4)
==> 5 * (4 * fact(3))
==> 5 * (4 * (3 * fact(2)))
==> 5 * (4 * (3 * (2 * fact(1))))
==> 5 * (4 * (3 * (2 * 1)))
==> 5 * (4 * (3 * 2))
==> 5 * (4 * 6)
==> 5 * 24
==> 120
```

### 汉诺塔问题

我们对柱子编号为a, b, c，将所有圆盘从a移到c可以描述为：

* 如果a只有一个圆盘，可以直接移动到c；
* 如果a有N个圆盘，可以看成a有1个圆盘（底盘） + (N-1)个圆盘，首先需要把 (N-1) 个圆盘移动到 b，然后，将 a的最后一个圆盘移动到c，再将b的(N-1)个圆盘移动到c。

请编写一个函数，给定输入 n, a, b, c，打印出移动的步骤：move(n, a, b, c)

例如，输入 move(2, 'A', 'B', 'C')，打印出：
A --> B
A --> C
B --> C

```python
def move(n,a,b,c): 
    if n==1:
        #这其实是只有一个圆盘需要从A到C的情况。所有递归，最终都是走到这一步。
        print (a,'-->',c)
        #这是结束递归，省略了None。没有这句的话，递归没办法结束。
        return
    #将A柱的n-1个盘移到B柱，这里毫无争议。注意形参顺序变化了。 
    move(n-1,a,c,b) 
    #这句话才是第一个柱子的第n个圆盘移动到目标柱子。
    print a,'-->',c 
    #过渡柱子B上（n-1）个圆盘B递归移动到目标柱子C
    move(n-1,b,a,c))

```

### 二分查找法

递归版二分查找

```typescript
/**
 * 递归二分查找（泛型实现）
 * 要求：输入数组必须是升序排列！
 */
function binarySearchRecursive<T>(
  arr: T[],
  l: number,
  r: number,
  target: T
): number {
  // 递归终止条件：搜索区间为空
  if (l > r) return -1;

  // 安全计算中点
  const mid = l + Math.floor((r - l) / 2);

  // 找到目标值
  if (arr[mid] === target) {
    return mid;
  }
  // 目标值在左半区间
  else if (target < arr[mid]) {
    return binarySearchRecursive(arr, l, mid - 1, target);
  }
  // 目标值在右半区间
  else {
    return binarySearchRecursive(arr, mid + 1, r, target);
  }
}
```

非递归版，即迭代版二分查找

```typescript
/**
 * 二分查找 - 非递归版本
 * 要求：输入数组必须是升序排列
 */
function binarySearch<T extends number | string>(arr: T[], target: T): number {
  //在arr[l...r]中查找target
  let l = 0;
  let r = arr.length - 1;

  while (l <= r) {
    const mid = l + Math.floor((r - l) / 2);//避免溢出

    if (arr[mid] === target) {
      return mid;
    } else if (target < arr[mid]) {
      r = mid - 1; // 在左半区间 arr[l, mid-1] 继续查找
    } else {
      l = mid + 1; // 在右半区间 arr[mid+1, r] 继续查找
    }
  }

  return -1; // 未找到目标值
}
```

递归实现容易，但迭代版性能更优（避免函数调用开销）

### 二分搜索树插入新结点

```typescript
    // 向以node为根的二分搜索树中插入元素e，递归算法
    // 返回插入新节点后二分搜索树的根
    private addRecursive(node: this.Node | null, e: E): this.Node {
        // 递归终止条件：当前节点为null，创建新节点并返回（作为新的子树根）
        if (node === null) {
            this.size++;
            return new this.Node(e);
        }

        // 递归查找插入位置
        if (e.compareTo(node.e) < 0) {
            // 元素小于当前节点，插入左子树，更新左指针
            node.left = this.addRecursive(node.left, e);
        } else if (e.compareTo(node.e) > 0) {
            // 元素大于当前节点，插入右子树，更新右指针
            node.right = this.addRecursive(node.right, e);
        }
        // 元素相等时：不插入

        // 返回当前节点（插入后，当前子树的根节点不变）
        return node;
    }

```

### 文本逆序复制

这里如何实现对每行文本的**逆序复制**？使用**递归的方式**

如果当前读取的字符不是换行符，且源文件没有读完，则递归读取下一个字符，直到遇到换行符，才开始递归的返回过程，即从该行的最后一个字符开始写入目的文件。

```typescript
//revcopy以递归的方式逐个字符实现一行文本的逆序复制
function revcopy(buffer: string[]): void {
  const char = fs_handle!.read(1); // 读取1字节
  
  // 正确逻辑: 1. 先读取字符 2. 检查是否为有效字符且非换行
  if (char !== null && char.toString() !== '\n') {
    // 递归处理后续字符
    revcopy(buffer);
    // 回溯时写入当前字符（实现逆序）
    buffer.push(char.toString());
  
}
```

### 两两交换链表中的节点

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表

**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

示例：给定1->2->3->4，你应该返回2->1->4->3

```typescript
class ListNode {
    val: number;
    next: ListNode | null;
    constructor(val: number, next: ListNode | null = null) {
        this.val = val;
        this.next = next;
    }
}


function swapPairs(head: ListNode | null): ListNode | null {
  // 终止条件：空链表或者只剩单个节点
  if (head === null || head.next === null) {
    return head;
  }

  // 递归步骤
  // 1. 保存第二个节点next
  const next:ListNode = head.next;

  // 2. 递归处理后续节点（next.next）, 完成两两交换（将当前节点的next指向递归结果，将第二个节点的next指向当前节点）
  head.next = swapPairs(next.next);
  next.next = head;

  // 3. 返回新的头节点
  return next;
}
```

