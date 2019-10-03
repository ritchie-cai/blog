
《深入拆解Tomcat & Jetty》李号双，eBay技术主管

### 一、开篇词
要独当一面，离不开技术的深度和广度。
- 技术的广度体现在你的知识是成体系的，从前端到后端、从应用层面到操作系统、从软件到硬件、从开发、测试、部署到运维…有些领域虽然你不需要挖得很深，但是你必须知道这其中的“门道”。
- 而技术的深度体现在对于某种技术，你不仅知道怎么用，还知道这项技术如何产生的、它背后的原理是什么，以及它为什么被设计成这样，甚至你还得知道如何去改进它。
可以先突破深度，再以点带面拓展广度，因此可以通过深入学习一些优秀的开源系统来达到突破深度的目的。

### 二、必备基础
#### 1. Web容器学习路径
Tomcat和Jetty就是一个Servlet容器。为了方便使用，它们也具有HTTP服务器的功能，因此Tomcat或者Jetty就是一个“HTTP服务器 + Servlet容器”，我们也叫它们Web容器。其他应用服务器比如JBoss和WebLogic，它们不仅仅有Servlet容器的功能，也包含EJB容器，是完整的Java EE应用服务器。从这个角度看，Tomcat和Jetty算是一个轻量级的应用服务器。

- 基础知识：《UNIX环境高级编程》

#### 2. HTTP协议必知必会
HTTP是通信的方式，HTML才是通信的目的，就好比HTTP是信封，信封里面的信（HTML）才是内容；但是没有信封，信也没办法寄出去。HTTP协议就是浏览器与服务器之间的沟通语言，具体交互过程是请求、处理和响应。
<br/>
由于HTTP是无状态的协议，为了识别请求是哪个用户发过来的，出现了Cookie和Session技术。Cookie本质上就是一份存储在用户本地的文件，里面包含了每次请求中都需要传递的信息；Session可以理解为服务器端开辟的存储空间，里面保存的信息用于保持状态。作为Web容器，Tomcat负责创建和管理Session，并提供了多种持久化方案来存储Session。

#### 3. 你应该知道的Servlet规范和Servlet容器
Servlet 接口和 Servlet 容器这一整套规范叫作 Servlet 规范。Tomcat 和 Jetty 都按照 Servlet 规范的要求实现了 Servlet 容器，同时它们也具有 HTTP 服务器的功能。
<br/>
当客户请求某个资源时，HTTP 服务器会用一个 ServletRequest 对象把客户的请求信息封装起来，然后调用 Servlet 容器的 service 方法，Servlet 容器拿到请求后，根据请求的 URL 和 Servlet 的映射关系，找到相应的 Servlet，如果 Servlet 还没有被加载，就用反射机制创建这个 Servlet，并调用 Servlet 的 init 方法来完成初始化，接着调用 Servlet 的 service 方法来处理请求，把 ServletResponse 对象返回给 HTTP 服务器，HTTP 服务器会把响应发送给客户端。同样我通过一张图来帮助你理解。

![工作流程](https://static001.geekbang.org/resource/image/b7/15/b70723c89b4ed0bccaf073c84e08e115.jpg)

Servlet 规范里定义了`ServletContext`这个接口来对应一个 Web 应用。Web 应用部署好后，Servlet 容器在启动时会加载 Web 应用，并为每个 Web 应用创建唯一的 ServletContext 对象。你可以把 ServletContext 看成是一个全局对象，一个 Web 应用可能有多个 Servlet，这些 Servlet 可以通过全局的 ServletContext 来共享数据，这些数据包括 Web 应用的初始化参数、Web 应用目录下的文件资源等。由于 ServletContext 持有所有 Servlet 实例，你还可以通过它来实现 Servlet 请求的转发。

<br/>
不知道你有没有发现，引入了 Servlet 规范后，你不需要关心 Socket 网络通信、不需要关心 HTTP 协议，也不需要关心你的业务类是如何被实例化和调用的，因为这些都被 Servlet 规范标准化了，你只要关心怎么实现的你的业务逻辑。这对于程序员来说是件好事，但也有不方便的一面。所谓规范就是说大家都要遵守，就会千篇一律，但是如果这个规范不能满足你的业务的个性化需求，就有问题了，因此设计一个规范或者一个中间件，要充分考虑到可扩展性。Servlet 规范提供了两种扩展机制：
- 是过滤器，这个接口允许你对请求和响应做一些统一的定制化处理，比如你可以根据请求的频率来限制访问，或者根据国家地区的不同来修改响应内容。过滤器的工作原理是这样的：Web 应用部署完成后，Servlet 容器需要实例化 Filter 并把 Filter 链接成一个 FilterChain。当请求进来时，获取第一个 Filter 并调用 doFilter 方法，doFilter 方法负责调用这个 FilterChain 中的下一个 Filter。
- 是监听器，这是另一种扩展机制。当 Web 应用在 Servlet 容器中运行时，Servlet 容器内部会不断的发生各种事件，如 Web 应用的启动和停止、用户请求到达等。 Servlet 容器提供了一些默认的监听器来监听这些事件，当事件发生时，Servlet 容器会负责调用监听器的方法。当然，你可以定义自己的监听器去监听你感兴趣的事件，将监听器配置在 web.xml 中。比如 Spring 就实现了自己的监听器，来监听 ServletContext 的启动事件，目的是当 Servlet 容器启动时，创建并初始化全局的 Spring 容器。

### 三、整体架构


### 四、连接器
