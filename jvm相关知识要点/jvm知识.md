### 1. JVM 的运行时数据区（jvm内存的划分）：
在java1.8之前方法区和堆属于线程共享区域，虚拟机栈、本地方法栈以及程序计数器属于线程私有区；
java1.8之后方法区在直接内存上面划分出来一块元数据用于之前方法区的存储。

. 程序计数器：是一块较小的内存空间，他可以被看作是当前线程所执行的字节码的行号指示器。（字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等功能都需要依赖这个计数器来完成）
PS： 这是为一个在java虚拟机规范中没有规定OutOfMemory的区域

- 虚拟机栈：生命周期与线程相同。java的虚拟机栈描述的是java方法执行的内存模型：每个方法在执行的同时都会创建一个栈帧，用于存储局部变量表、操作数栈、动态链接、方法的出口等信息。每一个方法从调用直至执行完成的过程，就对应一个栈帧在虚拟机栈中入栈到出栈的过程。
PS： 局部变量表存放了编译期可知的各种基本数据类型、对象的引用（reference）以及returnAddress类型（指向了一条字节码指令的地址）。在这个区域规定了两种异常：StackOverFlowError和OutOfMemoryError

- 本地方法栈：与虚拟机栈发挥的作用类似，他们之间的区别在于虚拟机栈为虚拟机执行java方法（也就是字节码）服务，而本地方法栈则为虚拟机使用到的native方法服务
PS：这个区域同虚拟机栈一样规定了两种异常：StackOverflowError和OutOfMemoryError

- 堆：java（Heap）是java虚拟机所管理的内存中最大的一块。java堆是被所有线程共享的一块内存区域，在虚拟机启动时创建。此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存。（java虚拟机规范描述：所有对象实例以及数组都）要在对上分配，但是随着JIT编译器的反正和逃逸分析技术逐渐成熟，栈上分配、标量替换优化技术会导致一些微妙变化）
java堆可以处于物理上不连续的内存空间，只要逻辑上是连续的即可，可以通过-Xmx和-Xms控制
PS：如果在堆中没有内存完成实例分配，并且堆无法在扩展，就会抛出OutOfMemoryError

- 方法区：虚拟机加载的类信息、常量池、静态变量、即时编译后的代码（Non-Heap）；这个区域的内存回收目标主要是针对常量池的回收与对类型的卸载。-XX:PermSize 和 -XX：MaxPermSize来控制方法区的大小
PS：方法区无法满足内存分配要求就会抛出OutOfMemoryError

### 2. java的类加载机制
+ 加载：需要做三件事：通过一个类的全限定名来获取定义此类的二进制字节流（jar包读取、网络读取、运行时计算生成即动态代理技术、由其他文件生成JSP、数据库中读取），开发人员可以通过自已的一个类加载器去控制字节流的获取方式（即重写一个类加载器的loadClass方法）；将字节流所代表的静态存储结构转化为方法区的运行时数据结构；在内存中生成一个代表这个类的java.lang.Class对象，作为方法区这个类的各种数据访问入口
+ 验证：确保class文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身安全，包括四个阶段的验证：文件格式验证（类似于class文件验证）、元数据验证（对字节码描述信息进行语义分析，这个类是否有父类，是否为final修饰，如果不是抽象类，是否实现了接口或父类要求实现的方法，类中字段、方法是否与父类产生矛盾）、字节码验证（通过数据流和控制流分析，确定程序语义是否合法、符合逻辑，保证任意时刻操作数栈的数据类型与指令代码序列都和配合工作，保证跳转指令不会跳转到方法体以外的字节码指令上，保证方法体中的类型转换有效）、符号引用验证（发生于虚拟机将符号引用转化为直接引用的时候，这个转化动作将在连接的第三阶段解析的时候发生，符号引用验证可以看做对常量池中的各种符合的引用信息的匹配性校验，包括符号引用中通过字符串描述的全限定名能否找到对应类，指定类、字段、访问性是否能被访问等）
+ 准备：正式为类变量分配内存并设置类变量初始值的阶段，这些变量所使用的内存都将在方法区中进行分配。这时候进行内存分配的仅包括类变量（被static修饰的），不包括实例变量，实例变量将会在对象实例化是随对象一起分配在堆中。
+ 解析：虚拟机将常量池内的符号引用替换为直接引用的过程。
	
扩展：符号引用与直接引用
-符号引用：以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能无歧义定位到目标即可。
-直接引用：直接指向目标的指针、相对偏移量或是一个能间接定位到目标的句柄。

### 3. 一个对象在内存里面是怎么划分的
一个对象在内存中的存储布局可以分为3块区域：对象头、实例数据、填充对齐
对象头包括两个部分：
第一部分，用于存储对象自身的运行时数据，包括hashCode、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID、偏向时间戳等；这一部分被称为Mark Word
另一部分，是类型指针，及对象指向它的类元数据的指针
实例数据部分是对象真正存储的有效信息，也就是程序代码中所定义的各种类型的字段内容
填充对齐没有特别含义，仅仅起占位符的作用

### 4. 运行时常量池与字符串常量池的区别
jdk1.7之前运行时常量池逻辑包含字符串常量池存放在方法区
jdk1.7字符串常量池被从方法区拿到了堆，运行时常量池还在方法区
jdk1.8 字符串常量池还在堆中，运行时常量池在方法区，即元空间

String.intern()方法，如果字符串在堆中已经存在，把堆得引用存入字符串常量池，不存在则在字符串常量池中创建字符串常量
 ```
举个例子：
String str = "a"; //直接在字符串常量池创建
String str = String.valueOf(1);  //不会在字符串常量池中直接创建
String s = new String("a") ; //“a”在字符串常量池中创建 在堆中创建字符串对象
String s = new String("a") + new String("b");//第一行
System.out.println(s.intern() == s); //第二行
// true      
// 解析：第1行代码创建了5个对象  字符串常量池中的:"a"和"b" 堆中的:new String("a")和new String("b")和s
// 第二行代码调用s.intern() 由于字符串常量池中并不存在"ab"所以会把s的引用放入String Poll 并返回这个引用 所以会返回true
```

### 5. 内存泄漏与内存溢出的区别
内存泄漏是指分配出去的内存无法回收，即对象可达但是无用的对象无法被GC回收
内存溢出是指程序要求的内存超出了JVM的limit值（详见第10题）

### 6. java中的四种引用
- 强引用：强引用是指程序代码之中普遍存在的，类似“Object obj = new Object()”这类引用，只要强引用还存在，垃圾收集器永远不会回收被引用的对象
- 软引用：描述一些还有用但是非必须的对象，对于软引用关联的对象，在系统没有发生内存溢出之前都不会被回收，系统在即将发生内存溢出以前，会将这类对象列入回收范围进行二次回收。如果这次回收之后还是没有足够内存，才会抛出内存溢出异常
- 弱引用：用来描述费必需对象，他的存活时间一直到下一次GC之前，GC的时候，不论内存是否足够，这类对象都会被回收
- 虚引用：被称为幽灵引用，是最弱的一种引用，无法通过虚引用找到一个对象实例，一个对象是否有虚引用不会对他的生存时间构成影响。设置虚引用的目的是，能在这个对象被收集器回收时收到一个系统通知。

### 7. java虚拟机new 一个对象的创建过程
- java虚拟机遇到一个new指令的时候首先会去运行时常量池里面检查，是否已经存在这个类的符号引用，并且检查这个符号引用所代表的类是否已被加载、解析和初始化。如果没有，则需要先进行类加载过程
- 在java堆中堆对象分配内存
扩展：
如果java堆内存是规整的，那么会采用指针碰撞的方式来给对象分配内存。只要是通过移动指针，将指针向空闲一方移动对对象大小相同的距离；
如果堆内存不规整，会采用空闲列表的方式。
怎么保证高并发条件下堆内存分配的线程安全：方式一，每次更新的时候都采用CAS的方式来保证原子性；方法二，每个线程在java堆中预先分配一小块内存，称为本线程分配缓冲（TLAB），只有TLAB用完分配新的TLAB时，才需要同步锁定。使用-XX:+/-UseTLAB
- 虚拟机将分配到的内存空间进行初始化零值（即默认值）
- 对象头设置：（这个对象是那个对象的实例、如何找到类的元数据信息、对象的hash码、对象的GC分代年龄信息等）
PS：执行完new指令之后，会执行init方法，将对象按照程序员的意愿进行初始化

### 8. 双亲委派模型
java中的类加载器，包括启动类加载器、扩展类加载器、应用程序类加载器
概念：除了顶层的启动类加载器外，其余类加载器都应该有自己的父类加载器。工作过程中，如果一个类加载器收到了类加载的请求，首先不会自己尝试加载这个类，而是把这个请求委派给父类加载器去完成，每一个层次的类加载器都是这样，因此所有的加载请求最终都会传送至顶层的启动类加载器，只有父加载器反馈自己无法完成加载请求，子加载器才会尝试自己去加载。这样可以避免顶层类被重复加载

### 9. java 方法区可以存放哪些东西？jvm如何控制方法区的大小以及内存溢出的原因
虚拟机加载的类信息（类名、访问修饰符、字段描述、方法描述）、常量池、静态变量、即时编译后的代码
-XX:PermSize 和 -XX：MaxPermSize来控制方法区的大小
方法区无法满足内存分配要求就会抛出OutOfMemoryError

### 10. jvm OOM的种类
- java.lang.StackOverflowError: 线程请求的栈深度大于所允许的最大深度（一般会出现在递归的过程中）
- java.lang.OutOfMemoryError: java heap space
java虚拟机堆内存空间不够（堆溢出），可以通过设置虚拟机参数：-Xms设置最小堆，-Xmx设置最大堆
- java.lang.OutOfMemoryError: GC overhead limit exceeded
执行垃圾收集的时间比例太大，有效运算量大小，默认情况下，如果GC花费的时间超过98%，并且GC回收的内存小于2%
- java.lang.OutOfMemoryError: Direct buffer memory
直接内存溢出 可以通过设置-XX：MaxDirectMemorySize的值来控制之间内存的大小
- java.lang.OutOfMemoryError: Metaspace(元空间)
方法区溢出以及运行时常量池溢出 -XX:MaxMetaspace
- java.lang.OutOfMemoryError: Unable to create new native thread
由于创建的线程太多导致内存溢出
- java.lang.OutOfMemoryError: requested arraysize exceeds VM limited
创建的数组的大小超出了虚拟机的限制

### 11. 解释一下字节码，讲一讲java里面的class文件组成
字节码：一种包含执行程序，由op代码/数据对组成的二进制文件，是一种中间码java中的字节码就是.class文件，.class文件是java程序可以在各个平台上运行的关键，java class是一组以8位字节为基础的二进制流

class文件组成：
+ 魔数（0xCAFFEEBABY），用于判断这个文件是否符合jVM的规范，是否可被接受
+ 次版本号
+ 主版本号，jdk版本可以向下兼容，如果运行版本低于class文件所需版本，将会报错
+ 常量池的所有数据，常量池的容量计数从1开始，将0空出来是为表达“不引用任何一个常量的池项目”的含义
+ 访问标志：用于识别一些类或者接口层次的访问信息，包括这个class是类还是接口，是否定义为public，是否定义为abstract类型，如果是类是否被声明为final
+ 类索引、父类索引与接口索引集合
+ 字段表集合
+ 方法表集合
+ 属性表集合

### 12. JDK自带的JVM监控和性能分析工具
- Jps（JVM Process Status Tool）:虚拟机进程状态工具，可以列出正在运行的虚拟机进程，并显示虚拟机执行主类名称以及这些进程的本地虚拟机唯一ID。Jps可以通过RMI协议查询开启了RMI服务的远程虚拟机进程状态
- Jstat(JVM Statistics Monitoring Tool):是用于监视虚拟机各种运行状态信息的命令行工具。可以显示本地或者远程虚拟机进程中的类装载、内存、垃圾收集、JIT编译等运行数据。
- Jinfo(Configuration Info for Java):实时查看和调整虚拟机各项参数。Jps可以查看显式指定参数，Jinfo可以查看未被显式指定参数的系统默认值。
- Jmap(Memory Map for Java):用于生成堆转储快照（一般为headdump或dump文件），此外还可以查询finalize执行队列、java堆和永久代的详细信息，如空间使用率、当前用的是那种收集器等。
- Jhat(JVM Heap Analysis Tool):Jhat命令与Jmap命令搭配使用，来分析Jmap生成的堆转储快照。
- Jstack（Stack Trace for Java）：用于生成虚拟机当前时刻的线程快照（一般称为threaddump或者javacore文件）。线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈集合。（Tips：JDK1.5 java.lang.Thread类新增getAllStackTraces()方法用于获取虚拟机中所有线程的StackTraceElement对象）
g) hsdis: JIT反编译插件

### 13. minor GC的触发条件
Eden区满了

### 14. Full GC触发条件
- 程序调用了System.gc()
- 老年代区满了
- 方法区满了
- 当分配一个很大的对象，Eden区放不下，进行过minor GC后依然放不下，如果老年代的剩余空间比当前对象大小还小
- 当老年代的剩余空间大小比survivor区历次晋升老年代的平均大小还要小
