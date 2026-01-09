# AQS

## 回答重点

简单来说 AQS 就是起到了一个抽象、封装的作用，将一些排队、入队、加锁、中断等方法提供出来，便于其他相关 JUC 锁的使用，具体加锁时机、入队时机等都需要实现类自己控制。


# AQS 是什么？

**AQS** 是 **AbstractQueuedSynchronizer**（抽象队列同步器），是 Java 并发包 `java.util.concurrent` 中的核心框架。

## 主要作用

AQS 提供了实现同步器（如锁、信号量、倒计时门闩等）的基础框架，通过以下机制工作：

- **状态管理**：维护一个整型的 `state` 状态变量
- **队列管理**：使用 CLH 队列（先进先出）管理等待线程
- **线程阻塞/唤醒**：使用 `LockSupport` 进行线程的阻塞和唤醒

## Java 实现概要

````java
public class AQSExample {
    // 简化的 AQS 概念示例
    static class SimpleLock {
        private final AbstractQueuedSynchronizer sync = 
            new AbstractQueuedSynchronizer() {
                @Override
                protected boolean tryAcquire(int arg) {
                    // 尝试获取锁（state = 0 时表示未锁定）
                    return compareAndSetState(0, 1);
                }

                @Override
                protected boolean tryRelease(int arg) {
                    // 释放锁
                    setState(0);
                    return true;
                }

                @Override
                protected int getState() {
                    return super.getState();
                }
            };

        public void lock() {
            sync.acquire(1);
        }

        public void unlock() {
            sync.release(1);
        }
    }

    public static void main(String[] args) {
        SimpleLock lock = new SimpleLock();
        lock.lock();
        try {
            System.out.println("执行受保护的代码");
        } finally {
            lock.unlock();
        }
    }
}
````

## 常见的 AQS 实现

- **ReentrantLock**：可重入锁
- **Semaphore**：信号量
- **CountDownLatch**：倒计时门闩
- **CyclicBarrier**：循环屏障
- **ReentrantReadWriteLock**：读写锁

需要了解更多细节吗？
