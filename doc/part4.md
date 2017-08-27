### Ⅳ. Jetty开发指南
<span id="top"></span>
  - 21. [嵌入式开发](#21嵌入式开发)
    - [Jetty的嵌入式Hello World](#211jetty的嵌入式hello-world)
      - [下载Jar包](#2111下载jar包)
      - [Hello World栗子](#2112hello-world栗子)
      - [编译Hello World栗子](#2113编译hello-world栗子)
      - [运行Handler和Server](#2114运行handler和server)
      - [下一步](#2115下一步)
    - [Jetty的嵌入式开发](#212jetty的嵌入式开发)
      - [概述](#2121概述)
      - [创建Server](#2122创建server)
      - [使用Handler](#2123使用handler)
        - [一个Handler的Hello World](#21231一个handler的hello-world)
        - [运行HelloWorldHandler](#21232运行helloworldhandler)
        - [Handler Collections和Wrappers](#21233handler-collections和wrappers)
        - [Scoped Handlers](#21234scoped-handlers)
        - [Resource Handler](#21235resource-handler)
      - [嵌入Connectors](#2124嵌入connectors)
        - [一个Connectors](#21241一个connectors)
        - [多个Connectors](#21242多个connectors)
      - [嵌入Servlets](#2125嵌入servlets)
      - [嵌入Contexts](#2126嵌入contexts)
      - [嵌入ServletContexts](#2127嵌入servletcontexts)
      - [嵌入Web应用程序](#2128嵌入web应用程序)
      - [像JettyXML一样进行嵌入式开发](#2129像jettyxml一样进行嵌入式开发)
    - [嵌入开发的栗子](#)
  - 22. HTTP客户端（略）
  - 23. [Jetty和Maven](#)
    - [使用Maven](#)
    - [配置Jetty的Maven插件](#)
    - [Jetty Maven插件的文件扫描](#)
    - [Jetty Jspc Maven插件](#)
  - 后略
- - -
<span id="21嵌入式开发"></span>
### 21、嵌入式开发
  - [Jetty的嵌入式Hello World](#211jetty的嵌入式hello-world)
    - [下载Jar包](#2111下载jar包)
    - [Hello World栗子](#2112hello-world栗子)
    - [编译Hello World栗子](#2113编译hello-world栗子)
    - [运行Handler和Server](#2114运行handler和server)
    - [下一步](#2115下一步)
  - [Jetty的嵌入式开发](#)
  - [嵌入开发的栗子](#)

<span id="211jetty的嵌入式hello-world"></span>
#### 21.1、Jetty的嵌入式HelloWorld

<span id="2111下载jar包"></span>
##### 21.1.1、下载Jar包

Jetty把整体分解为许多的jar包，我们可以按需导入依赖。典型的做法是使用Maven去管理jar包，然而本节教程是使用的整体jar包，它包含了所有的Jetty服务。你可以通过curl或者浏览器去手动下载这个完整的jar包`jetty-all.jar`：http://central.maven.org/maven2/org/eclipse/jetty/aggregate/jetty-all/9.4.6.v20170531/jetty-all-9.4.6.v20170531-uber.jar。

> **NOTE：**
>
> 这个地址拒绝使用wget去下载，你可以使用curl的方式下载。

> **注意：**
>
> jetty-all.jar包只是在本节作举例假设的引用，千万不要在教程以外的地方使用。你可以在后面的Maven章节了解到如何使用Maven去管理你的项目依赖。

按照以下的方式使用curl下载：
```
> mkdir Demo
> cd Demo
> curl -o jetty-all-uber.jar http://central.maven.org/maven2/org/eclipse/jetty/aggregate/jetty-all/9.4.6.v20170531/jetty-all-9.4.6.v20170531-uber.jar
```

<br>

<span id="2112hello-world栗子"></span>
##### 21.1.2、Hello World栗子
这个栗子中包含了许多关于JettyAPI的用例，这个教程会在主函数里面用到一个简单的Handler的Hello World来运行服务器：
```
package org.eclipse.jetty.embedded;

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.eclipse.jetty.server.Request;
import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.server.handler.AbstractHandler;

public class HelloWorld extends AbstractHandler
{
    @Override
    public void handle( String target,
                        Request baseRequest,
                        HttpServletRequest request,
                        HttpServletResponse response ) throws IOException,
                                                      ServletException
    {
        // Declare response encoding and types
        response.setContentType("text/html; charset=utf-8");

        // Declare response status code
        response.setStatus(HttpServletResponse.SC_OK);

        // Write back response
        response.getWriter().println("<h1>Hello World</h1>");

        // Inform jetty that this request has now been handled
        baseRequest.setHandled(true);
    }

    public static void main( String[] args ) throws Exception
    {
        Server server = new Server(8080);
        server.setHandler(new HelloWorld());

        server.start();
        server.join();
    }
}
```
<br>

<span id="2113编译hello-world栗子"></span>
##### 21.1.3、编译Hello World栗子
```
> mkdir classes
> javac -d classes -cp jetty-all-uber.jar HelloWorld.java
```
<br>

<span id="2114运行handler和server"></span>
##### 21.1.4、运行Handler和Server
```
> java -cp classes:jetty-all-uber.jar org.eclipse.jetty.embedded.HelloWorld
```

<br>

<span id="2115下一步"></span>
##### 21.1.5、下一步

如果你想了解更多的内容，请按照下面的步骤来：
* 跟着[21.2、Jetty的嵌入式开发](#212jetty的嵌入式开发)继续学习。
* 探索整个[Jetty javadoc](#http://www.eclipse.org/jetty/javadoc/9.4.6.v20170531/)。
* 考虑使用[Jetty和Maven](#)去管理你的项目依赖。

[回到顶部](#top)
- - -

<span id="212jetty的嵌入式开发"></span>
#### 21.2、Jetty的嵌入式开发
  - 21.2.1、[概述](#2121概述)
  - 21.2.2、[创建Server](#2122创建server)
  - 21.2.3、[使用Handler](#2123使用handler)
  - 21.2.4、[嵌入Connectors](#2124嵌入connectors)
  - 21.2.5、[嵌入Servlets](#2125嵌入servlets)
  - 21.2.6、[嵌入Contexts](#2126嵌入contexts)
  - 21.2.7、[嵌入ServletContexts](#2127嵌入servletcontexts)
  - 21.2.8、[嵌入Web应用程序](#2128嵌入web应用程序)
  - 21.2.9、[像JettyXML一样进行嵌入式开发](#2129像jettyxml一样进行嵌入式开发)

Jetty有一句口号“不要把你的应用部署到Jetty，要把Jetty部署到你的应用中！”，Jetty被设计成是可以被实例化并且拿来使用的一个组件，就像一个Java的POJO对象一样。把Jetty服务嵌入到你的应用中，这意味着嵌入一个HTTP模块进你的应用中去。

这个教程会一步步地教你如何用最简单的方式使用Jetty服务器去运行多个Web应用。栗子中的许多代码来自于Jetty项目的源码。

<span id="2121概述"></span>
##### 21.2.1、概述

想要嵌入Jetty，以下是标准的步骤，在栗子里面会有详细的说明：

1. 创建Server实例
2. 添加/配置Connectors
3. 添加/配置Handlers、Contexts、Servlets
4. 开启Server
5. 泡杯咖啡

<br>

<span id="2122创建server"></span>
##### 21.2.2、创建Server
下面的代码会简单的向你介绍一个JettyServer：
```
package org.eclipse.jetty.embedded;

import org.eclipse.jetty.server.Server;

/**
 * The simplest possible Jetty server.
 */
public class SimplestServer
{
    public static void main( String[] args ) throws Exception
    {
        Server server = new Server(8080);
        server.start();
        server.dumpStdErr();
        server.join();
    }
}
```
这会在8080端口运行一个HTTP服务器，并且没有任何服务，任何请求都会是404。
<br>
<span id="2123使用handler"></span>
##### 21.2.3、使用Handler
  - 21.2.3.1、[一个Handler的Hello World](#21231一个handler的hello-world)
  - 21.2.3.2、[运行HelloWorldHandler](#21232运行helloworldhandler)
  - 21.2.3.3、[Handler Collections和Wrappers](#21233handler-collections和wrappers)
  - 21.2.3.4、[Scoped Handlers](#21234scoped-handlers)
  - 21.2.3.5、[Resource Handler](#21235resource-handler)

为了对请求做出响应，Jetty要求你在server里面设置一个Handler，这个Handler可以：

- 检查/修改HTTP请求
- 生成一个完整的HTTP响应
- 调用另外一个Handler（参见API：[HandlerWrapper](http://www.eclipse.org/jetty/javadoc/9.4.6.v20170531/org/eclipse/jetty/server/handler/HandlerWrapper.html)）
- 选择一个或多个Handler来调用（参见API：[HandlerCollection](http://www.eclipse.org/jetty/javadoc/9.4.6.v20170531/org/eclipse/jetty/server/handler/HandlerCollection.html)）

<span id="21231一个handler的hello-world"></span>
###### 21.2.3.1、一个Handler的Hello World
这是`HelloHandler.java`代码：
```
package org.eclipse.jetty.embedded;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.eclipse.jetty.server.Request;
import org.eclipse.jetty.server.handler.AbstractHandler;

public class HelloHandler extends AbstractHandler
{
    final String greeting;
    final String body;

    public HelloHandler()
    {
        this("Hello World");
    }

    public HelloHandler( String greeting )
    {
        this(greeting, null);
    }

    public HelloHandler( String greeting, String body )
    {
        this.greeting = greeting;
        this.body = body;
    }

    public void handle( String target,
                        Request baseRequest,
                        HttpServletRequest request,
                        HttpServletResponse response ) throws IOException,
                                                      ServletException
    {
        response.setContentType("text/html; charset=utf-8");
        response.setStatus(HttpServletResponse.SC_OK);

        PrintWriter out = response.getWriter();

        out.println("<h1>" + greeting + "</h1>");
        if (body != null)
        {
            out.println(body);
        }

        baseRequest.setHandled(true);
    }
}
```
传到handle方法的参数是：
- target：发起请求的目标，可以是一个URL或者发送者的name值。
- baseRequest：Jetty的可变请求对象，这个对象是未经包装的。
- request：不可变的请求对象，这个对象可能经过filter或者servlet包装过。
- response：响应，这个对象可能经过filter或者servlet包装过。

handler可以在响应的body生成之前进行响应的status设置、content-type设置、把请求标记为已处理。

<span id="21232运行helloworldhandler"></span>
###### 21.2.3.2、运行HelloWorldHandler
为了Handler能够处理HTTP请求，你必须把它添加到Server实例中，下面这个`OneHandler.java`代码就会向你展示Jetty服务器如何才能使用HelloWorldHandler：
```
package org.eclipse.jetty.embedded;

import org.eclipse.jetty.server.HttpConnectionFactory;
import org.eclipse.jetty.server.Server;

public class OneHandler
{
    public static void main( String[] args ) throws Exception
    {
        Server server = new Server(8080);
        server.setHandler(new HelloHandler());

        server.start();
        server.join();
    }
}
```
当有一个或多个handlers处理所有的请求的时候，一些hanlders会选择其他指定的handlers（比如，一个ContextHandlerCollection会根据context path去选择ContextHandler）；

其他handlers则使用业务逻辑去生成对应的响应（比如说，ServletHandler传递请求到一个web应用Servlet）；

还有的其他handlers会处理一些业务无关的请求响应（比如，RequestLogHandler或者是StatisticsHandler）。

后面的部分会告诉你怎么才能把handlers连接起来组成一个切面，你可以在Jetty的`org.eclipse.jetty.server.handler`类中找到更多的有用的handlers实例。

<span id="21233handler-collections和wrappers"></span>
###### 21.2.3.3、Handler Collections和Wrappers

一个完整的请求处理是从多个Handlers中建立的，你可用以多种方式将多个Handler连接起来，Jetty提供了几种实现了`HandlerContainer`的实例：

> *译者文外补充：HandlerContainer是Handler的容器，你可以把一套业务的Handler放在一个容器里面，再把这个容器添加到Server中，Handler容器的本质也是一个Handler。*

- HandlerCollection
  持有一个含有其他Handler的collection，处理请求的时候会调用collection顺序中的每一个handler。你可用使用它把一些作统计用的handlers和一些作日志用的handlers练成一串，统一生成响应。

- HandlerList
  持有一个含有其他Handler的collection，和HandlerCollection不同的是，如果某个handler返回异常或者响应已经被确认或者`request.isHandled()`方法返回true的时候才会停止。你可用使用这种连接方式去连接按照条件处理的handler，比如匹配不同的虚拟主机。

> *译者文外补充：异常就是handler内报错；响应被确认应该是整条链跑到底了；而isHandled()则是某个在handler里面调用`baseRequest.setHandled(true)`方法。*

- HandlerWrapper
  一个Handler的基类，你可用它来把很多handler以良好的链式处理连在一起形成一个AOP切面。比如说一个标准的Web程序是由一个context handlers、一个session handlers、一个security handlers和一个servlet handlers组成。

> *译者文外补充：在其Javadoc中定义所描述：`HandlerWrapper`虽然拥有Handler一样的行为，但是它更加代表着Handler方法和`LifeCycle`接口，它不像`HandlerCollection`那样专注实现为一个Handler容器，这个基类常常被用来实现为具有装饰模式的容器。*

- ConetextHandlerCollection
  一个专门的HandlerCollection，它用请求URL的最长的前缀来选择包含这个前缀的ContextHandler去处理请求。

> *译者文外补充：它是继承于`HandlerCollection`的，在其Javadoc中定义所描述：这个`HandlerCollection`的子类可以对contexts和这个容器里面包含的handlers基于context path和`ContextHandlers`类的关系生成一一对应的映射。contexts并不需要直接装进这个容器中，你只需要把handlers装进去就可以了。多个contexts可能会有一样的context path，它们会按照顺序被调用直到其中一个handler处理了这次请求。*

<br>

<span id="21234scoped-handlers"></span>
###### 21.2.3.4、Scoped Handlers

许多标准Servlet容器，Jetty是用`HandlerWrapper`实现的，它把很多handler以良好的链式处理连在一起：`ContextHandler`-`SessionHandler`-`SecurityHandler`-`ServletHandler`。然而，因为Servlet规范的特性，这种链式处理并不能就这么单纯的就把handler嵌套起来，因为外层handler有时候会需要内层handler处理过的信息。比如说当ContextHandler调用应用的监听器以通知有请求进到context的时候，ServletHandler必须提前知道请求该转发到哪一个servlet，这样才好返回正确的servletPath值。

`HandlerWrapper`是`ScopedHandler`抽象类的父类，这个抽象类提供了一个良好的链式范围。比如说一个ServletHandler嵌套在ContextHandler中，执行嵌套的顺序如下：
```
Server.handle(...)
  ContextHandler.doScope(...)
    ServletHandler.doScope(...)
      ContextHandler.doHandle(...)
        ServletHandler.doHandle(...)
          SomeServlet.service(...)
```
这样一来，当ContextHandler处理到请求后，在指定的ServletHandler里面也会处理一遍。

> *译者文外补充：`ServletHandler`和`ContextHandler`都是`ScopedHandler`的具体实现。*

<br>

<span id="21235resource-handler"></span>
###### 21.2.3.5、Resource Handler

FileServer栗子展示了如何使用Resource Handler去为当前工作目录下的静态资源服务：
```
package org.eclipse.jetty.embedded;

import org.eclipse.jetty.server.Handler;
import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.server.handler.DefaultHandler;
import org.eclipse.jetty.server.handler.HandlerList;
import org.eclipse.jetty.server.handler.ResourceHandler;

/**
 * Simple Jetty FileServer.
 * This is a simple example of Jetty configured as a FileServer.
 */
public class FileServer
{
    public static void main(String[] args) throws Exception
    {
        // 创建Jetty的server到8080端口
        // 如果你设置的端口号为0 那么Jetty会随机为你选择一个当前系统可用的端口号
        // 你可以在启动的时候通过log看到随机选择的端口号
        // 或者使用测试案例来获取
        Server server = new Server(8080);

        // 创建ResourceHandler
        // 这会为给定的资源文件处理请求
        // 这是Jetty Handler对象 所以你可以把它放在链式处理过程中
        // 你可以在其他栗子中看到这个用法
        ResourceHandler resource_handler = new ResourceHandler();

        // 配置ResourceHandler
        // 设置resource base 来表明应该服务于那个目录下的文件
        // 在当前的栗子中 resource base 设置的是当前目录
        // 除此之外它还可以设置到其他任何JVM可以访问到的目录
        resource_handler.setDirectoriesListed(true);
        resource_handler.setWelcomeFiles(new String[]{ "index.html" });
        resource_handler.setResourceBase(".");

        // Add the ResourceHandler to the server.
        HandlerList handlers = new HandlerList();
        handlers.setHandlers(new Handler[] { resource_handler, new DefaultHandler() });
        server.setHandler(handlers);

        // Start things up! By using the server.join() the server thread will join with the current thread.
        // See "http://docs.oracle.com/javase/1.5.0/docs/api/java/lang/Thread.html#join()" for more details.
        server.start();
        server.join();
    }
}
```
你会注意到有一个HandlerList对象，里面链接了ResourceHandler和DefaultHandler，这样DefaultHandler会在访问到不存在资源的情况下生成404响应。


<br>
<span id="2124嵌入connectors"></span>
##### 21.2.4、嵌入Connectors
- 21.2.4.1、[一个Connectors](#21241一个connectors)
- 21.2.4.2、[多个Connectors](#21242多个connectors)

在上一个栗子中，`Server`通过端口号在内部创建了一个默认的Connector来监听所有通过这个端口号进来的请求。然而，一般我们用Jetty作嵌入式开发的时候，正确的做法是显示地去配置一个或多个connector提供给`Server`实例。

<span id="21241一个connectors"></span>
###### 21.2.4.1、一个Connectors

举个例子，`OneConnector.java`，实例化、配置、添加好了一个简单的HTTP Connector实例，并把这个实例添加到server中：
```
package org.eclipse.jetty.embedded;

import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.server.ServerConnector;

/**
 * A Jetty server with one connectors.
 */
public class OneConnector
{
    public static void main( String[] args ) throws Exception
    {
        // The Server
        Server server = new Server();

        // HTTP connector
        ServerConnector http = new ServerConnector(server);
        http.setHost("localhost");
        http.setPort(8080);
        http.setIdleTimeout(30000);

        // Set the connector
        server.addConnector(http);

        // Set a handler
        server.setHandler(new HelloHandler());

        // Start the server
        server.start();
        server.join();
    }
}
```
上栗的connector可以处理HTTP协议的请求，这样的实现方式是`ServerConnector`类的默认方式是一样的。
<br>

<span id="21242多个connectors"></span>
###### 21.2.4.2、多个Connectors

如果想要配置多个connectors的时候（比如HTTP+HTTPS），可能需要共享一些为HTTP连接配置过的参数。为了做到这一点，你需要显示地使用`ConnectionFactory`实例去配置`ServerConnector`类，然后提供它们通用的HTTP配置。

> *译者文外补充：这里文档并没有给出示例代码，代码的下载链接也挂了，后面仅有一段关于这个多连接栗子的描述，我就不翻译了。关于这多链接的示例代码，“已往之不谏”前辈的博客里面有，大家可以在[这里](http://www.cnblogs.com/yiwangzhibujian/p/5845623.html)去参考。*

> *栗子描述原文：The ManyConnectors example, configures a server with two `ServerConnector` instances: the http connector has a `HTTPConnectionFactory` instance; the https connector has a `SslConnectionFactory` chained to a `HttpConnectionFactory`. Both `HttpConnectionFactory` are configured based on the same `HttpConfiguration` instance, however the HTTPS factory uses a wrapped configuration so that a `SecureRequestCustomizer` can be added.*

<br>
<span id="2125嵌入servlets"></span>
##### 21.2.5、嵌入Servlets

Servlet是为HTTP请求提供业务逻辑的标准解决方案。Servlet除了它里面的request对象是不可变的之外，在其他方面和Jetty的Handler很像。在Jetty里面Servlet通过`ServletHandler`类去接入处理。这个类使用标准的路径映射去为请求匹配一个Servlet，你还需要设置请求的`servletPath`和`pathInfo`，然后通过一些Filters处理之后，把请求传递到servlet，以生成项响应。

以下的栗子展示了一个简答的Servlet mapping过程
```
package org.eclipse.jetty.embedded;

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.servlet.ServletHandler;

public class MinimalServlets
{
    public static void main( String[] args ) throws Exception
    {
        Server server = new Server(8080);

        // ServletHandler是最最最简单的方式去创建context handler
        // 来返回一个Servlet实例
        // 这个handler需要注册Servlet对象
        ServletHandler handler = new ServletHandler();
        server.setHandler(handler);

        // 通过这个类 Jetty可以实例化一个Servlet实例并把它嵌入到给定的context path上

        // 重要：
        // 这是一个原生的Servlet
        // 不是平常的那种被`@WebServlet`或者web.xml或者其他类似方式配置过的Servlet
        handler.addServletWithMapping(HelloServlet.class, "/*");

        // 开启
        server.start();

        // server.join()方法会让当前线程join并且wait
        server.join();
    }

    @SuppressWarnings("serial")
    public static class HelloServlet extends HttpServlet
    {
        @Override
        protected void doGet( HttpServletRequest request,
                              HttpServletResponse response ) throws ServletException,
                                                            IOException
        {
            response.setContentType("text/html");
            response.setStatus(HttpServletResponse.SC_OK);
            response.getWriter().println("<h1>Hello from HelloServlet</h1>");
        }
    }
}
```

<br>
<span id="2126嵌入contexts"></span>
##### 21.2.6、嵌入Contexts

一个`ContextHandler`是一个`ScopedHandler`，只有请求过来的URL前缀和我们设定的context path相匹配才会响应。请求匹配到它们对应的context path之后，会有它们对应的path方法，并且context范围内的资源是可用的，以下是可能的范围情况：

- 当请求到某个context的时候，加载这个context的类加载器范围是可用的。
- 通过`ServletContext`API设置的attribute集合都可用被使用。
- 通过`ServletContext`API设置的paramenters集合都可用被使用。
- 通过`ServletContext`API设置的用做静态资源请求的Resource库中的资源都可用被使用。
- 虚拟机的名字集合都可用被使用。

下面这个栗子就是通过包装`HelloHandler`来展示如何建立context：

```
package org.eclipse.jetty.embedded;

import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.server.handler.ContextHandler;

public class OneContext
{
    public static void main( String[] args ) throws Exception
    {
        Server server = new Server( 8080 );

        // 在"/hello"上添加一个handler
        ContextHandler context = new ContextHandler();
        context.setContextPath( "/hello" );
        context.setHandler( new HelloHandler() );

        // 现在可以访问 http://localhost:8080/hello

        server.setHandler( context );

        // Start the server
        server.start();
        server.join();
    }
}
```
当有许多context的时候，你可以嵌入一个`ContextHandlerCollection`来有效地检查请求URI，然后选择匹配的ContextHandler来处理请求。下面这个栗子就是告诉你如何配置多少context：
```
package org.eclipse.jetty.embedded;

import org.eclipse.jetty.server.Handler;
import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.server.handler.ContextHandler;
import org.eclipse.jetty.server.handler.ContextHandlerCollection;

public class ManyContexts
{
    public static void main( String[] args ) throws Exception
    {
        Server server = new Server(8080);

        ContextHandler context = new ContextHandler("/");
        context.setContextPath("/");
        context.setHandler(new HelloHandler("Root Hello"));

        ContextHandler contextFR = new ContextHandler("/fr");
        contextFR.setHandler(new HelloHandler("Bonjoir"));

        ContextHandler contextIT = new ContextHandler("/it");
        contextIT.setHandler(new HelloHandler("Bongiorno"));

        ContextHandler contextV = new ContextHandler("/");
        contextV.setVirtualHosts(new String[] { "127.0.0.2" });
        contextV.setHandler(new HelloHandler("Virtual Hello"));

        ContextHandlerCollection contexts = new ContextHandlerCollection();
        contexts.setHandlers(new Handler[] { context, contextFR, contextIT,
                contextV });

        server.setHandler(contexts);

        server.start();
        server.join();
    }
}
```

<br>
<span id="2127嵌入servletcontexts"></span>
##### 21.2.7、嵌入ServletContexts

`ServletContextHandler`的`ContextHandler`的一个重要子类，它支持标准的Servlet和sessions机制。接下来的栗子实例化了一个`DefaultServlet`来为/tmp/目录下的静态资源服务，还有一个`DumpServlet`，它负责创建session和传储请求中的一些基本细节：

```
package org.eclipse.jetty.embedded;

import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.servlet.DefaultServlet;
import org.eclipse.jetty.servlet.ServletContextHandler;

public class OneServletContext
{
    public static void main( String[] args ) throws Exception
    {
        Server server = new Server(8080);

        ServletContextHandler context = new ServletContextHandler(
                ServletContextHandler.SESSIONS);
        context.setContextPath("/");
        context.setResourceBase(System.getProperty("java.io.tmpdir"));
        server.setHandler(context);

        // Add dump servlet
        context.addServlet(DumpServlet.class, "/dump/*");
        // Add default servlet
        context.addServlet(DefaultServlet.class, "/");

        server.start();
        server.join();
    }
}
```

<br>

> *译者文外补充：这里的`DumpServlet`只是示例，不是JettyAPI提供的实例。这里的`setResourceBase()`方法很重要，这跟我们一般使用Tomcat服务器，而我们的资源路径默认就是项目下的webapp目录是一样的，我们需要靠这个来设置Jetty，以达到同样的效果。*

<br>
<span id="2128嵌入web应用程序"></span>
##### 21.2.8、嵌入Web应用程序

一个`WebAppContext`是一个`ServletContextHandler`的拓展，它使用标准布局和`web.xml`来配置使用注解或者在web.xml中配置的Servlet、filters和其他功能。下面这个栗子配置了一个Jetty的测试webapp。Web应用可以使用容器提供的资源，在这个栗子里面的`LoginService`正是这样配置的：
```
package org.eclipse.jetty.embedded;

import java.io.File;
import java.lang.management.ManagementFactory;

import org.eclipse.jetty.jmx.MBeanContainer;
import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.server.handler.AllowSymLinkAliasChecker;
import org.eclipse.jetty.webapp.WebAppContext;

public class OneWebApp
{
    public static void main( String[] args ) throws Exception
    {
        Server server = new Server(8080);

        // 设置JMX
        MBeanContainer mbContainer = new MBeanContainer(
                ManagementFactory.getPlatformMBeanServer());
        server.addBean(mbContainer);

        // WebAppContext对象是用来控制它自己赖以生存的环境的。
        // 在这个栗子里面，context path设置为“/”
        // 这样它就可以处理根context下的请求了
        // 我们还可以看到它设置了war的位置
        // 整个host的其他配置都是可用的可配置的
        // 你可用配置注解扫描的支持（需要通过PlusConfiguration）
        // 还可用选择webapp在哪里自动解压
        WebAppContext webapp = new WebAppContext();
        webapp.setContextPath("/");
        File warFile = new File(
                "../../tests/test-jmx/jmx-webapp/target/jmx-webapp");
        webapp.setWar(warFile.getAbsolutePath());

        // 一个WebAppContext是一个ContextHandler
        // 所以它也需要被配置到server里面这样server才会知道请求将被送往哪里
        server.setHandler(webapp);

        // Start things up!
        server.start();

        server.dumpStdErr();

        server.join();
    }
}
```

<br>

> *译者文外补充：栗子中的JMX是一种额外组件，不是Jetty的服务。你可以准备一些war包自己测试。也可以不使用war包，直接使用有标准web应用布局的目录。*

<br>
<span id="2129像jettyxml一样进行嵌入式开发"></span>
##### 21.2.9、像JettyXML一样进行嵌入式开发


<br>


<span id="213嵌入式开发的栗子"></span>
#### 21.3、嵌入式开发的栗子

[回到顶部](#top)
- - -