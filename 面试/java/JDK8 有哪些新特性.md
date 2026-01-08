# JDK8 有哪些新特性？

## 回答重点

JDK8 较为重要和平日里经常被问的特性如下：

1）用元空间替代了永久代
2）引入了 Lambda 表达式
3）引入了日期类、接口默认方法、静态方法
4）新增 Stream 流式接口
5）引入 Optional 类
6）新增了 CompletableFuture 、StampedLock 等并发实现类。

如果你对 HashMap、ConcurrentHashMap 面试题有准备的话，这时候也可以抛出来，引导面试官来询问。比如：Java 8 修改了 HashMap 和 ConcurrentHashMap 的实现。

## 扩展知识

元空间替代了永久代

因为 JDK8 要把 JRockit 虚拟机和 Hotspot 虚拟机融合，而 JRockit 没有永久代，所以把 Hotspot 永久代给去了（本质也是永久代回收效率太低）。

详细可看：面试鸭《[为什么 Java8 移除了永久代，加了元空间？](https://www.mianshiya.com/question/1780933295228350466)》 这题。

### Lambda 表达式

Lambda 是 Java 8 引入的一种匿名函数，可以把 Lambda 表达式理解为是一段可以传递的代码（将代码像数据一样进行传递）。使用它可以写出更简洁、更灵活的代码。

接受的对象必须只能有一个接口。就是说是`@FunctionalInterface`

其本质是作为函数式接口的实例。例如：

```java
// 传统方式
Runnable runnable1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("mianshiya.com");
    }
};

// Lambda 表达式
Runnable runnable2 = () -> System.out.println("mianshiya.com");
```

### 日期类

Java 8 引入了新的日期和时间 API（位于 java.time 包中），它们更加简洁和易于使用，解决了旧版日期时间 API 的许多问题。

例如 `Date`、`Calendar` 都是可变类且线程不安全。而新的日期类都是不可变的，一旦创建就不能修改，这样可以避免意外的修改，提升代码的安全性和可维护性。

```java
LocalDate date = LocalDate.now();
LocalTime time = LocalTime.now();
LocalDateTime dateTime = LocalDateTime.now();
```

`Date` 本身不包含时区信息，必须使用 `Calendar` 类来处理时区，但使用起来非常复杂且容易出错。

新 API 提供了专门的时区类（如 `ZonedDateTime`, `OffsetDateTime`, `ZoneId` 等），简化了时区处理，并且这些类的方法更加直观和易用。

### 接口默认方法、静态

默认方法允许在接口中定义方法的默认实现，这样接口的实现类不需要再实现这些方法。之所以提供静态方法，是为了将相关的方法内聚在接口中，而不必创建新的对象。

```java
interface MyInterface {
    default void defaultMethod() {
        System.out.println("Default Method");
    }

    static void hello() { 
        System.out.println("Hello, New Static Method Here"); 
    } 
}
```

### Stream 流式接口

Stream API 提供了一种高效且易于使用的方式来处理数据集合。它支持链式操作、惰性求值和并行处理。

```java
List<String> list = Arrays.asList("a", "b", "c", "d");
List<String> result = list.stream()
                          .filter(s -> s.startsWith("a"))
                          .collect(Collectors.toList());
```

### Optional

`Optional` 类用来解决可能出现的 `NullPointerException` 问题，提供了一种优雅的方式来处理可能为空的值。

```java
Optional<String> optional = Optional.of("mianshiya.com");
optional.ifPresent(System.out::println);
```

Optional 详细可查看面试鸭[《什么是 Optional 类？》 这题](https://www.mianshiya.com/bank/1787463103423897602/question/1800345746421391361)

### CompletableFuture

CompletableFuture 提供了一个新的异步编程模型，简化了异步任务的编写和管理。

```java
CompletableFuture.supplyAsync(() -> "Hello")
                 .thenApply(s -> s + " World")
                 .thenAccept(System.out::println);
```


User: CompletableFuture 你能解释下如何使用吗？

GitHub Copilot: # CompletableFuture 使用指南

## 基本概念

`CompletableFuture` 是 Java 8 引入的异步编程工具，用于处理非阻塞式异步任务，可以优雅地组合多个异步操作。

## 核心用法

### 1. **创建 CompletableFuture**

```java
// 方式一：直接创建
CompletableFuture<String> future = new CompletableFuture<>();
future.complete("结果");

// 方式二：使用静态方法
CompletableFuture<String> future2 = CompletableFuture.completedFuture("结果");

// 方式三：异步执行任务
CompletableFuture<String> future3 = CompletableFuture.supplyAsync(() -> {
    return "异步计算结果";
});
```

### 2. **处理结果 - 链式操作**

```java
CompletableFuture.supplyAsync(() -> 10)
    .thenApply(x -> x * 2)        // 转换结果
    .thenAccept(x -> System.out.println(x))  // 消费结果
    .thenRun(() -> System.out.println("完成")); // 执行操作
```

### 3. **常用方法**


| 方法              | 说明                       |
| ----------------- | -------------------------- |
| `thenApply()`     | 转换结果（返回新值）       |
| `thenAccept()`    | 消费结果（无返回值）       |
| `thenRun()`       | 执行操作（忽略结果）       |
| `thenCompose()`   | 组合多个 CompletableFuture |
| `thenCombine()`   | 合并两个 Future 的结果     |
| `exceptionally()` | 异常处理                   |
| `handle()`        | 处理结果或异常             |

### 4. **异常处理**

```java
CompletableFuture.supplyAsync(() -> {
    throw new RuntimeException("错误");
})
.exceptionally(ex -> {
    System.out.println("捕获异常: " + ex.getMessage());
    return "默认值";
})
.thenAccept(System.out::println);
```

### 5. **并发组合**

```java
// 等待所有任务完成
CompletableFuture.allOf(future1, future2, future3).join();

// 等待任一任务完成
CompletableFuture.anyOf(future1, future2, future3).join();
```

### 6. **实际例子**

```java
// 模拟多个远程调用
CompletableFuture<String> user = CompletableFuture.supplyAsync(() -> 
    queryUserFromDB("userId"));
CompletableFuture<String> orders = CompletableFuture.supplyAsync(() -> 
    queryOrdersFromDB("userId"));

// 组合结果
user.thenCombine(orders, (u, o) -> u + " 拥有订单: " + o)
    .thenAccept(System.out::println)
    .join();  // 阻塞等待完成
```

## 关键点

- **异步非阻塞**：不需要等待，继续执行后续代码
- **链式调用**：易读易维护
- **灵活组合**：支持多个 Future 的组合
- **线程池**：默认使用 `ForkJoinPool.commonPool()`，可自定义

这样就能优雅地处理异步任务了！


User: 你能举个具体场景的使用CompletableFuture例子吗？

GitHub Copilot: # 电商订单系统实例


这是一个实际的电商场景：获取订单详情需要并发查询用户信息、商品信息和库存。

```java
// 业务模型
class User {
    String id;
    String name;
    String email;
}

class Product {
    String id;
    String name;
    double price;
}

class Inventory {
    String productId;
    int quantity;
}

class OrderDetail {
    User user;
    Product product;
    Inventory inventory;
}

// 模拟的数据查询服务
class OrderService {
  
    // 模拟数据库查询（实际应为DB操作）
    private CompletableFuture<User> fetchUser(String userId) {
        return CompletableFuture.supplyAsync(() -> {
            try { Thread.sleep(1000); } catch (InterruptedException e) {}
            return new User(userId, "张三", "zhangsan@example.com");
        });
    }
  
    private CompletableFuture<Product> fetchProduct(String productId) {
        return CompletableFuture.supplyAsync(() -> {
            try { Thread.sleep(800); } catch (InterruptedException e) {}
            return new Product(productId, "iPhone 15", 5999.0);
        });
    }
  
    private CompletableFuture<Inventory> fetchInventory(String productId) {
        return CompletableFuture.supplyAsync(() -> {
            try { Thread.sleep(600); } catch (InterruptedException e) {}
            return new Inventory(productId, 100);
        });
    }
  
    // 核心方法：并发获取订单详情
    public CompletableFuture<OrderDetail> getOrderDetail(String userId, String productId) {
        long startTime = System.currentTimeMillis();
      
        // 1. 并发执行三个查询
        CompletableFuture<User> userFuture = fetchUser(userId);
        CompletableFuture<Product> productFuture = fetchProduct(productId);
        CompletableFuture<Inventory> inventoryFuture = fetchInventory(productId);
      
        // 2. 等待所有查询完成，组合结果
        return userFuture
            .thenCombine(productFuture, (user, product) -> {
                System.out.println("获取用户和产品完成");
                return new Pair<>(user, product);
            })
            .thenCombine(inventoryFuture, (pair, inventory) -> {
                OrderDetail detail = new OrderDetail();
                detail.user = pair.first;
                detail.product = pair.second;
                detail.inventory = inventory;
                return detail;
            })
            .exceptionally(ex -> {
                System.err.println("查询失败: " + ex.getMessage());
                return null;
            })
            .thenApply(detail -> {
                long endTime = System.currentTimeMillis();
                System.out.println("总耗时: " + (endTime - startTime) + "ms");
                return detail;
            });
    }
}

// 使用示例
public class Main {
    public static void main(String[] args) {
        OrderService service = new OrderService();
      
        // 获取订单详情（异步执行）
        service.getOrderDetail("user001", "product123")
            .thenAccept(order -> {
                if (order != null) {
                    System.out.println("=== 订单详情 ===");
                    System.out.println("用户: " + order.user.name);
                    System.out.println("邮箱: " + order.user.email);
                    System.out.println("商品: " + order.product.name);
                    System.out.println("价格: ¥" + order.product.price);
                    System.out.println("库存: " + order.inventory.quantity);
                }
            })
            .join();  // 等待异步任务完成
    }
}
```

## 核心优势


| 优势         | 说明                                                                |
| ------------ | ------------------------------------------------------------------- |
| **并发执行** | 三个查询同时进行，总耗时 ~1000ms（最长的那个），而非 2400ms（串行） |
| **优雅组合** | 用`thenCombine()` 逐步组合结果，代码清晰                            |
| **异常处理** | 用`exceptionally()` 统一捕获异常                                    |
| **非阻塞**   | 主线程不被阻塞，可处理其他任务                                      |

## 性能对比

```
串行执行:  查询用户(1s) → 查询商品(0.8s) → 查询库存(0.6s) = 2.4秒
并行执行:  所有查询同时进行，最长为1s = 1秒
效率提升: 58%
```

这就是 `CompletableFuture` 在实际项目中的威力！
