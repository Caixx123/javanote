# JVM HotSpot

## Java内存区域

1. 线程私有
    - 程序计数器
    - 虚拟机栈
    - 本地方法栈
2. 线程共享
    - 堆
    - 方法区
    - 直接内存（非运行时数据区的一部分）

3. 程序计数器
    - 字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。
    - 在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。
    - 唯一不会出现 OutOfMemoryError 的内存区域，它的生命周期随着线程的创建而创建，随着线程的结束而死亡。

4. Java虚拟机栈
    - 为虚拟机执行Java方法（字节码）服务
    - 它的生命周期和线程相同，随着线程的创建而创建，随着线程的死亡而死亡。
    - 栈由一个个栈帧组成，而每个栈帧中都拥有：局部变量表、操作数栈、动态链接、方法返回地址。
    - 若栈的内存大小不允许动态扩展，那么当线程请求栈的深度超过当前 Java 虚拟机栈的最大深度的时候，就抛出 StackOverFlowError 错误。
    - 如果栈的内存大小可以动态扩展， 如果虚拟机在动态扩展栈时无法申请到足够的内存空间，则抛出OutOfMemoryError异常。

5. 本地方法栈
    - 为虚拟机使用到的Native方法服务
    - 本地方法被执行的时候，在本地方法栈也会创建一个栈帧，用于存放该本地方法的局部变量表、操作数栈、动态链接、出口信息。

6. 堆
    - Java 虚拟机所管理的内存中最大的一块，Java 堆是所有线程共享的一块内存区域，在虚拟机启动时创建。
    - 此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例以及数组都在这里分配内存。

    1. 字符串常量池
        - JVM 为了提升性能和减少内存消耗针对字符串（String 类）专门开辟的一块区域，主要目的是为了避免字符串的重复创建。

7. 方法区（元空间（Metaspace
    - 是 JVM 运行时数据区域的一块逻辑区域，是各个线程共享的内存区域。
    - 存储已被虚拟机加载的 类信息、字段信息、方法信息、常量、静态变量、即时编译器编译后的代码缓存等数据。

    1. 运行时常量池：存放编译期生成的各种字面量（Literal）和符号引用（Symbolic Reference）的 常量池表(Constant Pool Table) 。
        - 字面量是源代码中的固定值的表示法，即通过字面我们就能知道其值的含义。字面量包括整数、浮点数和字符串字面量。
        - 常见的符号引用包括类符号引用、字段符号引用、方法符号引用、接口方法符号。

    2. 元空间：使用的是本地内存（Native Memory），而不是JVM堆内存

8. 直接内存：是一种特殊的内存缓冲区，并不在 Java 堆或方法区中分配的，而是通过 JNI(Java Native Interface)的方式在本地内存上分配的。

## HotSpot虚拟机

1. 对象的创建
    1. 类加载检查
    2. 分配内存
        - 指针碰撞
        - 空闲列表
        - 内存分配并发问题
            1. CAS+失败重试
            2. TLAB
    3. 初始化零值
    4. 设置对象头
    5. 执行init方法

2. 对象的内存布局
    1. 对象头
        - 标记字段（Mark Word）：用于存储对象自身的运行时数据， 如哈希码（HashCode）、GC 分代年龄、锁状态标志、线程持有的锁、偏向线程 ID、偏向时间戳等等。
        - 类型指针（Klass Word）：对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例。
    2. 实例数据：是对象真正存储的有效信息，也是在程序中所定义的各种类型的字段内容
    3. 对齐填充：不是必然存在的，也没有什么特别的含义，仅仅起占位作用

3. 对象的访问定位
    1. 句柄
    2. 直接指针

## JVM 垃圾回收

1. 堆空间基本结构

2. 内存分配和回收原则
    1. 针对 HotSpot VM 的实现，它里面的 GC 其实准确分类只有两大种：
        - 部分收集 (Partial GC)：
            - 新生代收集（Minor GC / Young GC）：只对新生代进行垃圾收集；
            - 老年代收集（Major GC / Old GC）：只对老年代进行垃圾收集。需要注意的是 Major GC 在有的语境中也用于指代整堆收集；
            - 混合收集（Mixed GC）：对整个新生代和部分老年代进行垃圾收集。
        - 整堆收集 (Full GC)：收集整个 Java 堆和方法区。空间分配担保

3. 死亡对象判断方法
    1. 引用计数法：对象计数器为0时，就需要被回收
    2. 可达性分析法：对象到GC Roots不可达时，就需要被回收
    3. 引用类型
        1. 强引用
        2. 软引用
        3. 弱引用
        4. 虚引用
    
4. 垃圾收集算法（内存回收方法论
    1. 标记清除算法
    2. 标记复制算法（新生代
    3. 标记整理算法（老年代
    4. 分代收集算法

5. 垃圾收集器（内存回收具体实现
    1. JDK 默认垃圾收集器（使用 java -XX:+PrintCommandLineFlags -version 命令查看）：
        - JDK 8：Parallel Scavenge（新生代）+ Parallel Old（老年代）
        - JDK 9 ~ JDK20: G1

    2. Serial
    3. ParNew
    4. Parallel Scavenge
    5. Serial Old
    6. Parallel Old
    7. CMS（Concurrent Mark Sweep
    8. G1（Garbage-First
        - 初始标记
        - 并发标记
        - 最终标记
        - 筛选回收
    9. ZGC

## 类文件结构详解

## 类加载过程详解

1. 类的生命周期（类从被加载到虚拟机内存中开始到卸载出内存为止
    1. 加载
    2. 连接
        1. 验证
        2. 准备
        3. 解析
    3. 初始化
    4. 使用
    5. 卸载
        - 该类的所有的实例对象都已被 GC，也就是说堆不存在该类的实例对象。
        - 该类没有在其他任何地方被引用
        - 该类的类加载器的实例已被 GC

2. 类加载过程（Class文件需要加载到虚拟机之后才能运行使用
    1. 加载
        - 通过全类名获取定义此类的二进制字节流。
        - 将字节流所代表的静态存储结构转换为方法区的运行时数据结构。
        - 在内存中生成一个代表该类的 Class 对象，作为方法区这些数据的访问入口。
    2. 连接
        1. 验证
            - 确保 Class 文件的字节流中包含的信息符合《Java 虚拟机规范》的全部约束要求，保证这些信息被当作代码运行后不会危害虚拟机自身的安全。
            - 文件格式验证（Class 文件格式检查）
            - 元数据验证（字节码语义检查）
            - 字节码验证（程序语义检查）
            - 符号引用验证（类的正确性检查）
        2. 准备
            - 为类变量分配内存并设置类变量初始值的阶段（设置类变量初始值，并非初始化
        3. 解析
            - 虚拟机将常量池内的符号引用替换为直接引用的过程
    3. 初始化
        - 初始化阶段是执行初始化方法 <clinit> ()方法的过程，是类加载的最后一步，这一步 JVM 才开始真正执行类中定义的 Java 程序代码(字节码)

## 类加载器详解

1. 介绍
    - 类加载器是一个负责加载类的对象，用于实现类加载过程中的加载这一步。
    - 每个 Java 类都有一个引用指向加载它的 ClassLoader。
    - 数组类不是通过 ClassLoader 创建的（数组类没有对应的二进制字节流），是由 JVM 直接生成的。
2. 作用
    - 加载 Java 类的字节码（ .class 文件）到 JVM 中（在内存中生成一个代表该类的 Class 对象）。 字节码可以是 Java 源程序（.java文件）经过 javac 编译得来，也可以是通过工具动态生成或者通过网络下载得来。
3. 加载规则
    - 根据需要动态加载
4. 类加载器总结
    - 启动类加载器 BootstrapClassLoader：主要用来加载 JDK 内部的核心类库（ %JAVA_HOME%/lib目录下的 rt.jar、resources.jar、charsets.jar等 jar 包和类）以及被 -Xbootclasspath参数指定的路径下的所有类
    - 扩展类加载器 ExtensionClassLoader：主要负责加载 %JRE_HOME%/lib/ext 目录下的 jar 包和类以及被 java.ext.dirs 系统变量所指定的路径下的所有类。
    - 应用程序类加载器 AppClassLoader：负责加载当前应用 classpath 下的所有 jar 包和类。
    - 自定义加载器
5. 双亲委派模型
    - 介绍
        - ClassLoader 类使用委托模型来搜索类和资源。
        - 双亲委派模型要求除了顶层的启动类加载器外，其余的类加载器都应有自己的父类加载器。
        - ClassLoader 实例会在试图亲自查找类或资源之前，将搜索类或资源的任务委托给其父类加载器\
    - 执行流程
        - 在类加载的时候，系统会首先判断当前类是否被加载过。已经被加载的类会直接返回，否则才会尝试加载（每个父类加载器都会走一遍这个流程）。
        - 类加载器在进行类加载的时候，它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成（调用父加载器 loadClass()方法来加载类）。这样的话，所有的请求最终都会传送到顶层的启动类加载器 BootstrapClassLoader 中。
        - 只有当父加载器反馈自己无法完成这个加载请求（它的搜索范围中没有找到所需的类）时，子加载器才会尝试自己去加载（调用自己的 findClass() 方法来加载类）。
        - 如果子类加载器也无法加载这个类，那么它会抛出一个 ClassNotFoundException 异常。
    - 打破双亲委派
        - 重写loadClass,而不是findClass
        - Tomcat

## 重要JVM参数总结
- 最小堆内存：-Xms<heap size>[unit]
- 最大堆内存：-Xmx<heap size>[unit]
- 最小新生代内存：-XX:NewSize=<young size>[unit]
- 最大新生代内存：-XX:MaxNewSize=<young size>[unit]
- 最小最大新生代内存：-Xmn<young size>[unit]
- 老年代新生代内存比值：-XX:NewRatio=<int>
- 元空间初始大小：-XX:MetaspaceSize=N（注：Metaspace 的初始容量并不是 -XX:MetaspaceSize 设置，无论 -XX:MetaspaceSize 配置什么值，对于 64 位 JVM 来说，Metaspace 的初始容量都是 21807104（约 20.8m）
- 元空间最大大小：-XX:MaxMetaspaceSize=N（注：MetaspaceSize 表示 Metaspace 使用过程中触发 Full GC 的阈值，只对触发起作用

- 串行垃圾收集器：-XX:+UseSerialGC
- 并行垃圾收集器：-XX:+UseParallelGC
- CMS垃圾收集器：-XX:+UseConcMarkSweepGC
- G1垃圾收集器：-XX:+UseG1GC

\# 必选
\# 打印基本 GC 信息
- -XX:+PrintGCDetails
- -XX:+PrintGCDateStamps
\# 打印对象分布
- -XX:+PrintTenuringDistribution
\# 打印堆数据
- -XX:+PrintHeapAtGC
\# 打印Reference处理信息
\# 强引用/弱引用/软引用/虚引用/finalize 相关的方法
- -XX:+PrintReferenceGC
\# 打印STW时间
- -XX:+PrintGCApplicationStoppedTime

\# 可选
\# 打印safepoint信息，进入 STW 阶段之前，需要要找到一个合适的 safepoint
- -XX:+PrintSafepointStatistics
- -XX:PrintSafepointStatisticsCount=1

\# GC日志输出的文件路径
- -Xloggc:/path/to/gc-%t.log
\# 开启日志文件分割
- -XX:+UseGCLogFileRotation
\# 最多分割几个文件，超过之后从头文件开始写
- -XX:NumberOfGCLogFiles=14
\# 每个文件上限大小，超过就触发分割
- -XX:GCLogFileSize=50M

- -XX:+HeapDumpOnOutOfMemoryError
- -XX:HeapDumpPath=./java_pid<pid>.hprof
- -XX:OnOutOfMemoryError="< cmd args >;< cmd args >"
- -XX:+UseGCOverheadLimit

## JDK命令行工具

- jps （JVM Process Status）: 类似 UNIX 的 ps 命令。用于查看所有 Java 进程的启动类、传入参数和 Java 虚拟机参数等信息；
- jstat（JVM Statistics Monitoring Tool）: 用于收集 HotSpot 虚拟机各方面的运行数据;
- jinfo (Configuration Info for Java) : Configuration Info for Java,显示虚拟机配置信息;
- jmap (Memory Map for Java) : 生成堆转储快照;
- jhat (JVM Heap Dump Browser) : 用于分析 heapdump 文件，它会建立一个 HTTP/HTML 服务器，让用户可以在浏览器上查看分析结果。JDK9 移除了 jhat；
- jstack (Stack Trace for Java) : 生成虚拟机当前时刻的线程快照，线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合

## JDK可视化分析工具

- JConsole：Java 监视与管理控制台
- Visual VM:多合一故障处理工具
- MAT：内存分析器工具
- JProfiler

## JVM线上问题排查和性能调优案例