# JDK 动态代理和 CGLIB 动态代理有什么区别？

## 回答重点

**JDK 动态代理**是基于接口的，所以要求代理类一定是有定义接口的。

**CGLIB** 基于 ASM 字节码生成工具，它是通过继承的方式生成目标类的子类来实现代理类，所以要注意 final 方法。

它们之间的性能随着 JDK 版本的不同而不同，以下内容取自：[haiq的博客](https://www.cnblogs.com/haiq/p/4304615.html)

> * jdk6 下，在运行次数较少的情况下，jdk动态代理与 cglib 差距不明显，甚至更快一些；而当调用次数增加之后， cglib 表现稍微更快一些
> * jdk7 下，情况发生了逆转！在运行次数较少（1,000,000）的情况下，jdk动态代理比 cglib 快了差不多30%；而当调用次数增加之后(50,000,000)， 动态代理比 cglib 快了接近1倍
> * jdk8 表现和 jdk7 基本一致

### 扩展 JDK 动态代理

JDK 动态代理是基于接口的代理，因此要求代理类一定是有定义的接口，使用 `java.lang.reflect.Proxy` 类和 `java.lang.reflect.InvocationHandler` 接口实现。

以下为一个简单 JDK 动态代理示例：

```java
// 接口
public interface Service {
    void perform();
}

// 需要被代理的实现类
public class ServiceImpl implements Service {
    @Override
    public void perform() {
        System.out.println("mianshiya.com");
    }
}
```

JDK 动态代理处理类：

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class ServiceInvocationHandler implements InvocationHandler {
    private final Object target;

    public ServiceInvocationHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before method invoke");
        Object result = method.invoke(target, args);
        System.out.println("After method invoke");
        return result;
    }
}
```

创建并使用动态代理对象：

```java
import java.lang.reflect.Proxy;

public class DynamicProxyDemo {
    public static void main(String[] args) {
        Service target = new ServiceImpl();
        Service proxy = (Service) Proxy.newProxyInstance(
                target.getClass().getClassLoader(),
                target.getClass().getInterfaces(),
                new ServiceInvocationHandler(target)
        );

        proxy.perform();
    }
}
```

我们再看看 JDK 动态代理实现原理：

* 首先通过实现 InvocationHandler 接口得到一个切面类。
* 然后利用 Proxy 根据目标类的类加载器、接口和切面类得到一个代理类。
* 代理类的逻辑就是把所有接口方法的调用转发到切面类的 invoke() 方法上，然后根据反射调用目标类的方法。

扩展 CGLIB

CGLIB 基于 ASM 字节码生成工具，它是通过继承的方式来实现代理类，所以不需要接口，可以代理普通类，但需要注意 final 方法（不可继承）。

同样来看个示例：

```java
public class Service {
    public void perform() {
        System.out.println("mianshiya.com");
    }
}
```

CGLIB 动态代理处理类：

```java
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class ServiceMethodInterceptor implements MethodInterceptor {
    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        System.out.println("Before method invoke");
        Object result = proxy.invokeSuper(obj, args);
        System.out.println("After method invoke");
        return result;
    }
}
```

创建并使用动态代理对象：

```java
import net.sf.cglib.proxy.Enhancer;

public class CglibDynamicProxyDemo {
    public static void main(String[] args) {
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(Service.class);
        enhancer.setCallback(new ServiceMethodInterceptor());

        Service proxy = (Service) enhancer.create();
        proxy.perform();
    }
}
```

它是通过字节码生成技术而不是反射来实现调用的逻辑，具体就不再深入了。

## 补充

补充以下几点：

1. **应用场景**：

   - JDK动态代理：适合被代理类已实现接口的情况（如Spring AOP中的大多数Bean）
   - CGLIB：适合没有实现接口的普通类，但需要注意final类和final方法无法代理
2. **字节码技术细节**：

   - CGLIB基于ASM技术直接操作字节码，生成的代理类是被代理类的子类
   - JDK动态代理生成的代理类实现了目标接口
3. **MethodProxy vs Method反射**：

   - CGLIB的`MethodProxy.invokeSuper()`不使用反射，性能更高
   - JDK动态代理使用`Method.invoke()`进行反射调用
4. **选择建议**（实际应用）：

   - Spring 5.x开始，优先使用JDK动态代理（因为JDK性能已大幅提升）
   - 只有在被代理类没有接口时才使用CGLIB
5. **潜在问题**：

   - CGLIB无法代理final类和final方法
   - JDK动态代理在某些场景下可能存在类加载器问题

你的代码示例清晰易懂，非常适合面试讲解。总体来说，这篇总结质量很高！
