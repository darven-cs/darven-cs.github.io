---
title: Socket编程（Java）
date: 2025-10-02 14:55:56
index_img: /img/java-socket.png
tags: [Java,计算机网络，Socket编程]
categories: 
  - [Java]
  - [计算机网络]
---
<meta name="referrer" content="no-referrer"/>


![](https://cdn.nlark.com/yuque/0/2025/png/44165525/1758948623828-702334a8-71ca-45d2-8f0d-dd9e7575ac7b.png)



Java中的Socket编程主要分为**TCP协议**和**UDP协议**两种实现方式，两者者基于不同的通信模型，适用于不同的场景。以下从核心类、工作流程、特点及适用场景等方面进行总结：

### 一、TCP Socket编程（面向连接）
TCP（Transmission Control Protocol）是一种**面向连接、可靠的、基于字节流**的传输协议，通信前需建立连接（三次握手），通信后需断开连接（四次挥手），保证数据有序、不丢失。

#### 1. 核心类
+ `ServerSocket`：服务器端套接字，用于监听指定端口的连接请求，核心方法：
    - `ServerSocket(int port)`：绑定到指定端口
    - `accept()`：阻塞等待客户端连接，返回一个`Socket`对象（与客户端通信的套接字）
    - `close()`：关闭服务器套接字
+ `Socket`：客户端套接字（也用于服务器端与客户端通信的连接），核心方法：
    - `Socket(String host, int port)`：连接到指定主机和端口的服务器
    - `getInputStream()`：获取输入流（读取对方数据）
    - `getOutputStream()`：获取输出流（向对方发送数据）
    - `getInetAddress()`：获取对方的IP地址
    - `close()`：关闭套接字

#### 2. 工作流程
+ **服务器端**：
    1. 创建`ServerSocket`并绑定端口（如`new ServerSocket(8080)`）
    2. 调用`accept()`阻塞等待客户端连接，获取与客户端通信的`Socket`
    3. 通过`Socket`的输入流读取客户端数据，输出流发送响应
    4. 通信结束后关闭`Socket`和`ServerSocket`
+ **客户端**：
    1. 创建`Socket`并指定服务器IP和端口（如`new Socket("localhost", 8080)`）
    2. 通过`Socket`的输出流发送数据，输入流读取服务器响应
    3. 通信结束后关闭`Socket`

#### 3. 特点
+ 面向连接：通信前必须建立连接，连接断开后需重连
+ 可靠性：通过确认、重传机制保证数据不丢失、不重复、有序到达
+ 字节流传输：数据以流的形式连续传输，无大小限制（理论上）
+ 效率较低：连接建立和维护有额外开销

#### 4. 示例代码要点
```java
// 服务器端核心代码
try (ServerSocket serverSocket = new ServerSocket(8080)) {
    Socket clientSocket = serverSocket.accept(); // 等待连接
    // 获取输入流（读客户端数据）
    try (BufferedReader in = new BufferedReader(
             new InputStreamReader(clientSocket.getInputStream()));
         // 获取输出流（向客户端写数据）
         PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true)) {
        
        String message = in.readLine(); // 读数据
        out.println("收到：" + message); // 发响应
    }
}

// 客户端核心代码
try (Socket socket = new Socket("localhost", 8080)) {
    PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
    BufferedReader in = new BufferedReader(
        new InputStreamReader(socket.getInputStream()));
    
    out.println("Hello TCP Server"); // 发数据
    String response = in.readLine(); // 读响应
}
```



### 二、UDP Socket编程（无连接）
UDP（User Datagram Protocol）是一种**无连接、不可靠、基于数据报**的传输协议，通信前无需建立连接，直接发送数据报，不保证数据到达顺序和完整性，但传输效率高。

#### 1. 核心类
+ `DatagramSocket`：用于发送和接收数据报的套接字，核心方法：
    - `DatagramSocket(int port)`：绑定到指定端口（服务器端）
    - `DatagramSocket()`：不绑定端口（客户端，系统自动分配）
    - `receive(DatagramPacket p)`：阻塞接收数据报，存入`p`
    - `send(DatagramPacket p)`：发送数据报`p`
    - `close()`：关闭套接字
+ `DatagramPacket`：数据报包，封装了数据、目标地址（发送时）或源地址（接收时），核心方法：
    - `DatagramPacket(byte[] buf, int length, InetAddress address, int port)`：创建发送的数据报（含数据、目标IP和端口）
    - `DatagramPacket(byte[] buf, int length)`：创建接收的数据报（含缓冲区）
    - `getLength()`：获取实际接收的数据长度
    - `getData()`：获取数据缓冲区
    - `getAddress()`：获取发送方IP地址（接收时）
    - `getPort()`：获取发送方端口（接收时）



#### 2. 工作流程
+ **服务器端**：
    1. 创建`DatagramSocket`并绑定端口（如`new DatagramSocket(8080)`）
    2. 创建`DatagramPacket`（含缓冲区），调用`receive()`阻塞等待数据报
    3. 从`DatagramPacket`中解析发送方信息（IP、端口）和数据
    4. （可选）创建响应数据报，通过`send()`发送给客户端
    5. 通信结束后关闭`DatagramSocket`
+ **客户端**：
    1. 创建`DatagramSocket`（不绑定端口）
    2. 构建数据报（含数据、服务器IP和端口），调用`send()`发送
    3. （可选）创建接收数据报，调用`receive()`等待服务器响应
    4. 通信结束后关闭`DatagramSocket`

#### 3. 特点
+ 无连接：无需建立连接，直接发送数据，减少开销
+ 不可靠：不保证数据到达，可能丢失、重复或乱序
+ 数据报传输：每个数据报有大小限制（通常≤65535字节）
+ 效率高：适合实时性要求高的场景

#### 4. 示例代码要点
```java
// 服务器端核心代码
try (DatagramSocket socket = new DatagramSocket(8080)) {
    byte[] buff = new byte[1024];
    DatagramPacket packet = new DatagramPacket(buff, buff.length);
    socket.receive(packet); // 接收数据报
    
    // 解析数据和客户端信息
    String message = new String(packet.getData(), 0, packet.getLength());
    InetAddress clientAddr = packet.getAddress();
    int clientPort = packet.getPort();
    
    // 发送响应
    byte[] response = "收到".getBytes();
    DatagramPacket responsePacket = new DatagramPacket(
        response, response.length, clientAddr, clientPort);
    socket.send(responsePacket);
}

// 客户端核心代码
try (DatagramSocket socket = new DatagramSocket()) {
    String message = "Hello UDP Server";
    byte[] data = message.getBytes();
    InetAddress serverAddr = InetAddress.getByName("localhost");
    // 创建数据报（目标服务器IP和端口）
    DatagramPacket packet = new DatagramPacket(data, data.length, serverAddr, 8080);
    socket.send(packet); // 发送
    
    // 接收响应
    byte[] buff = new byte[1024];
    DatagramPacket responsePacket = new DatagramPacket(buff, buff.length);
    socket.receive(responsePacket);
}
```



### 三、TCP与UDP的对比及适用场景
| 特性 | TCP | UDP |
| --- | --- | --- |
| 连接性 | 面向连接（需三次握手） | 无连接（直接发送） |
| 可靠性 | 可靠（保证数据完整、有序） | 不可靠（可能丢失、乱序） |
| 传输方式 | 字节流 | 数据报（有限大小） |
| 效率 | 较低（连接和确认开销） | 较高（无额外开销） |
| 适用场景 | 文件传输、HTTP/HTTPS、邮件等 | 实时通信（视频/语音）、游戏、广播等 |


### 四、共同注意事项
1. **资源关闭**：使用`try-with-resources`自动关闭`Socket`、`ServerSocket`、`DatagramSocket`等资源，避免泄露。
2. **异常处理**：捕获`IOException`（如端口占用、网络中断），增强程序健壮性。
3. **多线程**：TCP服务器需通过多线程处理多个客户端连接；UDP服务器可通过多线程并行处理数据报。
4. **缓冲区大小**：UDP需注意数据报大小限制，TCP需合理设置流缓冲区避免性能问题。

通过上述总结，可以清晰区分Java中TCP和UDP Socket编程的实现方式，根据实际需求选择合适的协议进行开发。

