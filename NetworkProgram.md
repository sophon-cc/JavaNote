[toc]

# 网络编程
## 网络基础
网络通信：将数据通过网络从一台设备传输到另一台设备。java.net包下提供了一系列的类或接口，供程序员使用，完成网络通信。
## InetAddress
```java
// 1. 获取本机的 InetAddress 对象
InetAddress localHost = InetAddress.getLocalHost();
System.out.println(localHost);

// 2. 根据指定主机名/域名获取 InetAddress 对象
InetAddress host1 = InetAddress.getByName("LAPTOP-2LGOLCDI");
System.out.println(host1);

// 3. 根据主机名/域名返回 InetAddress 对象
InetAddress host2 = InetAddress.getByName("wwww.baidu.com");
System.out.println(host2);

// 4. 通过 InetAddress 对象获取 ip 地址
String hostAddress = host2.getHostAddress();
System.out.println(hostAddress);

// 5. 通过 InetAddress 对象获取主机名/域名
String hostName = host2.getHostName();
System.out.println(hostName);
```

## Socket
### TCP
#### 服务器端
```java
// 1. 服务器在本机 9999 号端口监听，要求该端口未被占用，生成 ServerSocket
ServerSocket serverSocket = new ServerSocket(9999);
// 2. 当没有客户端连接 9999 号端口时，程序会阻塞等待连接
//    如果客户端连接，则会返回 Socket 对象，程序继续
Socket accept = serverSocket.accept();
// 3. 通过 Socket 获取 IO 流
// 从客户端读
InputStream inputStream = accept.getInputStream();
int readLen = 0;
byte[] bytes = new byte[1024];
while ((readLen = inputStream.read(bytes)) != -1) {
    System.out.println(new String(bytes, 0, readLen));
}
// 写给客户端
OutputStream outputStream = accept.getOutputStream();
outputStream.write("hello client".getBytes());
// 终止符
accept.shutdownOutput();

// 4.关闭资源
inputStream.close();
accept.close();
serverSocket.close();

System.out.println("Server closed.");
```

#### 客户端
```java
// 1. 连接服务器（ip， 端口）
Socket socket = new Socket(InetAddress.getLocalHost(), 9999);
// 2. 连接后，生成 Socket 输出流
OutputStream outputStream = socket.getOutputStream();
// 3. 通过输出流写数据到 数据通道
// 写给服务器
outputStream.write("Hello Server!".getBytes());
outputStream.flush();
// 终止符
socket.shutdownOutput();
// 从服务器读
InputStream inputStream = socket.getInputStream();
int readLen = 0;
byte[] bytes = new byte[1024];
while ((readLen = inputStream.read(bytes)) != -1) {
    System.out.println(new String(bytes, 0, readLen));
}

// 4. 关闭资源
outputStream.close();
socket.close();

System.out.println("Client closed.");
```

### UDP
#### 接收端
```java
// 1.创建 DatagramSocket 对象，在 9999 端口监听，接收数据
DatagramSocket datagramSocket = new DatagramSocket(9999);
// 2.接收数据的 DatagramPacket 并预分配空间 注意 UDP 最大空间为 64 K
DatagramPacket datagramPacket = new DatagramPacket(new byte[1024], 1024);
// 3.调用接收方法。当有数据发送到本机的 9998 端口时，接收并填充数据包，否则阻塞
datagramSocket.receive(datagramPacket);
int length = datagramPacket.getLength();
byte[] data = datagramPacket.getData();
String s = new String(data, 0, length);
System.out.println(s);
// 3.将需要发送的数据，封装到 DatagramPacket 对象
datagramPacket = new DatagramPacket("hello UDPA".getBytes(), 10, InetAddress.getLocalHost(), 9998);
datagramSocket.send(datagramPacket);
// 4. 关闭资源
datagramSocket.close();
```
#### 发送端
```java
// 1.创建 DatagramSocket 对象，在 9998 端口监听，接收数据
DatagramSocket datagramSocket = new DatagramSocket(9998);
// 2.将需要发送的数据，封装到 DatagramPacket 对象
DatagramPacket datagramPacket =
        new DatagramPacket(("Hello UDPB").getBytes(), 10, InetAddress.getLocalHost(), 9999);
// 3. DatagramSocket 发送数据
datagramSocket.send(datagramPacket);

// 更新为接收数据的 DatagramPacket 并预分配空间 注意 UDP 最大空间为 64 K
datagramPacket = new DatagramPacket(new byte[1024], 1024);
// 4.调用接收方法。当有数据发送到本机的 9998 端口时，接收并填充数据包，否则阻塞
datagramSocket.receive(datagramPacket);
byte[] data = datagramPacket.getData();
System.out.println(new String(data, 0, datagramPacket.getLength()));
// 5.关闭资源
datagramSocket.close();
```