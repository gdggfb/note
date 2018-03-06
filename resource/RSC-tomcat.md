# 初衷
tomcat是java web程序员的老朋友了，一直以来自己只懂得其大概的原理和流程，今天将其git clone下来捋一捋其数据流程。（clone date：2018年3月2日16:12）

# 关键入口
+ nio细节入口 void org.apache.catalina.connector.Connector.startInternal()
+ 报文解析细节入口 SocketState org.apache.coyote.http11.Http11Processor.service(SocketWrapperBase<?> socketWrapper)
+ tomcat调用filter和servlet入口 void org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ServletRequest request, ServletResponse response)