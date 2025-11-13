# 树

- 树是节点的有限集合，是一种天然的组织结构
- 树的用途：压缩软件 哈夫曼树；人机对战（不断做树的搜索）；
- 有二分搜索树，平衡二叉树，AVL树，红黑树，堆，并查集，线段树，Trie（字典数，前缀树）

## 1. 二叉树

![在这里插入图片描述](note.assets/04ea9d88b26858161def758883ea022b.png)

* 二叉树不一定是“满”的，一个节点也是二叉树、空（NULL）也是二叉树

### 1.1  二叉树数组

```typescript
/** 
二叉树（数组表示）

要求：完成树的基本操作
  1.树的创建和销毁
  2.树中节点的搜索
  3.树中节点的添加与删除
  4.树中节点的遍历

函数操作: 创建树、 销毁树、 添加节点、 删除节点、 搜索节点、 遍历树

关于数组与树之间的算法转换：
number tree [n] 3 5 8 2 6 9 7   父亲结点下标*2+1 该结点左
                                父亲结点下标*2+2 该结点右
          3 (0)

    5 (1)       8 (2)
    
2 (3) 6 (4)   9 (5) 7 (6)
*/

class BinaryTree {
  private tree: number[];
  private size: number;

  constructor(size: number, root: number) {
    this.size = size;
    this.tree = new Array(size).fill(0);
    this.tree[0] = root;
  }

  searchNode(nodeIndex: number): number | null {
    if (nodeIndex < 0 || nodeIndex >= this.size) {
      return null;
    }
    if (this.tree[nodeIndex] === 0) {
      return null;
    }
    return this.tree[nodeIndex];
  }

  addNode(
    nodeIndex: number,
    direction: "left" | "right",
    node: number
  ): boolean {
    if (nodeIndex < 0 || nodeIndex >= this.size) {
      // nodeIndex 本身不合法
      return false;
    }
    if (this.tree[nodeIndex] === 0) {
      return false;
    }

    if (direction === "left") {
      const leftNodeIndex = 2 * nodeIndex + 1;
      if (leftNodeIndex >= this.size) {
        //结点范围不合法
        return false;
      }
      if (this.tree[leftNodeIndex] !== 0) {
        //结点已有值
        return false;
      }
      this.tree[leftNodeIndex] = node;
    } else {
      const rightNodeIndex = 2 * nodeIndex + 2;
      if (rightNodeIndex >= this.size) {
        //结点范围不合法
        return false;
      }
      if (this.tree[rightNodeIndex] !== 0) {
        //结点已有值
        return false;
      }
      this.tree[rightNodeIndex] = node;
    }
    return true;
  }

  deleteNode(nodeIndex: number): { success: boolean; deletedNode?: number } {
    if (nodeIndex < 0 || nodeIndex >= this.size) {
      return { success: false };
    }
    if (this.tree[nodeIndex] === 0) {
      return { success: false }; // 要删除的地方本身就没有结点
    }

    const deletedNode = this.tree[nodeIndex];
    this.tree[nodeIndex] = 0;
    return { success: true, deletedNode };
  }

  treeTraverse(): void {
    console.log(this.tree.join(" "));
  }
}

// main.ts
function main() {
  const root = 3;
  const pTree = new BinaryTree(10, root);

  const node1 = 5;
  const node2 = 8;
  pTree.addNode(0, "left", node1);
  pTree.addNode(0, "right", node2);

  const node3 = 2;
  const node4 = 6;
  pTree.addNode(1, "left", node3);
  pTree.addNode(1, "right", node4);

  const node5 = 9;
  const node6 = 7;
  pTree.addNode(2, "left", node5);
  pTree.addNode(2, "right", node6);

  const deleteResult = pTree.deleteNode(6);
  if (deleteResult.success && deleteResult.deletedNode !== undefined) {
    console.log(`Deleted node: ${deleteResult.deletedNode}`);
  }

  pTree.treeTraverse();

  const searchResult = pTree.searchNode(2);
  if (searchResult !== null) {
    console.log(`node = ${searchResult}`);
  }
}

main();

```

### 1.2 二叉树链表

```
/**
 * 二叉树：链表实现
 * 函数：创建树、销毁树、搜索节点、添加结点、删除节点、遍历（前序、中序、后序）

  结点要素：索引 数据 左孩子指针 右孩子指针
            (0)
      5(1)        8(2)
    2(3) 6(4)   9(5) 7(6)
 */
```

```typescript
//TreeNode 类，包含二叉树节点的基本属性
class TreeNode {
  index: number;
  data: number;
  leftChild: TreeNode | null;
  rightChild: TreeNode | null;
  parent: TreeNode | null;

  constructor(index:number = 0, data:number = 0) {
    this.index = index;
    this.data = data;
    this.leftChild = null;
    this.rightChild = null;
    this.parent = null;
  }

  searchTreeNode(nodeIndex: number): TreeNode | null {
    if (this.index === nodeIndex) {
      return this;
    }
    let temp: TreeNode | null = null;
    if (this.leftChild !== null) {
      if (this.leftChild.index === nodeIndex) {
        return this.leftChild;
      } else {
        //注意没找到的情况继续往下找
        temp = this.leftChild.searchTreeNode(nodeIndex);
        if (temp !== null) {
          return temp;
        }
      }
    }
    if (this.rightChild !== null) {
      if (this.rightChild.index === nodeIndex) {
        return this.rightChild;
      } else {
        temp = this.rightChild.searchTreeNode(nodeIndex);
        if (temp !== null) {
          return temp;
        }
      }
    }
    return null;
  }

  deleteTreeNode(): void {
    //递归删除节点
    if (this.leftChild !== null) {
      //先删除左子树
      this.leftChild.deleteTreeNode();
    }
    if (this.rightChild !== null) {
      //再删除右子树
      this.rightChild.deleteTreeNode();
    }
    if (this.parent !== null) {
      //找到父节点
      if (this.parent.leftChild === this) {
        //如果该节点是父节点的左节点
        this.parent.leftChild = null;
      }
      if (this.parent.rightChild === this) {
        //如果该节点是父节点的右节点
        this.parent.rightChild = null;
      }
    }
  }

  // 前序遍历
  preOrderTraverse(): void {
    console.log(`${this.index}---${this.data}`);
    if (this.leftChild !== null) {
      this.leftChild.preOrderTraverse();
    }
    if (this.rightChild !== null) {
      this.rightChild.preOrderTraverse();
    }
  }

  // 中序遍历
  inOrderTraverse(): void {
    if (this.leftChild !== null) {
      this.leftChild.inOrderTraverse();
    }
    console.log(`${this.index}---${this.data}`);
    if (this.rightChild !== null) {
      this.rightChild.inOrderTraverse();
    }
  }

  // 后序遍历
  postOrderTraverse(): void {
    if (this.leftChild !== null) {
      this.leftChild.postOrderTraverse();
    }
    if (this.rightChild !== null) {
      this.rightChild.postOrderTraverse();
    }
    console.log(`${this.index}---${this.data}`);
  }
}
```

```typescript
// Tree 类，以TreeNode类为基础，管理整个二叉树
class Tree {
  private root: TreeNode;

  constructor(rootIndex: number = 0, rootData: number = 0) {
    this.root = new TreeNode(rootIndex, rootData);
  }

  searchNode(nodeIndex: number): TreeNode | null {
    return this.root.searchTreeNode(nodeIndex);
  }

  addNode(nodeIndex: number, direction: number, newNode: TreeNode): boolean {
    const temp = this.searchNode(nodeIndex);
    if (temp === null) {
      return false;
    }

    const node = new TreeNode();
    node.index = newNode.index;
    node.data = newNode.data;
    node.parent = temp;

    if (direction === 0) {
      //添加左节点
      temp.leftChild = node;
    } else if (direction === 1) {
      //添加右节点
      temp.rightChild = node;
    } else {
      return false;
    }
    return true;
  }

  deleteNode(nodeIndex: number): boolean {
    const temp = this.searchNode(nodeIndex);
    if (temp === null) {
      return false;
    }
    temp.deleteTreeNode();
    return true;
  }

  preOrderTraversal(): void {
    this.root.preOrderTraverse();
  }

  inOrderTraversal(): void {
    this.root.inOrderTraverse();
  }

  postOrderTraversal(): void {
    this.root.postOrderTraverse();
  }
}
```

测试函数

```typescript
function main() {
  const node1 = new TreeNode(1,5);
  const node2 = new TreeNode(2,8);
  const node3 = new TreeNode(3,2);
  const node4 = new TreeNode(4,6);
  const node5 = new TreeNode(5,9);
  const node6 = new TreeNode(6,7);
  
  const tree = new Tree();
  tree.addNode(0, 0, node1);
  tree.addNode(0, 1, node2);

  tree.addNode(1, 0, node3);
  tree.addNode(1, 1, node4);

  tree.addNode(2, 0, node5);
  tree.addNode(2, 1, node6);

  tree.deleteNode(2);

  console.log("--------------poster----------------");
  tree.postOrderTraversal(); 
  // --------------poster----------------3---2
  //4---6
  //1---5
  //0---0
}

main();
```

## 2. 二分搜索树

### 2.1 BST

![在这里插入图片描述](note.assets/74f0b824e5d09e6b7e438cbd2b00e1d4.png)

注意：二分搜索树中的结点必须具有可比较性；

```ts
// 泛型约束：E 必须是可比较的类型
class BST<E extends { compareTo(other: E): number }> {
    private class Node {
        public e: E;
        public left: Node | null;
        public right: Node | null;

        constructor(e: E) {
            this.e = e;
            this.left = null;
            this.right = null;
        }
    }

    private root: Node | null;
    private size: number;

    constructor() {
        this.root = null;
        this.size = 0;
    }

    size(): number {
        return this.size;
    }
    isEmpty(): boolean {
        return this.size === 0;
    }
}
```

### 2.2  添加元素

向二分搜索树中添加新元素

* 我们的二分搜索树不包含重复元素（如果想包含重复元素，只需要定义：左子树小于等于结点；或者右子树大于等于节点）
* 二分搜索树添加元素的非递归写法，和链表很像；
* 更常用递归实现，更简单

```typescript
class BST<E extends { compareTo(other: E): number }> 
    //...
    
    // 调用递归辅助方法，更新根节点
    add(e: E): void {
        this.root = this.addRecursive(this.root, e);
    }

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
        // 元素相等时：不插入（默认不存储重复元素）

        // 返回当前节点（插入后，当前子树的根节点不变）
        return node;
    }
}
```

### 2.3 查询操作

```typescript
class BST<E extends { compareTo(other: E): number }> {
    //...

    // 判断二分搜索树中是否包含元素e
    contains(e: E): boolean {
        // 调用递归辅助方法，从根节点开始查找
        return this.containsRecursive(this.root, e);
    }

    // 看以node为根的二分搜索树中是否包含元素e
    private containsRecursive(node: this.Node | null, e: E): boolean {
        // 递归终止条件：节点为null，说明未找到
        if (node === null) {
            return false;
        }
        
        if (e.compareTo(node.e) === 0) {
            return true;
        }else if (e.compareTo(node.e) < 0) {
            return this.containsRecursive(node.left, e);
        }else {
            return this.containsRecursive(node.right, e);
        }
    }
}
```

### 2.4  遍历（递归实现）

#### 前序遍历

![在这里插入图片描述](note.assets/b5ec6f82f4b43966182c946e3183a906.png)

```typescript
class BST<E extends { compareTo(other: E): number }> {
    //...

    // 二分搜索树的前序遍历（递归）
    preOrder(): void {
        this.preOrderRecursive(this.root);
    }

    // 前序遍历以node为根的二分搜索树
    private preOrderRecursive(node: this.Node | null): void {
        if (node === null) return;
        console.log(node.e); 
        // 递归遍历左子树
        this.preOrderRecursive(node.left);
        // 递归遍历右子树
        this.preOrderRecursive(node.right);
    }
```

#### 中序遍历

```typescript
class BST<E extends { compareTo(other: E): number }> {
    inOrder(): void {
        this.inOrderRecursive(this.root);
    }

    private inOrderRecursive(node: this.Node | null): void {
        if (node === null) return;
        this.inOrderRecursive(node.left);
        console.log(node.e); 
        this.inOrderRecursive(node.right);
    }
```

#### 后序遍历

- 后序遍历的一个应用：为二分搜索树释放内存

```typescript
class BST<E extends { compareTo(other: E): number }> {
    postOrder(): void {
        this.postOrderRecursive(this.root);
    }

    private postOrderRecursive(node: this.Node | null): void {
        if (node === null) return;
        this.postOrderRecursive(node.left);
        this.postOrderRecursive(node.right);
        console.log(node.e); 
    }
```

### 2.5  遍历（非递归实现）

#### 深度优先遍历

非递归实现比递归实现复杂很多。中序遍历和后序遍历的非递归实现实际应用不广。前序遍历是深度优先遍历的具体实现之一，下面是前序遍历的使用栈的非递归实现

![在这里插入图片描述](note.assets/09dd8022eaf824687ab47bee7b11fca7.png)

```typescript
	// 非递归前序遍历（NR = Non-Recursive）
    preOrderNR(): void {
        // 根节点为null，直接返回
        if (this.root === null) return;
        // TS 用数组模拟栈：push()入栈，pop()出栈（LIFO）
        const stack: this.Node[] = [];
        stack.push(this.root);

        // 栈不为空则循环
        while (stack.length > 0) {
            // 弹出栈顶节点（当前访问节点）
            const cur = stack.pop(); 
            // 访问当前节点
            console.log(cur.e);
            // 注意：前序遍历是“根→左→右”，栈是LIFO，因此先压右子树，再压左子树
            if (cur.right !== null) {
                stack.push(cur.right);
            }
            if (cur.left !== null) {
                stack.push(cur.left);
            }
        }
    } 
```

#### 广度优先遍历（层序遍历）

![在这里插入图片描述](note.assets/d0a6ce577ccb4a53a33cda697e728282.png)

```typescript
// 二分搜索树的层序遍历（非递归，基于队列实现）
levelOrder(): void {
    if (this.root === null) return;

    // TS 用数组模拟队列：push()入队，shift()出队（FIFO）
    const queue: this.Node[] = [];
    queue.push(this.root);

    while (queue.length > 0) {
        // 出队队首节点（当前访问节点）
        const cur = queue.shift();
        // 访问当前节点
        console.log(cur.e);

        // 左子树先入队，右子树后入队（保证层序顺序）
        if (cur.left !== null) {
            queue.push(cur.left);
        }
        if (cur.right !== null) {
            queue.push(cur.right);
        }
    }
}
```

层序遍历常用于算法设计中找最短路径

### 2.6 删除最大元素和最小元素

从最简单的，删除二分搜索树的最大值和最小值开始。

以删除最大值为例

- 如果最大值直接为叶子节点，删除即可
- 如果不是，则将该左结点上提至该位置即可

![在这里插入图片描述](note.assets/2e82fb60ce0d3dbff1abd9f079af3c56.png)

![在这里插入图片描述](note.assets/86039cb5304bbdab433b6c2e13c7534c.png)

```typescript
// 寻找二分搜索树中的最小元素
minimum(): E {
    if (this.size === 0) {
        throw new Error("BST is empty");
    }
    const minNode = this.minimumNode(this.root);
    return minNode.e;
}

// 私有递归辅助：返回以 node 为根的 BST 最小值所在节点
private minimumNode(node: this.Node): this.Node {
    if (node.left === null) {
        return node;
    }
    return this.minimumNode(node.left);
}

// 寻找二分搜索树中的最大元素（公开方法）
maximum(): E {
    if (this.size === 0) {
        throw new Error("BST is empty");
    }
    const maxNode = this.maximumNode(this.root);
    return maxNode.e;
}

// 私有递归辅助：返回以 node 为根的 BST 最大值所在节点
private maximumNode(node: this.Node): this.Node {
    if (node.right === null) {
        return node;
    }
    return this.maximumNode(node.right);
}

// 从 BST 中删除最小值所在节点，返回最小值
removeMin(): E {
    const ret = this.minimum(); // 先获取最小值（已处理空树异常）
    this.root = this.removeMinNode(this.root);
    return ret;
}

// 删除以 node 为根的 BST 中的最小节点，返回删除后的新根
private removeMinNode(node: this.Node): this.Node | null {
    if (node.left === null) {
        // 找到最小节点（左子树为空），将其右子树替代当前节点
        const rightNode = node.right;
        node.right = null;
        this.size--;
        return rightNode;
    }
    // 递归删除左子树的最小节点，更新左指针
    node.left = this.removeMinNode(node.left);
    return node;
}

// 从 BST 中删除最大值所在节点，返回最大值
removeMax(): E {
    const ret = this.maximum(); // 先获取最大值
    this.root = this.removeMaxNode(this.root); 
    return ret;
}

// 私有递归辅助：删除以 node 为根的 BST 中的最大节点，返回删除后的新根
private removeMaxNode(node: this.Node): this.Node | null {
    if (node.right === null) {
        // 找到最大节点（右子树为空），将其左子树替代当前节点
        const leftNode = node.left;
        node.left = null; // 断开连接，便于垃圾回收
        this.size--;
        return leftNode;
    }

    // 递归删除右子树的最大节点，更新右指针
    node.right = this.removeMaxNode(node.right);
    return node;
}
```

测试代码

```typescript
function testRemoveMin() {
    // 1. 初始化：创建存储 number 类型的 BST 实例
    const bst = new BST<number>();
    const n = 1000; 

    // 2. 向 BST 插入 1000 个随机整数（范围 0-9999）
    for (let i = 0; i < n; i++) {
        const randomNum = Math.floor(Math.random() * 10000); 
        bst.add(randomNum); 
    }
    // 3. 批量删除 BST 的最小值，存入数组
    const nums: number[] = [];
    while (!bst.isEmpty()) {
        const minNum = bst.removeMin(); 
        nums.push(minNum);
    }
    // 4. 验证结果：输出删除的元素列表（可选）
    console.log("删除的元素列表（升序为正确结果）：", nums);
}
```

#### 2.7 删除任意元素
