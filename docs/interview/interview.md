# 经验总结

## Spring
- [Spring boot生命周期](https://blog.csdn.net/f641385712/article/details/105762050)：
  ```
  ApplicationStartingEvent：开始启动中
  ApplicationEnvironmentPreparedEvent：环境已准备好
  ApplicationContextInitializedEvent：上下文已实例化
  ApplicationPreparedEvent：上下文已准备好
  ApplicationStartedEvent：应用成功启动
  ApplicationStartedEvent：应用成功启动
  ApplicationReadyEvent：应用已准备好
  ApplicationFailedEvent：应用启动失败
  ```
- 获取上下文方式：
  - 实现ApplicationContextAware

## Redis
- 分布式锁
- 缓存：
    - 缓存击穿
    - 缓存穿透
    - 缓存雪崩
- 用途  

## MQ
- 集群高可用

## 事务
- 分布事务
  - Seata
    - TC (Transaction Coordinator) - 事务协调者
      - 维护全局和分支事务的状态，指示全局提交或者回滚。
    - TM (Transaction Manager) - 事务管理器
      - 定义全局事务的范围：开始全局事务、提交或回滚全局事务。
    - RM (Resource Manager) - 资源管理器
      - 管理执行分支事务的那些资源，向TC注册分支事务、上报分支事务状态、控制分支事务的提交或者回滚。
    - TM是一个分布式事务的发起者和终结者（管理者），TC负责维护分布式事务的运行状态，而RM则负责本地事务的运行。三个组件相互协作，其中TC以Server形式独立部署，TM和RM集成在应用中启动
- Spring事务
  - 传播机制
    ```
    PROPAGATION_REQUIRED —— 支持当前事务，如果当前没有事务，则新建一个事务，这是最常见的选择，也是 Spring 默认的一个事务传播属性。
    PROPAGATION_SUPPORTS —— 支持当前事务，如果当前没有事务，则以非事务方式执行。
    PROPAGATION_MANDATORY —— 支持当前事务，如果当前没有事务，则抛出异常。
    PROPAGATION_REQUIRES_NEW —— 新建事务，如果当前存在事务，把当前事务挂起。
    PROPAGATION_NOT_SUPPORTED —— 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
    PROPAGATION_NEVER —— 以非事务方式执行，如果当前存在事务，则抛出异常。
    PROPAGATION_NESTED —— Nested的事务和它的父事务是相依的，它的提交是要等和它的父事务一块提交的。
    ```
  - 四大特性：原子性、一致性、隔离性、持久性
  - 隔离级别：
    ```
    DEFAULT 这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别.
    未提交读（read uncommited） :脏读，不可重复读，虚读都有可能发生
    已提交读 （read commited）:避免脏读。但是不可重复读和虚读有可能发生
    可重复读 （repeatable read） :避免脏读和不可重复读.但是虚读有可能发生.
    串行化的 （serializable） :避免以上所有读问题.
    ```
  - 可能引起失效原因：
  
## 设计模式

## 其它
- Integer小知识:
  - [涉及IntegerCache，1000==1000为false而100==100为true](https://mp.weixin.qq.com/s/mBs5k2LnoGMerpE2vAQzyg):
    ``` java
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
- 秒杀系统
- 遇到的印象最深的问题，如何解决
- 常用中间件实现原理