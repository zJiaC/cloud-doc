* 业务场景
    搭建DI系统定时任务调度管理使用的是Quartz。<br>
    首先通过一个主定时任务去触发其它任务的调度处理。最终是触发BaseTask的execute方法。BaseTask继承Job。<br>
    然后execute方法里通过ThirdBusinessFactory工厂类去获取相应参数获取IThirdBll接口的实例。
* 问题
    因为Spring boot一般都是通过@Autowired去注入，从而实例然后调用，如果直接通过New实例化则里面再通过@Autowired注解会有问题。<br>
    但是如果在ThirdBusinessFactory。每个实例都写成如下将会是越来越多：
    ```java
    @Qualifier("A")
    @Autowired
    private IThirdBll A;
    ```
* 解决
    ```java
      @Autowired
      private List<Itest> testList;
    ```
    先将全部实例注解进去，再通过遍历testList寻找相应的实例。可以自定义个注解例如 TaskHandler，然后根据如下获取并加以判断。
    ```java
    
    @Target({ElementType.TYPE})
    @Retention(RetentionPolicy.RUNTIME)
    @Inherited
    @Component
    public @interface TaskHandler {
      String value() default "";
    
      String customer() default "";
    
      String description() default "";
    }


    TaskHandler annotation = taskHandlerClass.getAnnotation(TaskHandler.class)
    ```

