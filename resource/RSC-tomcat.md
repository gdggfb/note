# 初衷
tomcat是java web程序员的老朋友了，一直以来自己只懂得其大概的原理和流程，今天将其git clone下来捋一捋其数据流程。（clone date：2018年3月2日16:12）

# 关键入口
+ nio细节入口 org.apache.catalina.connector.Connector.startInternal()
+ 报文解析细节入口 org.apache.coyote.http11.Http11Processor.service(SocketWrapperBase<?> socketWrapper)
+ tomcat调用filter和servlet入口 org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ServletRequest request, ServletResponse response)

# 阶段1
1. 脚本启动：startup.sh -> catalina.sh -> Bootstrap -> Catalina -> StandardServer -> StandardService -> Connector
2. spring boot启动：Tomcat -> StandardServer -> StandardService -> Connector
过程很简单，且没有太多我们关注的东西
# 阶段2
+ Connector.startInternal() 最终调用了NioEndpoint.startInternal()
+ NioEndpoint.startInternal()主要做了四件事：
+ 1、调用createExecutor()创建线程池管理器
+ 2、调用initializeConnectionLatch()创建最大连接数监视器
+ 3、创建socket消息轮询器Poller，并启动线程
+ 4、调用startAcceptorThreads()创建连接处理器Acceptor，并启动线程。
+ Acceptor会将接收到的socket连接加入selector，Poller负责从selector读取消息并处理，由此可以看出tomcat接收新连接和接收报文是分离处理的，两者互不影响。
+ Poller主要做了两件事
+ 1、调用nio的selectedKeys()获得内核里Ready队列里描述符集合里的内容。
+ 2、调用processKey()处理nio的SelectionKey，最终会走到Http11Processor的service()方法。
# 阶段3