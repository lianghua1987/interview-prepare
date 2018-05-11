# Interview

## Java

### Collection

- **Set** - 无序

  - **TreeSet**
  - **HashSet**

- **List** - 有序

  - **ArrayList**
  - **Vector**
  - **LinkedList**

- **Queue** - FIFO

  - **Deque**
  - **AbstractQueue**
    - **PriorityQueue** - Non blocking, 类实质上维护了一个有序列表。加入到 Queue 中的元素根据它们的天然排序（通过其 java.util.Comparable 实现）或者根据传递给构造函数的 java.util.Comparator 实现来定位。
    - **ConcurrentLinkedQueue** - Non blocking, 是基于链接节点的、线程安全的队列。并发访问不需要同步。因为它在队列的尾部添加元素并从头部删除它们，所以只要不需要知道队列的大小，ConcurrentLinkedQueue 对公共集合的共享访问就可以工作得很好。收集关于队列大小的信息会很慢，需要遍历队列。
  - **BlockingQueue** - 它实质上就是一种带有一点扭曲的 FIFO 数据结构。不是立即从队列中添加或者删除元素，线程执行操作阻塞，直到有空间或者元素可用。
    - **DelayQueue** - 一个由优先级堆支持的、基于时间的调度队列。基于PriorityQueue来实现的）是一个存放Delayed 元素的无界阻塞队列，只有在延迟期满时才能从中提取元素。该队列的头部是延迟期满后保存时间最长的 Delayed 元素。如果延迟都还没有期满，则队列没有头部，并且poll将返回null。当一个元素的 getDelay(TimeUnit.NANOSECONDS) 方法返回一个小于或等于零的值时，则出现期满，poll就以移除这个元素了。此队列不允许使用 null 元素。
    - **SynchoronousQueue** - 一个利用 BlockingQueue 接口的简单聚集（rendezvous）机制
    - **ArrayBlockingQueue** - 数组支持的有界队列, 在构造时需要指定容量， 并可以选择是否需要公平性，如果公平参数被设置true，等待时间最长的线程会优先得到处理（其实就是通过将ReentrantLock设置为true来 达到这种公平性的：即等待时间最长的线程会先操作）。通常，公平性会使你在性能上付出代价，只有在的确非常需要的时候再使用它。它是基于数组的阻塞循环队 列，此队列按 FIFO（先进先出）原则对元素进行排序。
    - **LinkedBlockingQueue** - 链接点支持的可选有界队列, 容量是没有上限的（说的不准确，在不指定时容量为Integer.MAX_VALUE，不要然的话在put时怎么会受阻呢），但是也可以选择指定其最大容量，它是基于链表的队列，此队列按 FIFO（先进先出）排序元素。
    - **PrioirtyBlockingQueue** - 优先级堆支持的无界优先级队列, 而不是先进先出队列。元素按优先级顺序被移除，该队列也没有上限（看了一下源码，PriorityBlockingQueue是对 PriorityQueue的再次包装，是基于堆数据结构的，而PriorityQueue是没有容量限制的，与ArrayList一样，所以在优先阻塞队列上put时是不会受阻的。虽然此队列逻辑上是无界的，但是由于资源被耗尽，所以试图执行添加操作可能会导致 OutOfMemoryError)，但是如果队列为空，那么取元素的操作take就会阻塞，所以它的检索操作take是受阻的。另外往入该队列中的元素要具有比较能力。

  |                |                        |                                     |
  | -------------- | ---------------------- | ----------------------------------- |
  | add            | 添加                   | 队列满，抛出 IIIegaISlabEepeplian   |
  | remove(queue)  | 移除并返回队列头部元素 | 队列空，抛出 NoSuchElementException |
  | offer(queue)   | 添加元素并返回true     | 队列满，返回false                   |
  | pool(queue)    | 移除并返回队列头部元素 | 队列空，返回null                    |
  | peek(queue)    | 返回队列头部元素       | 队列空，返回null                    |
  | put            | 添加一个元素           | 队列满，阻塞                        |
  | take           | 删除并返回队列头部元素 | 队列空，返回阻塞                    |
  | element(queue) | 返回队列头部元素       | 队列空，抛出 NoSuchElementException |

  **注意**：poll和peek方法出错进返回null。因此，向队列中插入null值是不合法的

### Map

- **TreeMap**
- **HashMap**
- **HashTable**

### ThreadPool

[ref]: https://www.jianshu.com/p/210eab345423

**Constructor**

```
//五个参数的构造函数
public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime,
                          TimeUnit unit, BlockingQueue<Runnable> workQueue)

//六个参数的构造函数-1
public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime,
                          TimeUnit unit, BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory)

//六个参数的构造函数-2
public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime,
                          TimeUnit unit, BlockingQueue<Runnable> workQueue,
                          RejectedExecutionHandler handler)

//七个参数的构造函数
public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime,
                          TimeUnit unit, BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory, RejectedExecutionHandler handler)
```

- **newFixedThreadPool**

  1. 可控制线程最大并发数（同时执行的线程数）
     1. 超出的线程会在队列中等待

  ```
  ExecutorService fixedThreadPool = Executors.newFixedThreadPool(int nThreads);
  
  ExecutorService fixedThreadPool = Executors.newFixedThreadPool(int nThreads, ThreadFactory threadFactory);
  
  return new ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>());
  ```

- **newCachedThreadPool**

  1. 线程数无限制
  2. 有空闲线程则复用空闲线程，若无空闲线程则新建线程
  3. 一定程序减少频繁创建/销毁线程，减少系统开销

  ```
  ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
  
  return new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60L, TimeUnit.SECONDS, new SynchronousQueue<Runnable>());
  ```

- **newSingleThreadPool**

  1. 有且仅有一个工作线程执行任务
  2. 所有任务按照指定顺序执行，即遵循队列的入队出队规则

  ```
  ExecutorService singleThreadPool = Executors.newSingleThreadPool();
  
  return new FinalizableDelegatedExecutorService (new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>()));
  ```

- **newScheduledThreadPool**

  ```
  super(corePoolSize, Integer.MAX_VALUE, DEFAULT_KEEPALIVE_MILLIS, MILLISECONDS, new DelayedWorkQueue());
  ```