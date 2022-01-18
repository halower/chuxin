# 边车对象

> 上篇提到了，领域对象出于安全的考虑不提供`set`的特性，因此，边车对象的出现，就是为了中转有效载荷数据，领域对象从中选择有效的数据进行初始化


 你首先掌握如下框架术语

|  术语   | 描述  |
|  ----  | ----  |
| 边车对象  | 就是一个拥有完整读写权限的数据模型，用户不直接操作领域对象而是领域对象主动从边车对象进行初始化 |


如何定义
```
/**
 * 边车对象
 */
@Data
@ModelSideCar(target = Dict.class)
public class DictSideCar {
    private Long id;
    /**
     * 词条名称
     */
    private String name;

    /**
     * 词条类型
     */
    private WordType wordType;
}

```