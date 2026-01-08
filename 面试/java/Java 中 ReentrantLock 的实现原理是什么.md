`ReentrantLock` 是 Java 并发包（`java.util.concurrent.locks`）中的可重入互斥锁。其实现原理主要包括以下几个方面：

### 1. 基于 AQS（AbstractQueuedSynchronizer）

- `ReentrantLock` 内部通过继承 AQS 实现锁的获取与释放。
- AQS 维护一个 volatile 的 state 变量，表示锁的状态（0 表示未锁定，>0 表示被线程持有）。

### 2. 可重入性

- 同一线程可以多次获取锁，每获取一次 state +1，释放一次 state -1，直到 state 为 0 时真正释放锁。

### 3. 公平锁与非公平锁

- **非公平锁（默认）**：线程尝试直接 CAS 抢占锁，提高吞吐量。
- **公平锁**：线程必须排队，先到先得，避免“插队”。

### 4. 队列同步器

- 未获取到锁的线程会被加入到 AQS 的 FIFO 队列中，阻塞等待唤醒。

### 5. 锁的获取与释放（简化流程）

```java
// 获取锁
if (state == 0) {
    if (CAS(state, 0, 1)) {
        setOwner(currentThread);
    }
} else if (owner == currentThread) {
    state++;
} else {
    // 入队等待
}

// 释放锁
if (owner == currentThread) {
    state--;
    if (state == 0) {
        owner = null;
        // 唤醒队列中的下一个线程
    }
}
```

### 6. 支持条件变量

- 可通过 `newCondition()` 创建条件变量，实现等待/通知机制。

---

**总结**：
`ReentrantLock` 通过 AQS 实现了可重入、可公平/非公平选择、阻塞队列和条件变量等高级特性，是一种比 `synchronized` 更灵活的锁实现。
