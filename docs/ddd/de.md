# 领域事件

> 领域事件用来表示领域中发生的事件。一个领域事件将导致进一步的业务操作，在实现业务解耦的同时，有助于形成完整的业务闭环。

## 如何使用 
```java
@Value
@EqualsAndHashCode(callSuper=false)
@DomainEvent(domain = "词典域", name = "测试事件")
public class DemoEvent extends AbstractDomainEvent {
    public DemoEvent(String name) {
        this.name = name;
    }
    private String name;
}


/**
 * 事件监听
 */
@Component
public class DemoEventListener {
    @EventListener
    public void listenEvent(DemoEvent event) {
        System.out.println("接收到: " + event.getName());
    }
}

```

## 示例解析

- 领域事件需要继承`AbstractDomainEvent`类，该类中包含了一些事件必要的元数据信息
- 使用 ` @DomainEvent`注解标注领域事件， 其中参数`domain`指定操作域， `name` 说明事件名称，便于后期维护

## 扩展
###  为什么需要`@Value`标记事件？
 聚合的每次状态变化，都是一个事件的发生，事件一旦发生便是不可改变的
###  为什么不提供事件仓储？
事件是不变的，以追加方式记录事件，形成事件日志，进而衍生为事件溯源，但是这种场景对于大多数项目和DDD的初学者可能造成困惑，本着知识最小原则，不提供事件溯源，而是由开发者掌握DDD后自行扩展，便可以非常简单的事件
###  事件的处理很简单嘛？
实则不然， 即便是在一个项目中，可能聚合之间的由于可能使用不同的存储方式，实际上不一定在一个进程内，这个事件就要考虑到分布式事务
