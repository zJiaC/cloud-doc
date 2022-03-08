今天碰到一个Spring中Constructor、@Autowired、@PostConstruct的执行顺序导致的问题。 
由于多线程调用的时候，方法调用执行的时候，重新调用了构造方法但@Autowired在构造方法之后执行，导致变量为null从而出现空指针问题。

[参考](http://www.hawu.me/coding/963)

```java
public Class AAA {
    @Autowired
    private BBB b;
    
    public AAA() {
        System.out.println("此时b还未被注入: b = " + b);
    }
 
    @PostConstruct
    private void init() {
        System.out.println("@PostConstruct将在依赖注入完成后被自动调用: b = " + b);
    }
}
```
