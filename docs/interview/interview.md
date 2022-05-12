# 个人经验总结

## Spring

- [Spring boot 生命周期](https://blog.csdn.net/f641385712/article/details/105762050)：
  ```
  ApplicationStartingEvent：开始启动中
  ApplicationEnvironmentPreparedEvent：环境已准备好
  ApplicationContextInitializedEvent：上下文已实例化
  ApplicationPreparedEvent：上下文已准备好
  ApplicationStartedEvent：应用成功启动
  ApplicationReadyEvent：应用已准备好
  ApplicationFailedEvent：应用启动失败
  ```
- 获取上下文方式：
  - 实现ApplicationContextAware

!> [SpringBoot内置生命周期事件详解](https://github.com/yuanmabiji/Java-SourceCode-Blogs/blob/master/SpringBoot/10%20SpringBoot%E5%86%85%E7%BD%AE%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E4%BA%8B%E4%BB%B6%E8%AF%A6%E8%A7%A3%20%20SpringBoot%E6%BA%90%E7%A0%81(%E5%8D%81).md)

!> [Spring：35道精选面试题](https://mp.weixin.qq.com/s/EQge6DmgIqYITM3mAxkatg)

## Redis

- 分布式锁
  - 这个主要利用 redis 的 setnx 命令进行，setnx："set if not exists" 就是如果不存在则成功设置缓存同时返回 1，否则返回 0
- 缓存：
  - 缓存穿透
    - 缓存穿透是指缓存和数据库中都没有的数据
    - 解决：
      - 接口层增加校验
      - 从缓存取不到的数据，在数据库中也没有取到，这时也可以将key-value对写为key-null
      - 布隆过滤器
  - 缓存击穿
    - 缓存击穿是指缓存中没有但数据库中有的数据(一般是缓存时间到期)
    - 解决：
      - 设置热点数据永远不过期
      - 接口限流与熔断，降级
      - 加互斥锁
  - 缓存雪崩
    - 缓存雪崩是指缓存中数据大批量到过期时间
    - 解决：
      - 缓存数据的过期时间设置随机，防止同一时间大量数据过期现象发生
      - 如果缓存数据库是分布式部署，将热点数据均匀分布在不同的缓存数据库中
      - 设置热点数据永远不过期
- 用途

## MQ

- 集群高可用部署
  

## 事务

- 分布事务
  - Seata
    - TC (Transaction Coordinator) - 事务协调者
      - 维护全局和分支事务的状态，指示全局提交或者回滚。
    - TM (Transaction Manager) - 事务管理器
      - 定义全局事务的范围：开始全局事务、提交或回滚全局事务。
    - RM (Resource Manager) - 资源管理器
      - 管理执行分支事务的那些资源，向 TC 注册分支事务、上报分支事务状态、控制分支事务的提交或者回滚。
    - TM是一个分布式事务的发起者和终结者(管理者)，TC 负责维护分布式事务的运行状态，而 RM 则负责本地事务的运行。三个组件相互协作，其中 TC 以 Server 形式独立部署，TM 和 RM 集成在应用中启动
- Spring事务
  - 传播机制
    ```
    PROPAGATION_REQUIRED —— 支持当前事务，如果当前没有事务，则新建一个事务，这是最常见的选择，也是 Spring 默认的一个事务传播属性。
    PROPAGATION_SUPPORTS —— 支持当前事务，如果当前没有事务，则以非事务方式执行。
    PROPAGATION_MANDATORY —— 支持当前事务，如果当前没有事务，则抛出异常。
    PROPAGATION_REQUIRES_NEW —— 新建事务，如果当前存在事务，把当前事务挂起。
    PROPAGATION_NOT_SUPPORTED —— 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
    PROPAGATION_NEVER —— 以非事务方式执行，如果当前存在事务，则抛出异常。
    PROPAGATION_NESTED —— Nested 的事务和它的父事务是相依的，它的提交是要等和它的父事务一块提交的。
    ```
  - 四大特性：原子性、一致性、隔离性、持久性
  - 隔离级别：
    ```
    DEFAULT 这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别.
    未提交读 (read uncommited) :脏读，不可重复读，虚读都有可能发生
    已提交读 (read commited):避免脏读。但是不可重复读和虚读有可能发生
    可重复读 (repeatable read) :避免脏读和不可重复读.但是虚读有可能发生.
    串行化的 (serializable) :避免以上所有读问题.
    ```
  - 可能引起失效原因：
    - @Transactional 应用在非 public 修饰的方法上
    - @Transactional 注解属性 propagation(传播机制) 设置错误
    - @Transactional 注解属性 rollbackFor 设置错误
    - 同一个类中方法调用，导致@Transactional 失效

## 设计模式
  - Spring中应用的设计模式
    - 工厂模式
      - Spring 容器本质是一个大工厂，使用工厂模式通过 BeanFactory、ApplicationContext 创建 bean 对象。
    - 代理模式
      - Spring AOP 功能功能就是通过代理模式来实现的，分为动态代理和静态代理。
    - 单例模式
      - Spring 中的 Bean 默认都是单例的，这样有利于容器对 Bean 的管理。
    - 模板模式
      - Spring 中 JdbcTemplate、RestTemplate 等以 Template 结尾的对数据库、网络等等进行操作的模板类，就使用到了模板模式。
    - 观察者模式
      - Spring 事件驱动模型就是观察者模式很经典的一个应用。
    - 适配器模式
      - Spring AOP 的增强或通知 (Advice) 使用到了适配器模式、Spring MVC 中也是用到了适配器模式适配 Controller。
    - 策略模式
      - Spring 中有一个 Resource 接口，它的不同实现类，会根据不同的策略去访问资源。

## 多线程
  - 线程使用方式
    - 实现 Runnable 接口
      - 需要实现 run() 方法。
      - 通过 Thread 调用 start() 方法来启动线程。
    - 实现 Callable 接口
      - 与 Runnable 相比，Callable 可以有返回值，返回值通过 FutureTask 进行封装。
    - 继承 Thread 类
      - 同样也是需要实现 run() 方法，因为 Thread 类也实现了 Runable 接口。
      - 当调用 start() 方法启动一个线程时，虚拟机会将该线程放入就绪队列中等待被调度，当一个线程被调度时会执行该线程的 run() 方法。

## 其它

- Integer小知识:
  - [涉及IntegerCache，1000==1000为false而100==100为true](https://mp.weixin.qq.com/s/mBs5k2LnoGMerpE2vAQzyg):

    ```
    Integer a = 1000, b = 1000;  
    System.out.println(a == b);
    Integer c = 100, d = 100;  
    System.out.println(c == d);
    ```
    ```
    false
    true
    ```
- 跨服务之间分页查询实现及大量数据导出情况
  - 个人实际经历是针对当前服务为主体进行查询，遍历获取对应的 id，然后 RPC 调用其它服务通过 id 去获取数据集合，再对其进行封装。
  - 针对导出需要查询大量数据的情况，则是采取服务端生成形式。
- [秒杀系统](https://www.pdai.tech/md/arch/arch-example-seckill.html#%E6%9E%B6%E6%9E%84%E6%A1%88%E4%BE%8B---%E7%A7%92%E6%9D%80%E7%B3%BB%E7%BB%9F%E8%AE%BE%E8%AE%A1)
- 遇到的印象最深的问题，如何解决
- 常用中间件实现原理
