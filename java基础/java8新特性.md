### 1. 接口可以有默认实现方法
```
public interface formula {
    default int add(int x,int y){
        return x+y;
    }
    
    static int sub(int x,int y){
        return x-y;
    }
}
```

### 2. lambda 表达式（重要）
```
// 使用 java 8 排序
private void sortUsingJava8(List<String> names){
    Collections.sort(names, (s1, s2) -> s1.compareTo(s2));
}
```
   
语法
lambda 表达式的语法格式如下：

(parameters) -> expression
或
(parameters) ->{ statements; }
以下是lambda表达式的重要特征:

可选类型声明：不需要声明参数类型，编译器可以统一识别参数值。
可选的参数圆括号：一个参数无需定义圆括号，但多个参数需要定义圆括号。
可选的大括号：如果主体包含了一个语句，就不需要使用大括号。
可选的返回关键字：如果主体只有一个表达式返回值则编译器会自动返回值，大括号需要指定明表达式返回了一个数值。

### 3. 可以用::来实现引用
构造器引用：它的语法是Class::new，或者更一般的Class< T >::new
静态方法引用：它的语法是Class::static_method
特定类的任意对象的方法引用：它的语法是Class::method
特定对象的方法引用：它的语法是instance::method

### 4. 函数式接口
@FunctionalInterface，函数式接口(Functional Interface)就是一个有且仅有一个抽象方法，但是可以有多个非抽象方法的接口。
java8新增函数式接口： java.util.function
Predicate <T> 接口是一个函数式接口，它接受一个输入参数 T，返回一个布尔值结果。（java.util.function.Predicate）

### 5. 新工具 − 新的编译工具，如：Nashorn引擎 jjs、 类依赖分析器jdeps。

### 6. Stream API −新添加的Stream API（java.util.stream） 把真正的函数式编程风格引入到Java中。
Stream（流）是一个来自数据源的元素队列并支持聚合操作

元素是特定类型的对象，形成一个队列。 Java中的Stream并不会存储元素，而是按需计算。
数据源 流的来源。 可以是集合，数组，I/O channel， 产生器generator 等。
聚合操作 类似SQL语句一样的操作， 比如filter, map, reduce, find, match, sorted等。
和以前的Collection操作不同， Stream操作还有两个基础的特征：

Pipelining: 中间操作都会返回流对象本身。 这样多个操作可以串联成一个管道， 如同流式风格（fluent style）。 这样做可以对操作
进行优化， 比如延迟执行(laziness)和短路( short-circuiting)。
内部迭代： 以前对集合遍历都是通过Iterator或者For-Each的方式, 显式的在集合外部进行迭代， 这叫做外部迭代。 Stream提供了内部
迭代的方式， 通过访问者模式(Visitor)实现。

### 7. 日期类添加了本地日期类以及带有时区的日期类
LocalDateTime、LocalDate、ZoneDateTime

### 8. Optional 类 − Optional 类已经成为 Java 8 类库的一部分，用来解决空指针异常。
// Optional.ofNullable - 允许传递为 null 参数
Optional<Integer> a = Optional.ofNullable(null);
// Optional.of - 如果传递的参数是 null，抛出异常 NullPointerException
Optional<Integer> b = Optional.of(10);
a.isPresent();  //Optional.isPresent - 判断值是否存在
// Optional.orElse - 如果值存在，返回它，否则返回默认值
Integer value1 = a.orElse(new Integer(0));
//Optional.get - 获取值，值需要存在
Integer value2 = b.get();

### 9. Nashorn, JavaScript 引擎 − Java 8提供了一个新的Nashorn javascript引擎，它允许我们在JVM上运行特定的javascript应用。
