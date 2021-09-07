# GeneralMatch-Tools
通用数据匹配框架

# 一、介绍

TT，通用数据匹配框架，Java语言实现，提供标准化匹配入口API，支持任意对象的匹配功能，匹配目前包含过滤、排序、截断、聚合能力。

# 二、结构


# 三、用法

## 1.框架标准化入口

Handler.exec(List<Object> list, Action... action);

## 2.匹配能力介绍

### （1）过滤

SimpleWhereAction，简单过滤器，适合少量数据

ParallelWhereAction，并发过滤器，适合大量数据

支持入参对象所有属性过滤匹配，支持多个字段，也支持and、or，并提供自由组合能力，当前版本共以下算法：

### （2）排序

SimpleSortAction，简单排序器，适合少量数据

ParallelSortAction，并发排序器，适合大量数据

支持多个字段排序，提供desc、asc。

### （3）截断

SimpleLimitAction，简单截断器

### （4）聚合

SimpleLimitAction，简单聚合器

支持入参多个字段自由聚合

# 四、扩展性

## 使用维度：

* 提供用户自行拼装处理器类型，以及对应执行顺序
* 基于ASM字节码而非反射处理入参对象，理论上支持所有自定义对象类型
* 一次操作支持同类型处理器执行多次
* 提供串行、并行两种方式，用户可自行选择使用
* ...

## 维护维度：

* 能力全部来自顶层抽象扩展而来，后续新增能力，不会影响现有功能，符合开闭原则
* 每种能力基于对应类型抽象实现而来，后续在现有能力扩展，不会影响现有功能，符合开闭原则
* ...

# 五、性能

## 测试对象：

```java
public class Student{
    private int id;
    private String name;
    private boolean sex;
    private int age;
    private int level;
    private double score;
}
```

## 测试场景：

where: 过滤算法：(sex || age) && (level || score)  
  过滤条件：sex(等于true) age(小于等于15) level(包含4,5,6) score(大于等于90) 

sort: score, level, id  
  倒序

limit: 0-100

group: sex, level

## 性能表现：

|数据量|串行（5次平均值）|并行（5次平均值）|
|:----|:----|:----|
|1000|724 <br>700<br>632<br>636<br>584<br><br>**655ms**|290<br>243<br>165<br>140<br>125<br><br>**193ms**|
|10000|1892<br>1644<br>1775<br>1624<br>1710<br><br>**1729ms**|345<br>386<br>487<br>403<br>370<br><br>**398ms**|
|100000|5604<br>5142<br>5796<br>5361<br>5683<br><br>**5517ms**|2831<br>2906<br>3066<br>2688<br>2889<br><br>**2876ms**|

注：上述表格数据非常片面，耗时统计包含了数据构建，toString，序列化等等操作，同时受环境和用例影响，不能作为客观评价，但是具有一定参考意义。

# 六、测试

TSingleVeracityTest 准确性测试（单个条件）

TCompVeracityTest 准确性测试（组合条件）

TPerformanceActionTest 批量性能测试（组合条件）
