# 集合和映射

## 1.集合

- **集合中不能包含重复元素**
- 典型应用：客户统计，文本词汇量统计，

```typescript
interface Set<E> {
    add(e: E): void;
    remove(e: E): void;
    contains(e: E): boolean;
    getSize(): number;
    isEmpty(): boolean;
}
```

## 2. 集合实现

### 2.1 基于二分搜索树的集合实现

在二分搜索树中，不能存放重复元素，所以这就是一个非常好的实现“集合”的底层数据结构

详见【树-BST】一节

### 2.2 基于链表的集合实现





