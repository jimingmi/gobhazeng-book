# 概念

RPC（Remote Procedure Call）即远程过程调用，它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。

RPC协议假定某些传输协议的存在，如TCP或UDP，为通信程序之间携带信息数据。在OSI网络通信模型中，RPC跨越了传输层和应用层。RPC使得开发包括网络分布式多程序在内的应用程序更加容易。

RPC采用客户机/服务器模式。请求程序就是一个客户机，而服务提供程序就是一个服务器。首先，客户机调用进程发送一个有进程参数的调用信息到服务进程，然后等待应答信息。在服务器端，进程保持睡眠状态直到调用信息到达为止。当一个调用信息到达，服务器获得进程参数，计算结果，发送答复信息，然后等待下一个调用信息，最后，客户端调用进程接收答复信息，获得进程结果，然后调用执行继续进行。

# 一个简单的RPC Demo

## 接口类

```java
public interface SimpleService {

	public String demo01(String str);

}
```

## 实现类

```java
public class SimpleServiceImpl implements SimpleService {

	public String demo01(String str) {
		if ("hello".equals(str)) {
			return "hello world";
		} else {
			return "error";
		}
	}

}
```

## 服务提供者

```java
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.lang.reflect.Constructor;
import java.lang.reflect.Method;
import java.net.ServerSocket;
import java.net.Socket;

public class Provider {

	public static void main(String[] args) {
		ServerSocket server = null;
		ObjectInputStream in = null;
		ObjectOutputStream out = null;
		Socket socket = null;

		try {
			server = new ServerSocket(8080);
			System.out.println("================开始监听================");
			socket = server.accept();
			in = new ObjectInputStream(socket.getInputStream());
			// 接口名
			String interfaceName = in.readUTF();
			// 方法名
			String methodName = in.readUTF();
			// 接口类型
			Class<?>[] parameterType = (Class<?>[]) in.readObject();
			// 参数列表
			Object[] arguments = (Object[]) in.readObject();

			System.out.println("接口名：" + interfaceName);
			System.out.println("方法名：" + methodName);

			// 根据接口名称获取class
			Class<?> serviceInterfaceClass = Class.forName(interfaceName);
			// 根据参数类型和方法名反射到方法
			Method method = serviceInterfaceClass.getMethod(methodName, parameterType);
			//根据实现类名称获取class(后期需要改成代理模式)
			Class<?> serviceInterface = Class.forName("com.gobha.rpc.core.SimpleServiceImpl");
			//获得实现类的构造器
			Constructor<?> cons = serviceInterface.getDeclaredConstructor();

			Object result = method.invoke(cons.newInstance(), arguments);

			out = new ObjectOutputStream(socket.getOutputStream());
			out.writeObject(result);

		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			try {
				out.close();
			} catch (IOException e1) {
				e1.printStackTrace();
			}
			if (server != null) {
				try {
					server.close();
				} catch (IOException e2) {
					e2.printStackTrace();
				}
				server = null;
			}
			if (socket != null) {
				try {
					socket.close();
				} catch (IOException e3) {
					e3.printStackTrace();
				}
				socket = null;
			}

		}

	}

}
```

## 服务消费者

```java
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.lang.reflect.Method;
import java.net.Socket;

import com.gobha.rpc.core.SimpleService;

public class Consumer {

	public static void main(String[] args) {
		Socket socket = null;
		ObjectOutputStream out = null;
		ObjectInputStream in = null;
		try {
			String interfaceName = SimpleService.class.getName();
			Method method = SimpleService.class.getMethod("demo01", java.lang.String.class);
			Object[] arguments = { "hello" };
			socket = new Socket("127.0.0.1", 8080);
			out = new ObjectOutputStream(socket.getOutputStream());

			out.writeUTF(interfaceName);
			out.writeUTF(method.getName());
			out.writeObject(method.getParameterTypes());
			out.writeObject(arguments);

			in = new ObjectInputStream(socket.getInputStream());
			Object returnObject = in.readObject();
			System.out.println(returnObject.toString());

		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			try {
				out.close();
				in.close();
			} catch (IOException e1) {
				e1.printStackTrace();
			}
			if (socket != null) {
				try {
					socket.close();
				} catch (IOException e2) {
					e2.printStackTrace();
				}
				socket = null;
			}
		}

	}

}
```

# 使用Netty作为网络通讯层的改进RPC

## 接口类

```java
public interface SimpleService {

	public String demo01(String str);

}
```

## 实现类

```java
public class SimpleServiceImpl implements SimpleService {

	public String demo01(String str) {
		if ("hello".equals(str)) {
			return "hello world";
		} else {
			return "error";
		}
	}

}
```

## 数据传输Bean

需要实现序列化接口，这里先用Java自带的`Serializable`接口，由于性能差后期需要改进。

**RpcReqBean**

```java
import java.io.Serializable;
import java.util.Arrays;

public class RpcReqBean implements Serializable {

	private static final long serialVersionUID = 1L;

	private String interfaceName;

	private String methodName;

	private Class<?>[] methodParameterTypes;

	private Object[] arguments;

	public String getInterfaceName() {
		return interfaceName;
	}

	public void setInterfaceName(String interfaceName) {
		this.interfaceName = interfaceName;
	}

	public String getMethodName() {
		return methodName;
	}

	public void setMethodName(String methodName) {
		this.methodName = methodName;
	}

	public Class<?>[] getMethodParameterTypes() {
		return methodParameterTypes;
	}

	public void setMethodParameterTypes(Class<?>[] methodParameterTypes) {
		this.methodParameterTypes = methodParameterTypes;
	}

	public Object[] getArguments() {
		return arguments;
	}

	public void setArguments(Object[] arguments) {
		this.arguments = arguments;
	}

	@Override
	public String toString() {
		return "RpcBean [interfaceName=" + interfaceName + ", methodName=" + methodName + ", methodParameterTypes="
				+ Arrays.toString(methodParameterTypes) + ", arguments=" + Arrays.toString(arguments) + "]";
	}

}
```

**RpcRespBean**

```java
import java.io.Serializable;

public class RpcRespBean implements Serializable {

	private static final long serialVersionUID = 1L;

	private Object result;

	public Object getResult() {
		return result;
	}

	public void setResult(Object result) {
		this.result = result;
	}

	@Override
	public String toString() {
		return "RpcRespBean [result=" + result + "]";
	}

}
```

## 服务提供者

### 处理类

```java
import java.lang.reflect.Constructor;
import java.lang.reflect.Method;

import org.apache.log4j.Logger;

import com.gobha.core.RpcReqBean;
import com.gobha.core.RpcRespBean;

import io.netty.channel.Channel;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;

/**
 * Provider处理类
 */
public class ProviderHandler extends SimpleChannelInboundHandler<RpcReqBean> {

	private static Logger logger = Logger.getLogger(ProviderHandler.class);

	private RpcRespBean respBean;

	/**
	 * 每当Provider收到新的Consumer时调用
	 */
	@Override
	public void handlerAdded(ChannelHandlerContext ctx) throws Exception {

		Channel incoming = ctx.channel();

		logger.info("[Consumer] - " + incoming.remoteAddress() + " Invoking!\n");
	}

	/**
	 * 每当Provider收到Consumer断开时调用
	 */
	@Override
	public void handlerRemoved(ChannelHandlerContext ctx) throws Exception {

		Channel incoming = ctx.channel();

		logger.info("[Consumer]-" + incoming.remoteAddress() + " Break!\n");

	}

	/**
	 * 每当Provider读到Consumer写入信息时，执行RPC
	 */
	@Override
	protected void channelRead0(ChannelHandlerContext ctx, RpcReqBean bean) throws Exception {

		Channel incoming = ctx.channel();

		// 根据接口名称获取class
		Class<?> serviceInterfaceClass = Class.forName(bean.getInterfaceName());
		// 根据参数类型和方法名反射到方法
		Method method = serviceInterfaceClass.getMethod(bean.getMethodName(), bean.getMethodParameterTypes());
		// 根据实现类名称获取class(后期需要改成代理模式)
		Class<?> serviceInterface = Class.forName("com.gobha.core.SimpleServiceImpl");
		// 获得实现类的构造器
		Constructor<?> cons = serviceInterface.getDeclaredConstructor();

		Object result = method.invoke(cons.newInstance(), bean.getArguments());

		respBean = new RpcRespBean();

		respBean.setResult(result);

		incoming.writeAndFlush(respBean);

	}

	/**
	 * 服务端监听到客户端活动
	 */
	@Override
	public void channelActive(ChannelHandlerContext ctx) throws Exception {
		Channel incoming = ctx.channel();
		logger.info("SimpleChatClient:" + incoming.remoteAddress() + " Connect!\n");
	}

	/**
	 * 服务端监听到客户端不活动
	 */
	@Override
	public void channelInactive(ChannelHandlerContext ctx) throws Exception {
		Channel incoming = ctx.channel();
		logger.info("SimpleChatClient:" + incoming.remoteAddress() + " Disconnect!\n");
	}

	/**
	 * 服务端异常处理
	 */
	@Override
	public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
		Channel incoming = ctx.channel();
		logger.info("SimpleChatClient:" + incoming.remoteAddress() + " Exception!\n");
		// 打印异常信息
		cause.printStackTrace();
		// 当出现异常就关闭
		ctx.close();
	}

}
```

### 初始化类

```java
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelPipeline;
import io.netty.channel.socket.SocketChannel;
import io.netty.handler.codec.serialization.ClassResolvers;
import io.netty.handler.codec.serialization.ObjectDecoder;
import io.netty.handler.codec.serialization.ObjectEncoder;

/**
 * 服务端初始化类
 */
public class ProviderInitializer extends ChannelInitializer<SocketChannel> {

	private final static int BSIZE = 1024 * 1024;


	@Override
	protected void initChannel(SocketChannel ch) throws Exception {
		ChannelPipeline pipeline = ch.pipeline();
		// 设置线程安全的WeakReferenceMap对类加载器进行缓存 支持多线程并发访问防止内存溢出
				pipeline.addLast(new ObjectDecoder(BSIZE,
						ClassResolvers.weakCachingConcurrentResolver(this.getClass().getClassLoader())));
				// 设置对象编码器 在服务器对外发送消息的时候自动将实现序列化的POJI对象编码
				pipeline.addLast(new ObjectEncoder());
		pipeline.addLast("handler",new ProviderHandler());

	}

}
```

### 启动提供者

```java
import org.apache.log4j.Logger;
import org.apache.log4j.PropertyConfigurator;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelOption;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioServerSocketChannel;

public class ProviderServer {

	private static Logger logger = Logger.getLogger(ProviderServer.class);

	private int port;

	public ProviderServer(int port) {
		super();
		this.port = port;
	}

	public void run() {
		/**
		 * NioEventLoopGroup 是用来处理I/O操作的多线程事件循环器 这里使用NioEventLoopGroup
		 * 第一个是‘boss’,用来接受进来的连接
		 * 第二个是‘worker’么,用来处理已接受的连接，一旦‘boss’接受连接,就会把连接信息注册到‘worker’上
		 */
		EventLoopGroup bossGroup = new NioEventLoopGroup();
		EventLoopGroup workerGroup = new NioEventLoopGroup();
		logger.info("准备运行端口：" + port);

		try {
			// ServerBootstrap 是一个启动NIO服务的辅助启动类,可以在这个服务中直接使用channel
			ServerBootstrap bootstrap = new ServerBootstrap();
			// 设置group
			bootstrap.group(bossGroup, workerGroup).channel(NioServerSocketChannel.class)
					.childHandler(new ProviderInitializer()).option(ChannelOption.SO_BACKLOG, 128)
					.childOption(ChannelOption.SO_KEEPALIVE, true);

			logger.info("ProviderServer 启动");

			// 绑定端口并启动去接收进来的连接
			ChannelFuture future = bootstrap.bind(port).sync();
			// 一直等待，直到socket被关闭
			future.channel().closeFuture().sync();
		} catch (Exception e) {
			e.printStackTrace();
			throw new RuntimeException("Provider运行错误！", e);
		} finally {
			// 关闭
			bossGroup.shutdownGracefully();
			workerGroup.shutdownGracefully();

			logger.info("ProviderServer 关闭");
		}
	}

	public static void main(String[] args) {
		PropertyConfigurator.configure("resource/log4j.properties");

		int port;
		if (args.length > 0) {
			port = Integer.parseInt(args[0]);
		} else {
			port = 8080;
		}

		new ProviderServer(port).run();
		logger.info("Server:run");
	}

}
```

## 服务消费者

### 处理类

```java
import com.gobha.core.RpcRespBean;

import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;

/**
 * Consumer处理类
 */
public class ConsumerHandler extends SimpleChannelInboundHandler<RpcRespBean> {

	/**
	 * 每当Consumer读到Provider写入信息时，直接将信息打印出来
	 */
	@Override
	protected void channelRead0(ChannelHandlerContext ctx, RpcRespBean bean) throws Exception {

		System.out.println(bean.getResult().toString());

	}

}
```

### 初始化类

```java
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelPipeline;
import io.netty.channel.socket.SocketChannel;
import io.netty.handler.codec.serialization.ClassResolvers;
import io.netty.handler.codec.serialization.ObjectDecoder;
import io.netty.handler.codec.serialization.ObjectEncoder;

/**
 * 客户端初始化类
 */
public class ConsumerInitializer extends ChannelInitializer<SocketChannel> {

	private final static int BSIZE = 1024;

	@Override
	protected void initChannel(SocketChannel ch) throws Exception {
		ChannelPipeline pipeline = ch.pipeline();
		/**
		 * 添加POJI对象解码器 禁止缓存类加载器
		 */
		pipeline.addLast(new ObjectDecoder(BSIZE, ClassResolvers.cacheDisabled(this.getClass().getClassLoader())));

		pipeline.addLast(new ObjectEncoder());
		pipeline.addLast("handler", new ConsumerHandler());

	}

}
```

### 启动消费者

```java
import java.lang.reflect.Method;

import org.apache.log4j.Logger;
import org.apache.log4j.PropertyConfigurator;

import com.gobha.core.RpcReqBean;
import com.gobha.core.SimpleService;

import io.netty.bootstrap.Bootstrap;
import io.netty.channel.Channel;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioSocketChannel;

public class ConsumerServer {

	private static Logger logger = Logger.getLogger(ConsumerServer.class);

	private final String host;

	private final int port;

	private RpcReqBean bean;

	public ConsumerServer(String host, int port) {
		super();
		this.host = host;
		this.port = port;
	}

	public void run() {
		EventLoopGroup group = new NioEventLoopGroup();
		try {
			Bootstrap bootstrap = new Bootstrap().group(group).channel(NioSocketChannel.class)
					.handler(new ConsumerInitializer());
			logger.info("ConsumerServer 启动");

			Channel channel = bootstrap.connect(host, port).sync().channel();

			String interfaceName = SimpleService.class.getName();
			Method method = SimpleService.class.getMethod("demo01", java.lang.String.class);
			Object[] arguments = { "hello" };

			bean = new RpcReqBean();
			bean.setInterfaceName(interfaceName);
			bean.setMethodName(method.getName());
			bean.setMethodParameterTypes(method.getParameterTypes());
			bean.setArguments(arguments);
			channel.writeAndFlush(bean);

			// 一直等待，直到socket被关闭
			channel.closeFuture().sync();

		} catch (Exception e) {
			e.printStackTrace();
			throw new RuntimeException("Consumer运行错误！", e);
		} finally {
			group.shutdownGracefully();
			logger.info("ConsumerServer 关闭");

		}
	}

	public static void main(String[] args) {
		PropertyConfigurator.configure("resource/log4j.properties");

		new ConsumerServer("localhost", 8080).run();
	}

}
```

## 运行

先启动提供者，再启动消费者，在消费者端查看服务调用的结果。