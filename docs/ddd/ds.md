# 领域服务

> 领域服务是造成聚合根改变的原因，是用来协调领域对象完成某个操作，用来处理业务逻辑的，它本身是一个行为，所以是无状态的。状态由领域对象（具有状态和行为）保存

!>特别注意为了领域服务非必须存在，且最好只进行内存操作，避免其它的副作用产生


## 使用领域服务的地方
- 执行一个显著的业务操作过程
- 对领域对象进行转换
- 以多个领域对象作为输入进行计算，结果产生一个值对象

## 如何使用

```java
@DomainService(domain = "词典域")
public class DictDomainService {
    public Dict merge(Dict dict1, Dict dict2) {
        // 只是做个演示
        final String name = dict2.getName() + dict1.getName();
        final Dict dict = new Dict(null, name, new WordType(dict2.getWordType().getTitle()));
        dict.registEvent(new DemoEvent(dict.getName()));
    }
}

```

## 示例解析

 - 使用`@DomainService`注解标注领域服务
 - 使用 `registEvent` 进行领域事件的注册（这个本来是该刚在聚合根章节说，在这里提一下）