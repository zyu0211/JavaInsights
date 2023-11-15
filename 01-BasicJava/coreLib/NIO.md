## 1、NIO 概述

### 1.1 概述

IO 的操作方式通常分为几种：**同步阻塞 BIO**、**同步非阻塞 NIO**、**异步非阻塞 AIO**。

- 在 JDK1.4 之前，我们建立网络连接的时候采用的是 BIO 模式。
- Java NIO 是从 Java 1.4 版本开始引入的一个新的 IO API，可以替代标准的 Java IO API。NIO 支持**面向缓冲区的、基于通道的** IO 操作。NIO 将以更加高效的方式进行文件的读写操作。BIO 与 NIO 一个比较重要的不同是，我们使用 BIO 的时候往往会引入多线程，每个连接对应一个单独的线程；而 NIO 则是使用单线程或者只使用少量的多线程，让连接共用一个线程。
- AIO 也就是 NIO 2，在 Java 7 中引入了 NIO 的改进版 NIO 2，它是异步非阻塞的 IO 模型。

Java NIO 几个核心组成部分：**Channels**、**Buffers**、**Selectors**

- Channel 是**双向**的，既可以用来进行读操作，又可以用来进行写操作。NIO 中的 Channel 的主要实现类有：FileChannel、DatagramChannel、SocketChannel 和 ServerSocketChannel，分别可以对应文件 IO、UDP 和 TCP（Client 和 Server）。
- Buffer 实现有：ByteBuffer、CharBuffer、DoubleBuffer、FloatBuffer、IntBuffer、LongBuffe、ShortBuffer，分别对应基本数据类型: byte、char、double、float、int、long、short。
- Selector 运行单线程处理多个 Channel，如果你的应用打开了多个通道，但每个连接的流量都很低，使用 Selector 就会很方便。例如在一个聊天服务器中，要使用 Selector，得向 Selector 注册 Channel，然后调用它的 select() 方法。这个方法会一直阻塞到某个注册的通道有事件就绪。一旦这个方法返回，线程就可以处理这些事件，事件的例子有如新的连接进来、数据接收等。

虽然 Java NIO 中除此之外还有很多类和组件，但 Channel，Buffer 和 Selector 构成了核心的 API。其它组件，如 Pipe 和 FileLock，只不过是与三个核心组件共同使用的工具类。

### 1.2 阻塞 IO

阻塞 IO 是最传统的一种 IO 模型，即在读写数据过程中会发生阻塞现象，直至有可供读取的数据或者数据能够写入。

在 BIO 模式中，服务器会为**每个客户端请求建立一个线程**，由该线程单独负责处理一个客户请求，这种模式虽然简单方便，但由于服务器为每个客户端的连接都采用一个线程去处理，使得资源占用非常大。因此，当连接数量达到上限时，如果再有用户请求连接，直接会导致资源瓶颈，严重的可能会直接导致服务器崩溃。

大多数情况下为了避免上述问题，都采用了**线程池模型**。也就是创建一个固定大小的线程池，如果有客户端请求，就从线程池中取一个空闲线程来处理，当客户端处理完操作之后，就会释放对线程的占用。因此这样就避免为每一个客户端都要创建线程带来的资源浪费，使得线程可以重用。但线程池也有它的弊端，如果连接大多是长连接，可能会导致在一段时间内，线程池中的线程都被占用，那么当再有客户端请求连接时，由于没有空闲线程来处理，就会导致客户端连接失败。

### 1.3 非阻塞 IO

NIO 采用非阻塞模式，基于 Reactor 模式的工作方式，I/O 调用不会被阻塞，它的实现过程是：会先对每个客户端注册感兴趣的事件，然后有一个线程专门去轮询每个客户端是否有事件发生，当有事件发生时，便顺序处理每个事件，当所有事件处理完之后，便再转去继续轮询。

NIO 中实现非阻塞 I/O 的核心对象就是 Selector，Selector 就是注册各种 I/O 事件的地方，而且当我们感兴趣的事件发生时，就是这个对象告诉我们所发生的事件。

NIO 的最重要的地方是当一个连接创建后，不需要对应一个线程，这个连接会被注册到多路复用器（选择器）上面，一个选择器线程可以同时处理成千上万个连接，系统不必创建大量的线程，也不必维护这些线程，从而大大减小了系统的开销。

### 1.4 异步 IO

AIO 是异步非阻塞的 IO 模型。异步 IO 是基于事件和回调机制实现的，也就是说 AIO 模式不需要 selector 操作，而是是事件驱动形式，也就是当客户端发送数据之后，会主动通知服务器，接着服务器再进行读写操作。

Java 的 AIO API 其实就是 Proactor 模式的应用，和 Reactor 模式类似。Reactor 和 Proactor 模式的主要区别就是真正的读取和写入操作是有谁来完成的，Reactor 中需要应用程序自己读取或者写入数据，而 Proactor 模式中，应用程序不需要进行实际的读写过程，它只需要从缓存区读取或者写入即可，操作系统会读取缓存区或者写入缓存区到真正的 IO 设备。

## 2、Channel

### 2.1 FileChannel

常见方法：

|方法|描述|
|---|---|
|int read(ByteBuffer buf)|从 Channel 中读取数据到 ByteBuffer|
|long read(ByteBuffer[] bufs)|将 Channel 中的数据“分散”到 ByteBuffer[] 中|
|int write(ByteBuffer buf)|将 ByteBuffer 中数据写入到 Channel|
|int write(ByteBuffer[] bufs)|将 ByteBuffer[]中的数据“聚集”到 Channel 中|
|long position()|返回此通道的文件位置|
|FileChannel position(long p)|设置此通道的文件位置|
|long size()|返回此通道的文件的当前大小|
|FileChannel truncate(long s)|将此通道的文件截取为给定大小|
|void forse(boolean metaData)|强制此通道的所有文件更新写入到存储设备中|
|transferFrom(Channel src, long pos, long count)|将字节从给定的通道传输到此通道的文件中|
|transferTo(long pos, long count, Channel target)|将数据从当前通道传输到其他的 channel 中|

一个示例：

```java
public class FileChannelDemo {
    public static void main(String[] args) throws IOException {
        // 创建 FileChannel
        RandomAccessFile aFile = new RandomAccessFile("d:\\01.txt", "rw");
        FileChannel inChannel = aFile.getChannel();
        RandomAccessFile bFile = new RandomAccessFile("d:\\02.txt", "rw");
        FileChannel outChannel = bFile.getChannel();
          
        // 创建 Buffer
        ByteBuffer buf = ByteBuffer.allocate(48);
        
        // 读取数据到 Buffer 中
        int bytesRead = inChannel.read(buf);
        while (bytesRead != -1) {
            System.out.println("读取： " + bytesRead);
            // 反转读写模式
            buf.flip();
            while (buf.hasRemaining()) {
                System.out.print((char) buf.get());
                outChannel.write(buf);
            }  
            // 清除缓冲区内容
            buf.clear();
            bytesRead = inChannel.read(buf);
        }  
          
        // 关闭 FileChannel
        aFile.close();
        inChannel.close();
        bFile.close();
        outChannel.close();
        System.out.println("操作结束");
    }  
}
```

### 2.2 ServerSocketChannel

ServerSocketChannel 是一个基于通道的 socket 监听器。它同我们所熟悉的 java.net.ServerSocket 执行相同的任务，不过它增加了通道语义，因此能够在非阻塞模式下运行。

由于 ServerSocketChannel 没有 bind() 方法，因此有必要取出对等的 socket，并使用它来绑定到一个端口以开始监听连接。我们也是使用对等 ServerSocket 的 API 来根据需要设置其他的 socket 选项。

同 java.net.ServerSocket 一样，ServerSocketChannel 也有 accept( ) 方法。ServerSocketChannel 的 accept()方法会返回 SocketChannel 类型对象，SocketChannel 可以在非阻塞模式下运行。

常见方法：

|方法|描述|
|---|---|
|static ServerSocketChannel open()|返回一个 ServerSocketChannel 对象|
|abstract ServerSocket socket()|检索与此通道关联的服务器套接字|
|abstract SocketChannel accept()|接受与此通道的套接字建立的连接|

一个示例：

```java
public class ServerSocketChannelDemo {
    
    public static final String GREETING = "Hello java nio.\r\n";
    
    public static void main(String[] argv) throws Exception {
        // 端口号
        int port = 1234; // default
        if (argv.length > 0) {
            port = Integer.parseInt(argv[0]);
        }  
        // buffer
        ByteBuffer buffer = ByteBuffer.wrap(GREETING.getBytes());
        // ServerSocketChannel
        ServerSocketChannel ssc = ServerSocketChannel.open();
        // 绑定端口，监听 1234 端口
        ssc.socket().bind(new InetSocketAddress(port));
        // 设置非阻塞
        ssc.configureBlocking(false);
        // 死循环，监听新连接的传入
        while (true) {
            System.out.println("Waiting for connections");  
            // 在非阻塞模式下，accept() 方法会立刻返回  
            // 如果还没有新进来的连接,返回的将是 null
            // 因此，需要检查返回的 SocketChannel 是否是 null
            SocketChannel sc = ssc.accept();
            if (sc == null) {   // 没有新连接传入
                System.out.println("null");
                Thread.sleep(2000);
            } else {    // 有新连接传入
                System.out.println("Incoming connection from: " +
                                    sc.socket().getRemoteSocketAddress());
                // 指针指向0
                buffer.rewind();
                sc.write(buffer);
                sc.close();
            }  
        }  
    }  
}
```

### 2.3 SocketChannel

SocketChannel 是一个连接到 TCP 网络套接字的通道。SocketChannel 是一种面向流连接 sockets 套接字的可选择通道。从这里可以看出：

- SocketChannel 是用来连接 Socket 套接字
- SocketChannel 主要用途用来处理网络 I/O 的通道
- SocketChannel 是基于 TCP 连接传输
- SocketChannel 实现了可选择通道，可以被多路复用的

SocketChannel 特点：

1. 对于已经存在的 socket 不能创建 SocketChannel
2. SocketChannel 中提供的 open 接口创建的 Channel 并没有进行网络级联，需要使用 connect 接口连接到指定地址
3. 未进行连接的 SocketChannle 执行 I/O 操作时，会抛出 NotYetConnectedException
4. SocketChannel 支持两种 I/O 模式：阻塞式和非阻塞式
5. SocketChannel 支持异步关闭。如果 SocketChannel 在一个线程上 read 阻塞，另一个线程对该SocketChannel 调用 shutdownInput，则读阻塞的线程将返回-1 表示没有读取任何数据；如果SocketChannel 在一个线程上 write 阻塞，另一个线程对该SocketChannel 调用 shutdownWrite，则写阻塞的线程将抛出 AsynchronousCloseException
6. SocketChannel 支持设定参数： SO_SNDBUF 套接字发送缓冲区大小 SO_RCVBUF 套接字接收缓冲区大小 SO_KEEPALIVE 保活连接 O_REUSEADDR 复用地址 SO_LINGER 有数据传输时延缓关闭 Channel (只有在非阻塞模式下有用) TCP_NODELAY 禁用 Nagle 算法

常见方法：

|方法|描述|
|---|---|
|static SocketChannel open()|打开 Socket 通道|
|boolean connect(SocketAddress remote)|连接此通道的套接字|
|boolean isConnected()|测试 SocketChannel 是否已经被连接|
|boolean isConnectedPending()|测试 SocketChannel 是否正在进行连接|
|boolean finishConnect()|校验正在进行套接字连接的 SocketChannel 是否已经完成连接|
|setOption(SocketOption\<T\> name, T value)|设置套接字选项的值|

一个示例：

```java
public class SocketChannelDemo {  
    public static void main(String[] args) throws IOException {  
        // 创建 SocketChannel  
        // 方式一：  
        SocketChannel socketChannel1 = SocketChannel.open(new  
InetSocketAddress("www.baidu.com", 80));  
        // 方式二：  
        SocketChannel socketChanne2 = SocketChannel.open();  
        socketChanne2.connect(new InetSocketAddress("www.baidu.com", 80));  
          
        // 连接校验  
        socketChannel.isOpen();// 测试 SocketChannel 是否为 open 状态  
        socketChannel.isConnected();//测试 SocketChannel 是否已经被连接  
        socketChannel.isConnectionPending();//测试 SocketChannel 是否在进行连接  
        socketChannel.finishConnect();//校验 SocketChannel 是否已经完成连接  
          
        // 设置非阻塞  
        socketChannel.configureBlocking(false);  
          
        ByteBuffer byteBuffer = ByteBuffer.allocate(16);  
        socketChannel.read(byteBuffer);  
        socketChannel.close();  
        System.out.println("read over");  
    }  
}
```

### 2.4 DatagramChannel

每一个 DatagramChannel 对象也有一个关联的 DatagramSocket 对象。正如 SocketChannel 模拟连接导向的流协议（如 TCP/IP），DatagramChannel 则模拟包导向的无连接协议（如 UDP/IP）。DatagramChannel 是无连接的，每个数据报（datagram）都是一个自包含的实体，拥有它自己的目的地址及不依赖其他数据报的数据负载。与面向流的的 socket 不同，DatagramChannel 可以发送单独的数据报给不同的目的地址。同样，DatagramChannel 对象也可以接收来自任意地址的数据包，每个到达的数据报都含有关于它来自何处的信息（源地址）。

一个示例：

```java
/**  
* 发包端：使用 send() 方法  
*/  
@Test  
public void sendDatagram() throws IOException, InterruptedException {  
    // 创建 DatagramChannel  
    DatagramChannel sendChannel = DatagramChannel.open();  
    // 发送地址  
    InetSocketAddress sendAddress = new InetSocketAddress("127.0.0.1",9999);  
    // 死循环，一直发送  
    while (true) {  
        ByteBuffer buf = ByteBuffer.wrap("发包".getBytes("UTF-8"));  
        sendChannel.send(buf, sendAddress);  
        System.out.println("发包端完成发包");  
        Thread.sleep(1000);  
    }  
}  
 
/**  
* 收包端：使用 receive() 方法  
*/  
@Test  
public void receive() throws IOException {  
    // 创建 DatagramChannel  
    DatagramChannel receiveChannel = DatagramChannel.open();  
    InetSocketAddress receiveAddress = new InetSocketAddress(9999);  
    // 绑定，监听 9999 端口  
    receiveChannel.bind(receiveAddress);  
      
    ByteBuffer receiveBuffer= ByteBuffer.allocate(512);  
    while (true) {  
        receiveBuffer.clear();  
        SocketAddress sendAddress = receiveChannel.receive(receiveBuffer);  
        receiveBuffer.flip();  
        System.out.print(sendAddress.toString() + " ");  
        System.out.println(Charset.forName("UTF-8").decode(receiveBuffer));  
    }  
}  

/**  
* read() 和 write() 方法演示  
*/  
@Test  
public void testConect1() throws IOException {  
    DatagramChannel connChannel = DatagramChannel.open();  
    connChannel.bind(new InetSocketAddress(9999 ));  
    connChannel.connect(new InetSocketAddress("127.0.0.1",9999));  
    // 写  
    connChannel.write(ByteBuffer.wrap("发包".getBytes("UTF-8")));  
    // 读  
    ByteBuffer readBuffer = ByteBuffer.allocate(512);  
    while (true) {  
        try {  
            readBuffer.clear();  
            connChannel.read(readBuffer);  
            readBuffer.flip();  
            System.out.println(Charset.forName("UTF-8").decode(readBuffer));  
        }catch(Exception e) {  
        }  
    }  
}
```

## 3、Buffer

Java NIO 中的 Buffer 用于和 NIO 通道进行交互。数据是从通道读入缓冲区，从缓冲区写入到通道中的。

缓冲区本质上是一块可以写入数据，然后可以从中读取数据的内存。这块内存被包装成 NIO Buffer 对象，并提供了一组方法，用来方便的访问该块内存。缓冲区实际上是一个容器对象，更直接的说，其实就是一个数组，在 NIO 库中，所有数据都是用缓冲区处理的。**在读取数据时，它是直接读到缓冲区中的；在写入数时，它也是写入到缓冲区中的；任何时候访问 NIO 中的数据，都是将它放到缓冲区中**。而在面向流  I/O 系统中，所有数据都是直接写入或者直接将数据读取到 Stream 对象中。在 NIO 中，所有的缓冲区类型都继承于抽象类 Buffer，最常用的就是 ByteBuffer，**对于 Java 中的基本类型，基本都有一个具体 Buffer 类型与之相对应**。

### 3.1 Buffer 基本用法

使用 Buffer 读写数据，四个基本步骤：

1. 写入数据到 Buffer
2. 调用 flip() 方法
3. 从 Buffer 中读取数据
4. 调用 clear() 方法或者 compact() 方法

> 当向 buffer 写入数据时，buffer 会记录写了多少数据。一旦要读取数据，需要通过 flip() 方法将 Buffer 从写模式切换到读模式。在读模式下，可以读取之前写入到 buffer 的所有数据。一旦读完了所有数据，就需要清空缓冲区，让它可以再次被写入。有两种方式能清空缓冲区：调用 clear() 或 compact() 方法。clear() 方法会清空整个缓冲区。compact() 方法只会清除已经读过的数据。任何未读的数据都被移到缓冲区的起始处，新写入的数据将放到缓冲区未读数据的后面。

一个示例：

```java
@Test  
public void testIntBuffer() throws IOException {  
    // 分配新的 int 缓冲区，参数为缓冲区容量  
    // 新缓冲区的当前位置将为零，其界限(限制位置)将为其容量。  
    // 它将具有一个底层实现数组，其数组偏移量将为零。  
    IntBuffer buffer = IntBuffer.allocate(8);  
    for (int i = 0; i < buffer.capacity(); ++i) {  
        int j = 2 * (i + 1);  
        // 将给定整数写入此缓冲区的当前位置，当前位置递增  
        buffer.put(j);  
    }  
    // 重设此缓冲区，将限制设置为当前位置，然后将当前位置设置为 0  
    buffer.flip();  
    // 查看在当前位置和限制位置之间是否有元素  
    while (buffer.hasRemaining()) {  
        // 读取此缓冲区当前位置的整数，然后当前位置递增  
        int j = buffer.get();  
        System.out.print(j + " ");  
    }  
}
```

三个重要属性：

- Capacity
    - 作为一个内存块，Buffer 有一个固定的大小值，也叫“capacity”。你只能往里写 capacity 个 byte、long，char 等类型。一旦 Buffer 满了，需要将其清空（通过读数据或者清除数据）才能继续往里写数据。
- Position
    - 写数据到 Buffer 中时，position 表示写入数据的当前位置，position 的初始值为 0。当一个 byte、long 等数据写到 Buffer 后， position 会向下移动到下一个可插入数据的 Buffer 单元。position 最大可为 capacity – 1（因为 position 的初始值为 0）。
    - 读数据到 Buffer 中时，position 表示读入数据的当前位置，如 position=2 时表示已开始读入了 3 个 byte，或从第 3 个 byte 开始读取。通过 ByteBuffer.flip() 切换到读模式时 position 会被重置为 0，当 Buffer 从 position 读入数据后，position 会下移到下一个可读入的数据 Buffer 单元。
- limit
    - 写数据时，limit 表示可对 Buffer 最多写入多少个数据。写模式下，limit 等于 Buffer 的 capacity。
    - 读数据时，limit 表示 Buffer 里有多少可读数据（not null 的数据），因此能读到之前写入的所有数据（limit 被设置成已写数据的数量，这个值在写模式下就是 position）。

几个常用方法：

- allocate()：Buffer 对象存储空间分配
- put()：写数据到 Buffer 里
- get()：从 Buffer 中读取数据
- flip()：将 Buffer 从写模式切换到读模式。调用 flip() 方法会将 position 设回 0，并将 limit 设置成之前 position 的值
- rewind()：将 position 设回 0，可以重读 Buffer 中的所有数据
- clear()：position 将被设回 0，limit 被设置成 capacity 的值。Buffer 中的数据并未清除，只是这些标记告诉我们可以从哪里开始往 Buffer 里写数据
- compact()：将所有未读的数据拷贝到 Buffer 起始处，然后将 position 设到最后一个未读元素正后面，limit 属性依然像 clear()方法一样，设置成 capacity
- mark()：可以标记 Buffer 中的一个特定 position，之后可以调用 reset() 方法恢复到position处
- reset()：恢复到 mark() 标记的 position 处

### 3.2 缓冲区操作

_**缓冲区分片**_

在 NIO 中，除了可以分配或者包装一个缓冲区对象外，还可以根据现有的缓冲区对象来创建一个子缓冲区，即在现有缓冲区上切出一片来作为一个新的缓冲区，但现有的缓冲区与创建的子缓冲区在底层数组层面上是数据共享的，也就是说，子缓冲区相当于现有缓冲区的视图窗口。调用 `slice()` 方法可以创建一个子缓冲区。

```java
@Test  
public void demo1() throws IOException {  
    ByteBuffer buffer = ByteBuffer.allocate(10);  
    // 缓冲区中的数据 0-9  
    for (int i = 0; i < buffer.capacity(); ++i) {  
        buffer.put((byte) i);  
    }  
    // 创建子缓冲区  
    buffer.position(3);  
    buffer.limit(7);  
    ByteBuffer slice = buffer.slice();  
    // 改变子缓冲区的内容  
    for (int i = 0; i < slice.capacity(); ++i) {  
        byte b = slice.get(i);  
        b *= 10;  
        slice.put(i, b);  
    }  
    buffer.position(0);
    buffer.limit(buffer.capacity());  
    while (buffer.remaining() > 0) {  
        System.out.println(buffer.get());  
    }  
}
```

_**只读缓冲区**_

只读缓冲区非常简单，可以读取它们，但是不能向它们写入数据。通过调用 `asReadOnlyBuffer()` 方法，将任何常规缓冲区转换为只读缓冲区，返回一个与原缓冲区完全相同的缓冲区，并与原缓冲区共享数据，只不过它是只读的。如果原缓冲区的内容发生了变化，只读缓冲区的内容也随之发生变化。如果尝试修改只读缓冲区的内容，则会报 ReadOnlyBufferException 异常。

```java
@Test  
public void demo2() throws IOException {  
    ByteBuffer buffer = ByteBuffer.allocate(10);  
    // 缓冲区中的数据 0-9  
    for (int i = 0; i < buffer.capacity(); ++i) {  
        buffer.put((byte) i);  
    }  
    // 创建只读缓冲区  
    ByteBuffer readonly = buffer.asReadOnlyBuffer();  
    // 改变原缓冲区的内容  
    for (int i = 0; i < buffer.capacity(); ++i) {  
        byte b = buffer.get(i);  
        b *= 10;  
        buffer.put(i, b);  
    }  
    readonly.position(0);  
    readonly.limit(buffer.capacity());  
    // 只读缓冲区的内容也随之改变  
    while (readonly.remaining() > 0) {  
        System.out.println(readonly.get());  
    }  
}
```

_**直接缓冲区**_

直接缓冲区是为加快 I/O 速度，使用一种特殊方式为其分配内存的缓冲区，JDK 文档中的描述为：给定一个直接字节缓冲区，Java 虚拟机将尽最大努力直接对它执行本机 I/O 操作。也就是说，它会在每一次调用底层操作系统的本机 I/O 操作之前(或之后)，尝试避免将缓冲区的内容拷贝到一个中间缓冲区中或者从一个中间缓冲区中拷贝数据。要分配直接缓冲区，需要调用 `allocateDirect()`方法，而不是 allocate() 方法，使用方式与普通缓冲区并无区别。

_**内存映射文件 I/O**_

内存映射文件 I/O 是一种读和写文件数据的方法，它可以比常规的基于流或者基于通道的 I/O 快的多。内存映射文件 I/O 是通过使文件中的数据出现为内存数组的内容来完成的，这其初听起来似乎不过就是将整个文件读到内存中，但是事实上并不是这样。一般来说，只有文件中实际读取或者写入的部分才会映射到内存中。

```java
static private final int start = 0;
static private final int size = 1024;

static public void main(String args[]) throws Exception {
    RandomAccessFile raf = new RandomAccessFile("d:\\01.txt","rw");
    FileChannel fc = raf.getChannel();
    MappedByteBuffer mbb = fc.map(FileChannel.MapMode.READ_WRITE, start, size);
    mbb.put(0, (byte) 97);
    mbb.put(1023, (byte) 122);
    raf.close();
}
```

## 4、Selector

### 4.1 简介

Selector 是 Java NIO 核心组件中的一个，用于检查一个或多个 NIO Channel 的状态是否处于可读、可写，可以实现单线程管理多个 channels，也就是可以管理多个网络链接。使用 Selector 的好处在于：使用更少的线程来就可以来处理通道了， 相比使用多个线程，避免了线程上下文切换带来的开销。

不是所有的 Channel 都可以被 Selector 复用的。判断一个 Channel 能被 Selector 复用：判断他是否继承了一个抽象类 **SelectableChannel**，如果继承了 SelectableChannel，则可以被复用，否则不能。所有 socket 通道，都继承了 SelectableChannel 类都是可选择的，包括从管道(Pipe)对象的中获得的通道。**而 FileChannel 类，没有继承 SelectableChannel，因此是不是可选通道**。

通道和选择器之间的关系，使用**注册**的方式完成。使用 `Channel.register(Selector sel，int ops)` 方法，将一个通道注册到一个选择器，第一个参数指定通道要注册的选择器，第二个参数指定选择器需要查询的通道操作。

选择器查询的通道操作，从类型来分，包括以下四种：

- 可读 : SelectionKey.OP_READ
- 可写 : SelectionKey.OP_WRITE
- 连接 : SelectionKey.OP_CONNECT
- 接收 : SelectionKey.OP_ACCEPT

如果 Selector 对通道的多操作类型感兴趣，可以用 “位或” 操作符来实现：`int key = SelectionKey.OP_READ | SelectionKey.OP_WRITE ;`

Selector 查询的不是通道的操作，而是通道的某个操作的一种**就绪状态**。一旦通道具备完成某个操作的条件，表示该通道的某个操作已经就绪，就可以被 Selector 查询到，程序可以对通道进行对应的操作。例如，一个 ServerSocketChannel 服务器通道准备好接收新进入的连接，则处于 “接收就绪” (OP_ACCEPT) 状态；某个 SocketChannel 通道可以连接到一个服务器，则处于 “连接就绪” (OP_CONNECT)；一个有数据可读的通道，可以说是 “读就绪” (OP_READ)；一个等待写数据的通道可以说是 “写就绪” (OP_WRITE)。

Channel 注册到选择器后，并且一旦通道处于某种就绪的状态，就可以被选择器查询到。这个工作，使用选择器 Selector 的 `select()` 方法完成，select 方法的作用，对感兴趣的通道操作，进行就绪状态的查询。Selector 可以不断的查询 Channel 中发生的操作的就绪状态，并且挑选感兴趣的操作就绪状态。一旦通道有操作的就绪状态达成，并且是 Selector 感兴趣的操作，就会被 Selector 选中，放入选择**键集合**中。

一个选择键，首先是包含了注册在 Selector 的通道操作的类型，比方说 SelectionKey.OP_READ。也包含了特定的通道与特定的选择器之间的注册关系。开发应用程序时，选择键是编程的关键。NIO 的编程，就是根据对应的选择键，进行不同的业务逻辑处理。选择键的概念，和事件的概念比较相似，一个选择键类似监听器模式里边的一个事件。由于 Selector 不是事件触发的模式，而是主动去查询的模式，所以不叫事件 Event，而是叫 SelectionKey 选择键。

### 4.2 基本使用

使用步骤：

1. 创建 Selector
2. 创建 SeverSocketChannel，并绑定监听端口
3. channel 设置为非阻塞模式
4. 注册 Channel 到 Selector
5. 调用 Selector 的 select 方法（循环调用），监测通道的就绪状况
6. 调用 selectKeys 方法获取就绪 channel 集合
7. 遍历就绪 channel 集合，判断就绪事件类型，实现具体的业务操作
8. 根据业务，决定是否需要再次注册监听事件

一个示例：

```java
/**  
 * 服务端代码  
 */  
public void ServerDemo() {  
    try {  
        ServerSocketChannel ssc = ServerSocketChannel.open();  
        ssc.socket().bind(new InetSocketAddress(8000));  
        ssc.configureBlocking(false);  
        // 获取 Selector 选择器  
        Selector selector = Selector.open();  
        // 注册 channel，并且指定感兴趣的事件是 Accept  
        ssc.register(selector, SelectionKey.OP_ACCEPT);
        
        ByteBuffer readBuff = ByteBuffer.allocate(1024);  
        ByteBuffer writeBuff = ByteBuffer.allocate(128);  
        writeBuff.put("received".getBytes());  
        writeBuff.flip();
          
        // 选择器进行轮询，进行后续操作
        while (selector.select() > 0) {  
            Set<SelectionKey> keys = selector.selectedKeys();
            Iterator<SelectionKey> it = keys.iterator();  
            while (it.hasNext()) {
                // 获取就绪状态  
                SelectionKey key = it.next();  
                // 根据就绪状态，判断进行什么操作  
                if (key.isAcceptable()) {  
                    // 创建新的连接，并且把连接注册到 selector 上
                    // 并声明这个 channel 只对读操作感兴趣  
                    SocketChannel socketChannel = ssc.accept();
                    socketChannel.configureBlocking(false);  
                    socketChannel.register(selector, SelectionKey.OP_READ); 
                } else if (key.isReadable()) {  
                    SocketChannel socketChannel = (SocketChannel) key.channel();  
                    readBuff.clear();
                    socketChannel.read(readBuff);
                    readBuff.flip();
                    System.out.println("received : " + new String(readBuff.array()));  
                    key.interestOps(SelectionKey.OP_WRITE);
                } else if (key.isWritable()) {  
                    writeBuff.rewind();
                    SocketChannel socketChannel = (SocketChannel) key.channel();  
                    socketChannel.write(writeBuff);  
                    key.interestOps(SelectionKey.OP_READ);  
                }  
            }  
        }  
    } catch (IOException e) {  
        e.printStackTrace();  
    }  
}  
​  
/**  
 * 客户端代码  
 */  
public void ClientDemo() {  
    try {  
        SocketChannel socketChannel = SocketChannel.open();  
        socketChannel.connect(new InetSocketAddress("127.0.0.1", 8000));  
        ByteBuffer writeBuffer = ByteBuffer.allocate(32);  
        ByteBuffer readBuffer = ByteBuffer.allocate(32);  
        writeBuffer.put("hello".getBytes());  
        writeBuffer.flip();  
        while (true) {  
            writeBuffer.rewind();  
            socketChannel.write(writeBuffer);  
            readBuffer.clear();  
            socketChannel.read(readBuffer);  
        }  
    } catch (IOException e) {  
        e.printStackTrace();  
    }  
}
```

## 5、Pipe 和 FileLock

### 5.1 Pipe

管道 Pipe 是 2 个线程之间的单向数据连接。Pipe 有一个 source 通道和一个 sink 通道，数据会被写到 sink 通道，从 source 通道读取。

一个示例：

```java
public void testPipe() throws IOException {  
    // 1、获取管道  
    Pipe pipe = Pipe.open();  
    // 2、获取 sink 通道，用来传送数据  
    Pipe.SinkChannel sinkChannel = pipe.sink();  
    // 3、申请一定大小的缓冲区  
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);  
    byteBuffer.put("helloJava".getBytes());  
    byteBuffer.flip();  
    // 4、sink 发送数据  
    sinkChannel.write(byteBuffer);  
    // 5、创建接收 pipe 数据的 source 通道  
    Pipe.SourceChannel sourceChannel = pipe.source();  
    // 6、接收数据，并保存到缓冲区中  
    ByteBuffer byteBuffer2 = ByteBuffer.allocate(1024);  
    int length = sourceChannel.read(byteBuffer2);  
    System.out.println(new String(byteBuffer2.array(), 0, length));  
    sourceChannel.close();
    sinkChannel.close();  
}
```

### 5.2 FileLock

文件锁在 OS 中很常见，如果多个程序同时访问、修改同一个文件，很容易因为文件数据不同步而出现问题。给文件加一个锁，同一时间，只能有一个程序修改此文件，或者程序都只能读此文件，这就解决了同步问题。

文件锁是**进程级别**的，不是线程级别的。文件锁可以解决多个进程并发访问、修改同一个文件的问题，但不能解决多线程并发访问、修改同一文件的问题。使用文件锁时，同一进程内的多个线程，可以同时访问、修改此文件。

文件锁是当前程序所属的 JVM 实例持有的，一旦获取到文件锁（对文件加锁），要调用 release()，或者关闭对应的 FileChannel 对象，或者当前 JVM 退出，才会释放这个锁。一旦某个进程（比如说 JVM 实例）对某个文件加锁，则在释放这个锁之前，此进程不能再对此文件加锁，就是说 JVM 实例在同一文件上的文件锁是不重叠的（进程级别不能重复在同一文件上获取锁）。

文件锁分类：

- 排它锁：又叫独占锁。对文件加排它锁后，该进程可以对此文件进行读写，该进程独占此文件，其他进程不能读写此文件，直到该进程释放文件锁。
- 共享锁：某个进程对文件加共享锁，其他进程也可以访问此文件，但这些进程都只能读此文件，不能写。只要还有一个进程持有共享锁，此文件就只能读，不能写。

使用步骤：

1. 创建 FileChannel 对象，文件锁只能通过 FileChannel 对象来使用 `FileChannel fileChannel = new FileOutputStream("./1.txt").getChannel();`
2. 对文件加锁 `FileLock lock = fileChannel.lock();`
3. 对此文件进行一些读写操作。
4. 释放锁 `lock.release();`

一个示例：

```java
public static void main(String[] args) throws IOException {  
    String input = "FileLockTest";  
    System.out.println("输入 :" + input);  
    ByteBuffer buf = ByteBuffer.wrap(input.getBytes());  
    String fp = "D:\\01.txt";
    Path pt = Paths.get(fp);
    FileChannel channel = FileChannel.open(pt, StandardOpenOption.WRITE,StandardOpenOption.APPEND);  
    // position of a cursor at the end of file  
    channel.position(channel.size() - 1);   
    // 获得锁方法一：lock()，阻塞方法，当文件锁不可用时，当前进程会被挂起  
    //lock = channel.lock();// 无参 lock()为独占锁  
    // lock = channel.lock(0L, Long.MAX_VALUE, true);//有参 lock()为共享锁，有写操作会报异常  
    // 获得锁方法二：trylock()，非阻塞的方法，当文件锁不可用时，tryLock()会得到 null 值  
    FileLock lock = channel.tryLock(0, Long.MAX_VALUE, false);  
      
    System.out.println("共享锁 shared: " + lock.isShared());  
    channel.write(buf);  
    channel.close(); // Releases the Lock  
    System.out.println("写操作完成.");  
    //读取数据  
    readPrint(fp);  
}  
 
public static void readPrint(String path) throws IOException {  
    FileReader filereader = new FileReader(path);  
    BufferedReader bufferedreader = new BufferedReader(filereader);  
    String tr = bufferedreader.readLine();  
    System.out.println("读取内容: ");  
    while (tr != null) {  
        System.out.println(" " + tr);  
        tr = bufferedreader.readLine();  
    }  
    filereader.close();  
    bufferedreader.close();  
}
```

## 6、其他

### 6.1 Path

Java Path 接口是在 Java7 中添加到 Java NIO 的。Path 接口位于 java.nio.file 包中，所以 Path 接口的完全限定名称为 java.nio.file.Path。Java Path 实例表示文件系统中的路径，一个路径可以指向一个文件或目录，路径可以是绝对路径，也可以是相对路径。

常见方法：

- Paths.get()：创建路径实例
- normalize()：可以使路径标准化

### 6.2 Files

Java NIO Files 类(java.nio.file.Files)提供了几种操作文件系统中的文件的方法，通常 java.nio.file.Files 类与 java.nio.file.Path 实例一起工作。

常见方法：

- Files.createDirectory(path)：根据 Path 实例新建目录
- Files.copy(sourcePath, targetPath)：拷贝一个文件到另外一个文件
- Files.move(sourcePath, targetPath)：将文件从一个路径移动到另一个路径
- Files.delete(path)：删除一个文件或者目录
- Files.walkFileTree()：递归遍历目录树功能

### 6.3 AsynchronousFileChannel

在 Java 7 中，Java NIO 中添加了 AsynchronousFileChannel，也就是**异步**地将数据写入文件。

常见操作：

- 创建：通过静态方法 open() 创建
- 读写：通过 Future 或 CompletionHandler 读写数据

### 6.4 Charset

java 中使用 Charset 来表示字符集编码对象。

常见方法：

- static Charset **forName**(String charsetName)：通过编码类型获得 Charset 对 象
- static SortedMap<String,Charset> **availableCharsets**()：获得系统支持的所有 编码方式
- static Charset **defaultCharset**()：获得虚拟机默认的编码方式
- static boolean **isSupported**(String charsetName)：判断是否支持该编码类型
- String **name()**：获得 Charset 对象的编码类型(String)
- CharsetEncoder **newEncoder()**：获得编码器对象
- CharsetDecoder **newDecoder()**：获得解码器对象