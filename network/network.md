# BIO
- 缺点： 两个地方有阻塞(accept, read)，并发需要多线程，多线程会造成服务器资源浪费
```
 try {
	ServerSocket serverSocket = new ServerSocket(9098);

	while (true) {
		System.out.println("wait conn");
		//阻塞
		Socket clientSocket = serverSocket.accept();

		System.out.println("conn success");

		System.out.println("wait data");
		//有阻塞
		clientSocket.getInputStream().read(bs);
		System.out.println("data success");
		System.out.println(new String(bs));
	}
} catch (IOException e) {
	e.printStackTrace();
}
```
# NIO 
### 设计思路
- 单线程处理并发