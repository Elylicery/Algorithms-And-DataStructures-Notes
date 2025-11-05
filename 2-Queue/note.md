# 队列

## 1. 数组队列

队列Queue

* 队列也是一种线性结构
* 普通队列，循环队列
* FIFO：先进先出，只能从一端（队尾）添加元素，只能从另一端（队首）取出元素

数组队列的复杂度分析

```typescript
ArrayQueue<E>
void enqueue(E)  O(1) 均摊
E deque() O(n) 
E getFront() O(1)
number getSize() O(1)
boolean isEmpty O(1)
```

注：出队操作时，每一次把数组的第一个元素（索引为0拿出后），后面所有的元素都要向前移，但如果数组承载的元素是百万级，出队要进行百万级的时间消耗，这样会耗费很长的时间。

如何改进使得出队和入队的循环复杂度都为O（1）？**循环队列**

## 2. 循环队列

删除后不挪动其他元素，基于这样的思想，我们记录队首是谁。
当队首的元素移出后，我们只需要更改front（即head）的指向就行了，即front++

![image-20251024093623697](note.assets/image-20251024093623697.png)

**front指向第一个元素，tail指向最后一个元素的下一个位置。**

**入队**

![111图](note.assets/b6c9537deefd01cd1baee8add66672e4.png)

![在这里插入图片描述](note.assets/f6dc4bc24bbb1363fa0d01aa1f736b87.png)

**出队**

![图](note.assets/9008da48ec112ca11d54bd88d14aa7d8.png)

**循环在哪里？**

![图](note.assets/df83558f7d58f2fbacc3df65a6a8dd06.png)

**判空与判满**

![在这里插入图片描述](note.assets/66dc9727ad4a79ddd1b6234ecf88d625.png)

```typescript
/**
 * 环形队列（循环队列）的TypeScript实现
 */
class MyQueue {
  private m_pQueue: number[]; // 队列数组
  private m_iQueueLen: number; // 队列当前元素个数
  private m_iQueueCapacity: number; // 队列最大容量
  private m_iHead: number; // 队列头指针
  private m_iTail: number; // 队列尾指针

  /**
   * 构造函数：初始化循环队列
   */
  constructor(queueCapacity: number) {
    this.m_iQueueCapacity = queueCapacity;
    this.m_pQueue = new Array(queueCapacity); // 初始化数组，长度为容量
    this.m_iHead = 0; // 头指针初始化为0
    this.m_iTail = 0; // 尾指针初始化为0
    this.m_iQueueLen = 0; // 初始时队列无元素，长度为0
  }

  /**
   * 清空队列：重置头指针、尾指针和元素个数，不改变队列容量
   */
  clearQueue(): void {
    this.m_iHead = 0;
    this.m_iTail = 0;
    this.m_iQueueLen = 0;
    this.m_pQueue.fill(0, 0, this.m_iQueueCapacity);
  }

  /**
   * 判断队列是否为空
   */
  isQueueEmpty(): boolean {
    return this.m_iQueueLen === 0;
  }

  /**
   * 判断队列是否已满
   */
  isQueueFull(): boolean {
    return this.m_iQueueLen === this.m_iQueueCapacity;
  }

  /**
   * 获取队列当前元素个数
   */
  getQueueLength(): number {
    return this.m_iQueueLen;
  }

  /**
   * 入队操作：将元素添加到队尾
   */
  enQueue(element: number): boolean {
    // 队满则无法入队，返回false
    if (this.isQueueFull()) {
      return false;
    }
    // 将元素存入尾指针位置
    this.m_pQueue[this.m_iTail] = element;
    // 尾指针循环后移：取模确保指针不越界（核心逻辑，对应C++的环形特性）
    this.m_iTail = (this.m_iTail + 1) % this.m_iQueueCapacity;
    // 元素个数加1
    this.m_iQueueLen++;
    return true;
  }

  /**
   * 出队操作：从队首移除元素，并通过返回值返回该元素
   */
  deQueue(): { success: boolean; element?: number } {
    // 队空则无法出队，返回失败
    if (this.isQueueEmpty()) {
      return { success: false };
    }
    // 获取队首元素（头指针指向的位置）
    const element = this.m_pQueue[this.m_iHead];
    // 头指针循环后移：取模确保指针不越界
    this.m_iHead = (this.m_iHead + 1) % this.m_iQueueCapacity;
    // 元素个数减1
    this.m_iQueueLen--;
    // 返回成功状态和出队元素（TS中无引用传参，用对象返回更直观）
    return { success: true, element };
  }

  /**
   * 遍历队列：依次打印所有元素（从队首到队尾）
   */
  queueTraverse(): void {
    let currentIndex = this.m_iHead; // 从队首开始遍历
    // 遍历次数等于元素个数（避免循环越界）
    for (let i = 0; i < this.m_iQueueLen; i++) {
      console.log(this.m_pQueue[currentIndex]);
      // 当前索引循环后移
      currentIndex = (currentIndex + 1) % this.m_iQueueCapacity;
    }
  }
}
```

循环队列的复杂度分析

```typescript
LoopQueue<E>
void enqueue(E)  O(1) 均摊
E deque() **O(1) 均摊** 
E getFront() O(1)
number getSize() O(1)
boolean isEmpty O(1)
```

## 3. 队列的应用

* 广度优先遍历

