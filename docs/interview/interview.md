# 个人经验总结

## *基础*

### Integer小知识:

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

### NIO 和 IO 区别

- NIO 是非阻塞的
- NIO 面向块，I/O 面向流

## *Spring*

### [Spring boot 生命周期](https://blog.csdn.net/f641385712/article/details/105762050)：

```
ApplicationStartingEvent：开始启动中
ApplicationEnvironmentPreparedEvent：环境已准备好
ApplicationContextInitializedEvent：上下文已实例化
ApplicationPreparedEvent：上下文已准备好
ApplicationStartedEvent：应用成功启动
ApplicationReadyEvent：应用已准备好
ApplicationFailedEvent：应用启动失败
```

### 获取上下文方式：

- [实现ApplicationContextAware](https://hutool.cn/docs/#/extra/Spring/Spring%E5%B7%A5%E5%85%B7-SpringUtil)

!> [SpringBoot内置生命周期事件详解](https://github.com/yuanmabiji/Java-SourceCode-Blogs/blob/master/SpringBoot/10%20SpringBoot%E5%86%85%E7%BD%AE%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E4%BA%8B%E4%BB%B6%E8%AF%A6%E8%A7%A3%20%20SpringBoot%E6%BA%90%E7%A0%81(%E5%8D%81).md)

!> [Spring：35道精选面试题](https://docsify.tobebetterjavaer.com/#/docs/sidebar/sanfene/spring)

## *Mysql*

### [存储引擎](https://www.pdai.tech/md/db/sql-mysql/sql-mysql-engine.html#mysql---%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E)

- InnoDB
  - 事务型存储引擎
  - 四个标准隔离级别
  - 主索引是聚簇索引
  - 支持真正的在线热备份
  - 支持表跟行级锁
  - 支持外键
- MyISAM
  - 不支持事务
  - 不支持行级锁
  - 崩溃恢复: MyISAM 崩溃后发生损坏的概率比 InnoDB 高很多，而且恢复的速度也更慢
  - 其它特性: MyISAM 支持压缩表和空间数据索引

### [SQL 优化](https://www.pdai.tech/md/db/sql-lan/sql-lan-optimize.html#sql%E8%AF%AD%E8%A8%80---sql%E8%AF%AD%E5%8F%A5%E4%BC%98%E5%8C%96)

## *Redis*

### 分布式锁

- 这个主要利用redis的setnx命令进行，setnx："set if not exists"就是如果不存在则成功设置缓存同时返回1，否则返回0 ，这个特性在很多后台中都有所运用，因为我们服务器是集群的，定时任务可能在两台机器上都会运行，所以在定时任务中首先 通过setnx设置一个lock，
  如果成功设置则执行，如果没有成功设置，则表明该定时任务已执行。 当然结合具体业务，我们可以给这个lock加一个过期时间，比如说30分钟执行一次的定时任务，那么这个过期时间设置为小于30分钟的一个时间就可以，这个与定时任务的周期以及定时任务执行消耗时间相关。
- 实现
  - 单个Redis实例：setnx(key,当前时间+过期时间) + Lua
  - Redis集群模式：Redlock
- 缺点
  - 借助 Redis 实现分布式锁时，有一个共同的缺陷: 当获取锁被拒绝后，需要不断的循环，重新发送获取锁(创建 key )的请求，直到请求成功。这就造成空转，浪费宝贵的 CPU 资源。
  - RedLock算法本身有争议，具体看这篇文章 [How to do distributed locking](https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html)  (opens new window) 以及作者的回复 [Is Redlock safe?](http://antirez.com/news/101)

### 缓存

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

## *MQ*

### 集群高可用部署

## *事务*

### 分布事务

- Seata
  - TC (Transaction Coordinator) - 事务协调者
    - 维护全局和分支事务的状态，指示全局提交或者回滚。
  - TM (Transaction Manager) - 事务管理器
    - 定义全局事务的范围：开始全局事务、提交或回滚全局事务。
  - RM (Resource Manager) - 资源管理器
    - 管理执行分支事务的那些资源，向 TC 注册分支事务、上报分支事务状态、控制分支事务的提交或者回滚。
  - TM是一个分布式事务的发起者和终结者(管理者)，TC 负责维护分布式事务的运行状态，而 RM 则负责本地事务的运行。三个组件相互协作，其中 TC 以 Server 形式独立部署，TM 和 RM 集成在应用中启动

### Spring事务

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

## *设计模式*

### Spring中应用的设计模式

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

## *多线程*

### 线程使用方式

- 实现 Runnable 接口
  - 需要实现 run() 方法。
  - 通过 Thread 调用 start() 方法来启动线程。
- 实现 Callable 接口
  - 与 Runnable 相比，Callable 可以有返回值，返回值通过 FutureTask 进行封装。
- 继承 Thread 类
  - 同样也是需要实现 run() 方法，因为 Thread 类也实现了 Runable 接口。
  - 当调用 start() 方法启动一个线程时，虚拟机会将该线程放入就绪队列中等待被调度，当一个线程被调度时会执行该线程的 run() 方法。

### [ThreadLocal](https://www.pdai.tech/md/java/thread/java-thread-x-threadlocal.html#java-%E5%B9%B6%E5%8F%91---threadlocal%E8%AF%A6%E8%A7%A3)

- ThreadLocal是通过线程隔离的方式防止任务在共享资源上产生冲突, 线程本地存储是一种自动化机制，可以为使用相同变量的每个不同线程都创建不同的存储

## *分布式*

### 跨服务之间分页查询实现及大量数据导出情况

- 个人实际经历是针对当前服务为主体进行查询，遍历获取对应的 id，然后 RPC 调用其它服务通过 id 去获取数据集合，再对其进行封装。
- 针对导出需要查询大量数据的情况，则是采取服务端生成形式。

### [分库分表实现](https://www.pdai.tech/md/framework/ds-sharding/sharding-x-arch.html)

### [分布式锁实现](https://www.pdai.tech/md/arch/arch-z-lock.html#%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F---%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%A1%88)

- [基于数据库](https://www.pdai.tech/md/arch/arch-z-lock.html#%E5%9F%BA%E4%BA%8E%E6%95%B0%E6%8D%AE%E5%BA%93%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81%E6%9C%89%E4%BB%80%E4%B9%88%E7%BC%BA%E9%99%B7)
- [基于 Redis](https://www.pdai.tech/md/arch/arch-z-lock.html#%E5%9F%BA%E4%BA%8Eredis%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81%E6%9C%89%E4%BB%80%E4%B9%88%E7%BC%BA%E9%99%B7)
  - 参照上面Redis知识整理
- [基于 zookeeper](https://www.pdai.tech/md/arch/arch-z-lock.html#%E5%9F%BA%E4%BA%8Ezookeeper%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81)

### [分布式 ID 实现](https://www.pdai.tech/md/arch/arch-z-id.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81%E5%85%A8%E5%B1%80%E5%94%AF%E4%B8%80id)

- 全局唯一ID
  - [UUID](https://www.pdai.tech/md/arch/arch-z-id.html#uuid)
  - [数据库生成](https://www.pdai.tech/md/arch/arch-z-id.html#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%94%9F%E6%88%90)
  - [使用redis实现](https://www.pdai.tech/md/arch/arch-z-id.html#%E4%BD%BF%E7%94%A8redis%E5%AE%9E%E7%8E%B0)
  - [雪花算法-Snowflake](https://www.pdai.tech/md/arch/arch-z-id.html#%E9%9B%AA%E8%8A%B1%E7%AE%97%E6%B3%95-snowflake)
  - [百度-UidGenerator](https://www.pdai.tech/md/arch/arch-z-id.html#%E7%99%BE%E5%BA%A6-uidgenerator)
  - [美团Leaf](https://www.pdai.tech/md/arch/arch-z-id.html#%E7%BE%8E%E5%9B%A2leaf)
  - [Mist 薄雾算法](https://www.pdai.tech/md/arch/arch-z-id.html#mist-%E8%96%84%E9%9B%BE%E7%AE%97%E6%B3%95)

### [微服务组件](https://www.pdai.tech/md/interview/x-interview-2.html#141-spring-cloud)

!> [分布式系统知识体系](https://www.pdai.tech/md/arch/arch-z-overview.html)

## *高并发*

### [接口幂等性](https://www.pdai.tech/md/spring/springboot/springboot-x-interface-mideng.html#%E4%BB%80%E4%B9%88%E6%98%AF%E6%8E%A5%E5%8F%A3%E5%B9%82%E7%AD%89)

- 从幂等和防止重复提交，接口幂等和常见的保证幂等的方式等知识点构筑知识体系
  - 幂等: 更多的是在重复请求已经发生，或是无法避免的情况下，采取一定的技术手段让这些重复请求不给系统带来副作用
  - 防止重复提交: 更多的是不让用户发起多次一样的请求,主要还是从客户端的角度来解决这个问题
- 保证幂等方式
  - 数据库层面
    - 悲观锁: for update
  - 唯一 ID/索引
  - 乐观锁
    - 使用版本号或者时间戳
    - 状态机
- 分布式锁

## *其它*

### [秒杀系统](https://www.pdai.tech/md/arch/arch-example-seckill.html#%E6%9E%B6%E6%9E%84%E6%A1%88%E4%BE%8B---%E7%A7%92%E6%9D%80%E7%B3%BB%E7%BB%9F%E8%AE%BE%E8%AE%A1)

- 遇到的印象最深的问题，如何解决
- 常用中间件实现原理

### [前后端分离接口安全](https://www.pdai.tech/md/spring/springboot/springboot-x-interface-jiami.html#%E5%B8%B8%E8%A7%81%E7%9A%84%E4%BF%9D%E8%AF%81%E6%8E%A5%E5%8F%A3%E5%AE%89%E5%85%A8%E7%9A%84%E6%96%B9%E5%BC%8F)
