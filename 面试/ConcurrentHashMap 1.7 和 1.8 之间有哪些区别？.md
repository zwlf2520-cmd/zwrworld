# ConcurrentHashMap 1.7 和 1.8 之间有哪些区别？

## 回答重点

JDK 1.7 `ConcurrentHashMap` 采用的是**分段锁**，即每个 `Segment` 是独立的，可以并发访问不同的 `Segment`，默认是 16 个 `Segment`，所以最多有 16 个线程可以并发执行。

而 JDK 1.8 移除了 `Segment`，锁的粒度变得更加细化，锁只在链表或红黑树的**节点级别**上进行。通过 CAS 进行插入操作，只有在更新链表或红黑树时才使用 `synchronized`，并且只锁住链表或树的头节点，进一步减少了锁的竞争，并发度大大增加。

并且 JDK 1.7 `ConcurrentHashMap` 只使用了**数组 + 链表**的结构，而 JDK 1.8 和 `HashMap`一样引入了红黑树。

除此之外，还有扩容的区别以及 `size` 方法的计算也不一样。


## 扩容的区别

JDK 1.7 中的扩容**：

* **基于 Segment**：`ConcurrentHashMap` 是由多个 `Segment` 组成的，每个 `Segment` 中包含一个 `HashMap`。当某个 `Segment` 内的 `HashMap` 达到扩容阈值时，单独为该 `Segment` 进行扩容，而不会影响其他 `Segment`。
* **扩容过程**：每个 `Segment` 维护自己的负载因子，当 `Segment` 中的元素数量超过阈值时，该 `Segment` 的 `HashMap` 会扩容，整体的 `ConcurrentHashMap` 并不是一次性全部扩容。

**JDK 1.8 中的扩容**：

* **全局扩容**：`ConcurrentHashMap` 取消了 `Segment`，变成了一个全局的数组（类似于 `HashMap`）。因此，当 `ConcurrentHashMap` 中任意位置的元素超过阈值时，整个 `ConcurrentHashMap` 的数组都会被扩容。
* **基于 CAS 的扩容**：在扩容时，`ConcurrentHashMap` 采用了类似 `HashMap` 的方式，但通过**CAS 操作**确保线程安全，避免了锁住整个数组。在扩容时，多个线程可以同时帮助完成扩容操作。
* **渐进式扩容**：JDK 1.8 的 `ConcurrentHashMap` 引入了渐进式扩容机制，扩容时并不是一次性将所有数据重新分配，而是多个线程共同参与，逐步迁移旧数据到新数组中，降低了扩容时的性能开销。
