# 应用服务
> 应用服务不会处理业务逻辑，它负责编排和转发，它将要实现的功能委托给一个或多个领域对象来实现，它本身只负责处理业务用例的执行顺序以及结果的拼装。\n
  在`CHUNXIN`中也推荐固定的套路
  - 转换为边车对象
  - 编排领域服务 
  - 发布领域事件

## 如何使用

```java
@ApplicationService
@AllArgsConstructor
public class DictUseCase {
    private final IDictAssembler dictAssembler;
    private final DictDomainService dictDomainService;
    private final IDomainEventPublisher<Dict> domainEventPublisher;

    public Boolean addDictItem(AddDictItemCmd cmd) {
        // 转换为边车对象
        final DictSideCar dictSideCar = dictAssembler.cmdToSideCar(cmd);
        final Dict dict = Dict.createWith(dictSideCar);
        //编排领域服务
        dictDomainService.xxx1(dict);
        dictDomainService.xxx2(dict);
        dictDomainService.xxx3(dict);
        //发布领域事件
        domainEventPublisher.publishEvent(dict);
        return false;
    }
}
```

## 示例解析
- 使用`@ApplicationService`注解标注应用服务
- 更现实的例子中，可能在操作业务的时候，校验是需要进行数据库等相关查询的，一个完整的业务用例场景，多数时候不仅限于领域逻辑，也不仅限于访问数据库或者其他第三方服务，往往还需要
消息验证、错误处理、监控、事务、认证与授权等，这个时候可以将这种服务，例如校验是否数据库已经存在的记录可定义为对应的验证方法，放在应用层，再通过`DAO`将校验后的结果传递给领域服务进行主力

## 应用步骤处理模板
基于上述的需求，一个大的应用服务的编排中如果操作多个聚合，可以对单个的聚合进行模板化的内聚处理

```java
@StepProcessor
@AllArgsConstructor
public class AddDictProcessor implements IStepProcessor<AddDictItemCmd, Dict> {
    @Override
    public void validator(AddDictItemCmd cmd) {
      // 有效性校验,处理例如校验是否数据库已经存在的记录
    }

    @Override
    public Dict execute(AddDictItemCmd cmd) {
      // 编排添加字典的业务逻辑即领域服务
    }
}
```

这样上述应用服务内的逻辑如果既包含新增词典，同时又有别的逻辑库，就变成了对应用步骤`AddDictProcessor`的编排

```
@ApplicationService
@AllArgsConstructor
public class DictUseCase {
    private final AddDictProcessor addDictProcessor;
    private final AddDictProcessor addUserProcessor;

    public Boolean addDictItem(AddDictItemCmd cmd) {
        //编排领域服务
        final Dict dict = addDictProcessor.apply(cmd);
        final User user = addUserProcessor.apply(cmd);
        return false;
    }
}
```
这样的好处是开发人员更关注领域服务的编排而不关心细节