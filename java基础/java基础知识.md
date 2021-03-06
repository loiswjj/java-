### 1. JDK 与 JRE有什么区别？
JDK（Java Development Kit）: java开发工具包，其包含了JRE以及java的开发环境，包含了各种类库以及工具
JRE （Java Runtime Environment）：java 运行所需环境，JRE里面包含了JVM以及java程序运行时所需的类库

如果是仅仅想要运行java项目而不是开发java程序，仅需要下载jre即可
PS：如果想要运行tomcat项目需要下载jdk，因为tomcat中的servlet需要先使用java进行编译

### 2. java中的包装类与基础数据类型
基础数据类型包括：int、float、double、long、short、byte、char、boolean
其对应的包装类分别为：Integer、Float、Double、Long、Short、Byte、Character、Boolean
关于Java的8种基本数据类型，其名称、位数、默认值、取值范围及示例如下表所示：

序号 数据类型 位数 默认值 取值范围 举例说明
1	byte(位)	8	0	 -2^7 - 2^7-1	byte b = 10;
2	short(短整数)	16	0	-2^15 - 2^15-1	short s = 10;
3	int(整数)	32	0	-2^31 - 2^31-1	int i = 10;
4	long(长整数)	64	0	-2^63 - 2^63-1	long l = 10l;
5	float(单精度)	32	0.0	-2^31 - 2^31-1	float f = 10.0f;
6	double(双精度)	64	0.0	-2^63 - 2^63-1	double d = 10.0d;
7	char(字符)	16	空	0 - 2^16-1	char c = 'c';
8	boolean(布尔值)	8	false	true、false	boolean b = true;

什么叫装箱？什么叫拆箱？
装箱就是将基础类型包装为包装类： Integer a = 1;
拆箱就是将包装类转化为基础数据类型： int b = a;

### 3. 怎么理解java的多态？
多态的定义：指允许不同类的对象对同一消息做出响应。即同一消息可以根据发送对象的不同而采用多种不同的行为方式。（发送消息就是函数调用）
作用是用于消除类型间的耦合关系
多态的三个条件：要有继承；要有重写；父类引用指向子类对象。
多态可以分为两种：接口实现，继承父类进行方法重写和同一个类中进行方法重载。

### 4. java的接口与抽象类有什么区别？
可以看到，在接口内只能是功能的定义，而抽象类中则可以包括功能的定义和功能的实现。在接口中，所有的属性肯定是 public、static 和 final，
所有的方法都是 abstract，所以可以默认不写上述标识符；在抽象类中，既可以包含抽象的定义，也可以包含具体的实现方法。
可以看到，在接口的实现类中使用 implements 关键字；而在抽象类的实现类中，则使用 extends 关键字。一个接口的实现类可以实现多个接口，
而一个抽象类的实现类则只能实现一个抽象类。
其实在 Java 中，抽象类和具体实现类之间是一种继承关系，也就是说如果釆用抽象类的方式，则父类和子类在概念上应该是相同的。接口却不一样，
如果采用接口的方式，则父类和子类在概念上不要求相同。接口只是抽取相互之间没有关系的类的共同特征，而不用关注类之间的关系，它可以使没有
层次关系的类具有相同的行为。因此，可以这样说：抽象类是对一组具有相同属性和方法的逻辑上有关系的事物的一种抽象，而接口则是对一组具有相
同属性和方法的逻辑上不相关的事物的一种抽象。
抽象类用于一组相关的事物，表示的是“is a”的关系，而接口用于一组不相关的事物，表示的是“like a”的关系。

### 5. 请你讲讲String、StringBuffer、StringBuilder三者的区别
String: 不可变长度，使用final修饰，线程安全；所以每次对String对象进行操作时实际上都是生成了一个新的对象
StringBuffer：长度可变，使用Synchronized修饰，线程安全
StringBuilder：长度可变，线程不安全；在不涉及到线程安全的情况下，优先使用，速度较StringBuffer有很大优势
