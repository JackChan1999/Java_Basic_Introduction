### 栈

- LIFO
- Stack
- LinkedList 双向链表，栈的链式实现

### 队列

- Queue接口
  - PriorityQueue 优先队列
  - ConcurrentLinkedQueue 支持并发访问的基于链表的队列
- 插入
  - add()
  - offer()
- 删除
  - remove()
  - poll()
- 检查
  - element()
  - peek()

### 阻塞队列

BlockingQueue

- ArrayBlockingQueue 顺序阻塞队列
- LinkedBlockingQueue 链式阻塞队列
- PriorityBlockingQueue 优先阻塞队列
- SynchronousQueue 同步阻塞队列
- DelayQueue
- BlockingDeque 双端阻塞队列
  - LinkedBlockingDeque  基于链表的双端阻塞队列

### Deque 双端队列

可以当成队列和栈使用

- ArrayDeque
- LinkedBlockingDeque
- LinkedList


| 方法说明          | 功能说明                  |
| ------------- | --------------------- |
| addFirst()    | 队首添加元素                |
| addLast()     | 队尾添加元素                |
| offerFirst()  | 队首添加元素                |
| offerLast()   | 队尾添加元素                |
| removeFirst() | 删除队首元素                |
| removeLast()  | 删除队尾元素                |
| pollFirst()   | 返回并删除队首元素             |
| pollLast()    | 返回并删除队尾元素             |
| getFirst()    | 获取队首元素，如果队列为空，则抛异常    |
| getLast()     | 获取队尾元素，如果队列为空，则抛异常    |
| peekFirst()   | 获取队首元素，如果队列为空，则返回null |
| peekLast()    | 获取队尾元素，如果队列为空，则返回null |

