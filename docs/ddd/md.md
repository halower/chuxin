# 如何定义符合框架要求的领域模型

> 你首先掌握如下术语，其中包含了框架扩充的术语

|  术语   | 描述  |
|  ----  | ----  |
| 实体  | 就是那些通过一个唯一标识字段来区分真实世界中的每一个个体的领域对象。 |
| 值对象  |  值对象，代表的是真实世界中那些一成不变的、本质性的事物，这样的领域对象叫作 “值对象”，如地理位置、行政区划、币种、行业、职位等。 |
| 聚合根  | 即外部程序不能跳过该入口去操作部分，对部分的操作都必须要通过入口。这时，入口对象就成了外部访问的唯一入口，被称为 “聚合根” |
| 聚合行为  | 为了与传统贫血模型进行区分，一个特征就是带有自身描述特性的一些方法，来进行自身逻辑的处理 |

## 如何定义

> 首先看如下示例

!>特别注意为了保护领域对象的纯粹性和安全性，该对象只有`只读`特性

```java
@Slf4j
@Getter
@AllArgsConstructor
@AggregateRoot(domain = "词典域")
public class Dict implements IDictBehavior {
    private final Long id;
    /**
     * 词条名称
     */
    private final String name;

    /**
     * 词条类型
     */
    private WordType wordType;

    @Override
    public void updateWordType(String wordType) {
        this.wordType = new WordType(wordType);
    }
    
    @Override
    public Dict validate() {
        // todo: 必要的逻辑验证处理
        return this;
    }

    public static Dict createWith(DictSideCar sideCar)  {
        return new Dict(null, sideCar.getName(), sideCar.getWordType()).validate();
    }
}
```
为了帮助非`DDD`开发人员在转换过程种的不适应，特此将`行为`使用[聚合行为接口](domain/bx.md)进行约束和单独定义

```java
public interface IDictBehavior extends IAggregateRoot {
    /**
     * 设置词条类型
     * @param wordType 词条类型
     */
    void updateWordType(String wordType);

    /**
     * 验证对象的有效性
     * @return 是否有效
     */
    Dict validate();
}

```

这样的好处，显而易见，让开发人员在设计对象方法的时候更加聚焦，而不会混乱

继续阅读 [边车对象](domain/side_car.md)