# 仓储

框架为非查询操作提供了一个统一的基础接口`IRepository`, 业务仓储只需要继承该接口既可以，理论上只需要实现该基础接口的三个接口即可

## 如何实现
```java
@Validated
public interface IDictRepository extends IRepository<Dict, Long> {
}
```


## 事件

```java
@Repository
@AllArgsConstructor
public class DictRepository implements IDictRepository {
    private final DictDao dictDao;
    private final DictConverter dictConverter;
    @Override
    @Transactional(readOnly = true)
    public Dict findById(@NotNull Long aLong) {
        final DictPo dictPo = dictDao.findById(aLong).orElseThrow(() -> new EntityNotFoundException(Dict.class));
        return dictConverter.poTodo(dictPo);
    }

    @Override
    public Dict persist(@Valid Dict aggregateRoot) {
        DictPo dictPo = new DictPo();
        if(null != aggregateRoot.getId()) {
            dictPo = dictDao.findById(aggregateRoot.getId()).orElseThrow(() -> new EntityNotFoundException(Dict.class));
        }
        dictConverter.doToPo(aggregateRoot, dictPo);
        final DictPo po = dictDao.save(dictPo);
        return dictConverter.poTodo(po);
    }

    @Override
    public void delete(@NotNull Long aLong) {
        dictDao.deleteById(aLong);
    }
}

```