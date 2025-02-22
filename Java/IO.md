# Java IO（java.io

1. 是什么
    - jdk提供的一组api，用于应用程序与外部硬件进行输入输出操作。
2. 为什么
3. 怎么做

## 字节流

1. InputStream
    1. FileInputStream
    2. BufferedInputStream
    3. DataInputStream
    4. ObjectInputStream
2. OutputStream
    1. FileOutputStream
    2. BufferedOutputStream
    3. DataOutputStream
    4. ObjectOutputStream

## 字符流

1. 区分字符流、字节流的原因
    - 字符流是由 Java 虚拟机将字节转换得到的，这个过程还算是比较耗时。
    - 如果我们不知道编码类型就很容易出现乱码问题。

2. Reader
    1. InputStreamReader
    2. FileReader
    3. BufferReader

3. Writer
    1. OutputStreamWriter
    2. FileWriter
    3. BufferWriter

### 字节缓冲流

1. 原因
    - IO 操作是很消耗性能的，缓冲流将数据加载至缓冲区，一次性读取/写入多个字节，从而避免频繁的 IO 操作，提高流的传输效率。
    - 字节缓冲流这里采用了装饰器模式来增强InputStream和OutputStream子类对象的功能。

2. BufferedInputStream
3. BufferedOutputStream

### 字符缓冲流

1. BufferReader
2. BufferWriter

### 打印流

- System.out 实际是用于获取一个 PrintStream 对象，print方法实际调用的是 PrintStream 对象的 write 方法。PrintStream 属于字节打印流，与之对应的是 PrintWriter （字符打印流）。
- PrintStream 是 OutputStream 的子类，PrintWriter 是 Writer 的子类。

### 随机访问流

1. RandomAccessFile

### Java IO涉及的设计模式

1. 装饰器
    - 对于字节流来说， FilterInputStream （对应输入流）和FilterOutputStream（对应输出流）是装饰器模式的核心，分别用于增强 InputStream 和OutputStream子类对象的功能。
2. 适配器
    - InputStreamReader 和 OutputStreamWriter 就是两个适配器(Adapter)， 同时，它们两个也是字节流和字符流之间的桥梁。
3. 工厂
    - NIO 中大量用到了工厂模式，比如 Files 类的 newInputStream 方法用于创建 InputStream 对象（静态工厂）、 Paths 类的 get 方法创建 Path 对象（静态工厂）、ZipFileSystem 类（sun.nio包下的类，属于 java.nio 相关的一些内部实现）的 getPath 的方法创建 Path 对象（简单工厂）。
4. 观察者
    - NIO 中的文件目录监听服务基于 WatchService 接口和 Watchable 接口。WatchService 属于观察者，Watchable 属于被观察者。

### Java IO模型

1. 概念：
    - 从计算机结构的视角来看的话， I/O 描述了计算机系统与外部设备之间通信的过程。
    - 从应用程序的视角来看的话，我们的应用程序对操作系统的内核发起 IO 调用（系统调用），操作系统负责的内核执行具体的 IO 操作。也就是说，我们的应用程序实际上只是发起了 IO 操作的调用而已，具体 IO 的执行是由操作系统的内核来完成的。

2. 常见模型：
    1. 同步阻塞 I/O;
    2. 同步非阻塞 I/O
    3. I/O 多路复用
    4. 信号驱动 I/O
    5. 异步 I/O

3. Java中3种常见IO模型
    1. BIO（Blocking I/O）
        - 在 BIO 模型中，IO 操作是阻塞的。当一个线程执行读写操作时，它会被阻塞，直到操作完成。
        - 每个客户端连接都会分配一个独立的线程来处理其 IO 操作。这种模型在处理大量并发连接时，线程资源消耗较大。
        - 适用于连接数较少且固定的场景，如传统的 C/S 架构。
    2. NIO (Non-blocking/New I/O)：java1.4
        - 在 NIO 模型中，IO 操作是非阻塞的。线程可以在等待 IO 操作完成的同时执行其他任务。
        - NIO 使用选择器（Selector）来监听多个通道（Channel）的事件，可以通过一个线程处理多个连接。
        - NIO 引入了缓冲区（Buffer）来提高数据读写的效率。
        - 适用于连接数较多且连接时间较长的场景，如高并发的服务器应用。
    3. AIO (Asynchronous I/O)：java1.7
        - 在 AIO 模型中，IO 操作是异步的。线程发起 IO 操作后，可以立即返回，操作完成后会通过回调函数通知线程。
        - AIO 使用回调机制来处理 IO 操作的完成通知。
        - 适用于连接数较多且 IO 操作时间较长的场景，如高性能的服务器应用。

### Java NIO核心

1. Buffer
2. Channel
3. Selector