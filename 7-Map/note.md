# 映射

## 1.映射Map

映射Map

* 存储（键，值）数据对的数据结构（Key，Value）

* 根据键（key)，寻找值（Value）

* 非常容易使用链表或者二分搜索树实现（可以参考Set一节笔记），同样的，经测试，二分搜索树实现映射的性能更好

  ```typescript
  class LinkedListNode<K, V> {
    key: K;
    value: V;
    left: LinkedListNode<K, V> | null;
    right: LinkedListNode<K, V> | null;
  }
  
  class BSTNode<K, V> {
    key: K;
    value: V;
    next: BSTNode<K, V> | null;
  }
  ```

* JS原生的Map类型支持set、get、delete、has操作

