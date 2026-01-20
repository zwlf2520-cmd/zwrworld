Spring Boot 的启动流程主要围绕 `SpringApplication.run()` 方法展开，该方法是 Spring Boot 应用程序的入口。以下是其核心启动流程：

1.  **执行 `main` 方法**：
    *   应用程序从带有 `@SpringBootApplication` 注解的主类中的 `main` 方法开始执行，通常会调用 `SpringApplication.run(PrimarySource.class, args)`。

2.  **创建 `SpringApplication` 实例**：
    *   `SpringApplication` 构造器会进行一系列初始化操作，包括：
        *   推断应用类型（是否为 Web 应用、Reactive Web 应用或普通应用）。
        *   加载所有 `SpringApplicationRunListener` 实例（通过 `META-INF/spring.factories`）。
        *   加载并设置主应用源（通常是 `@SpringBootApplication` 标注的类）。
        *   初始化 `Banner` 打印器。

3.  **运行 `SpringApplication` 的 `run` 方法**：
    *   调用 `SpringApplicationRunListener` 的 `starting()` 方法，通知应用启动开始。
    *   **准备环境 (Environment)**：
        *   配置和准备 `Environment` 对象，包括加载配置属性（如 `application.properties`/`yml`）、命令行参数、系统属性和环境变量。
        *   应用 `ApplicationContextInitializer` 的 `initialize()` 方法。
        *   调用 `SpringApplicationRunListener` 的 `environmentPrepared()` 方法。
    *   **创建应用上下文 (ApplicationContext)**：
        *   根据推断出的应用类型创建相应的 `ApplicationContext` 实例。例如，Web 应用会创建 `AnnotationConfigServletWebServerApplicationContext`。
        *   调用 `SpringApplicationRunListener` 的 `contextPrepared()` 方法。
    *   **准备应用上下文 (Context Preparation)**：
        *   设置 `ApplicationContext` 的 `Environment`。
        *   将 `SpringApplication` 注册为 Bean。
        *   注册 `SpringBootShutdownHook` 以处理应用关闭。
        *   加载并注册主应用源（例如，`@SpringBootApplication` 标注的类）中的 BeanDefinition。
        *   调用 `SpringApplicationRunListener` 的 `contextLoaded()` 方法。
    *   **刷新应用上下文 (Context Refresh)**：这是 Spring 容器的核心启动阶段，包括：
        *   **初始化 `BeanFactory`**：加载 Bean 定义。
        *   **执行 `BeanFactoryPostProcessor`**：修改 Bean 定义，例如 `PropertySourcePlaceholderConfigurer` 处理占位符，`ConfigurationClassPostProcessor` 解析 `@Configuration` 类。
        *   **注册 `BeanPostProcessor`**：注册 Bean 的后置处理器，它们在 Bean 实例化和初始化前后进行处理。
        *   **实例化单例 Bean**：创建并初始化所有非懒加载的单例 Bean。
        *   **触发事件**：发布各种事件，如 `ContextRefreshedEvent`。
        *   **Spring Boot 自动配置**：`@EnableAutoConfiguration` 在这个阶段发挥作用，根据 classpath、自定义配置等条件，创建并配置所需的 Bean。
    *   **调用 `SpringApplicationRunListener` 的 `started()` 方法**。

4.  **启动嵌入式 Web 服务器 (如果适用)**：
    *   对于 Web 应用，`ApplicationContext` 刷新完成后会启动内嵌的 Web 服务器（如 Tomcat），使其开始监听端口并处理请求。

5.  **调用 `CommandLineRunner` 和 `ApplicationRunner`**：
    *   查找并执行 `ApplicationContext` 中所有实现了 `CommandLineRunner` 或 `ApplicationRunner` 接口的 Bean 的 `run()` 方法。这些 Bean 会在应用启动成功后立即执行特定的任务。

6.  **调用 `SpringApplicationRunListener` 的 `running()` 方法**。

7.  **应用启动完成**：
    *   `run()` 方法返回 `ConfigurableApplicationContext` 对象，应用程序进入运行状态，等待处理请求（如果是 Web 应用）。

