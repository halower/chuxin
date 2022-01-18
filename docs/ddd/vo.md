# 值对象

> 值对象，代表的是真实世界中那些一成不变的、本质性的事物，这样的领域对象叫作 “值对象”，如地理位置、行政区划、币种、行业、职位等。
  在实际项目中，我们可以根据业务需求的不同，灵活选用实体还是值对象。比如，在线订餐系统中，根据业务需求的不同，菜单既可以设计成实体，也可以设计成值对象

 如何编写值对象

 
!>特别注意`@Value`指定为不可变类是指创建该类的实例后，该实例的实例变量是不可改变的，从而符合值对象的特性

```java
@Value
@ValueObject(domain = "词典域")
public class WordType {
    String title;

    public WordType(String title) {
        if (title.isEmpty()) {
            throw new IllegalArgumentException("词条名称不能为空");
        }
        if (isInvalid(title)) {
            throw new IllegalArgumentException("词条类型目前只允许扩展词,暂停词");
        }
        this.title = title.trim();
    }

    private boolean isInvalid(String title) {
        return !Arrays.asList("扩展词", "暂停词").contains(title);
    }
}
```