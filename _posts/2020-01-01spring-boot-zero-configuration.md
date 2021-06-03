# Servlet 3.1 新特性

为了介绍Servlet 3.1 的规范，哥们特地找到了官方文档，如下链接：
[https://download.oracle.com/otn-pub/jcp/servlet-3_1-fr-eval-spec/servlet-3_1-final.pdf?AuthParam=1579218798_b8308a6fa06213a941cdf0a1935f6b88](https://download.oracle.com/otn-pub/jcp/servlet-3_1-fr-eval-spec/servlet-3_1-final.pdf?AuthParam=1579218798_b8308a6fa06213a941cdf0a1935f6b88)

## 如何取代web.xml

以下内容节选自官方文档。
The following methods are added to ServletContext since Servlet 3.0 to enable
programmatic definition of servlets, filters and the url pattern that they map to.
These methods can only be called during the initialization of the application either
from the contexInitialized method of a ServletContextListener
implementation or from the onStartup method of a
ServletContainerInitializer implementation. In addition to adding Servlets
and Filters, one can also look up an instance of a Registration object
corresponding to a Servlet or Filter or a map of all the Registration objects for the
Servlets or Filters. If the ServletContext passed to the ServletContextListener’s
contextInitialized method where the ServletContextListener was neither
declared in web.xml or web-fragment.xml nor annotated with @WebListener
then an UnsupportedOperationException MUST be thrown for all the methods
defined in ServletContext for programmatic configuration of servlets, filters and
listeners.

大致意思如下：

- 从 Servlet 3.0 开始，便开始支持编程式地去定义 servlet , filter 等。
- 自定义的 servlet , filter，将会在 ServletContextListener 的 contexInitialized 方法里执行或者是在ServletContainerInitializer 的 onStartup 方法里执行。
- 如果你想把 ServletContext 传递给 ServletContextListener 的 contexInitialized 方法，那么ServletContextListener 需要配置在 web.xml 中。

从以上可以看出，如果我们使用 ServletContainerInitializer 的 onStartup 方法，在这个方法中去编程式地定义 servlet，那么我们完全可以抛弃 web.xml。

以下是官方介绍的，关于我们这么做的好处。
The ability to programmatically add a servlet to a context is useful for framework
developers. For example a framework could declare a controller servlet using this
method. The return value of this method is a ServletRegistration or a
ServletRegistration.Dynamic object which further allows you to setup the
other parameters of the servlet like init-params, url-mappings etc. There are
three overloaded versions of the method as described below.

那么我们可以有哪些方式去注册我们的 servlet 呢？如下所示：

- addServlet(String servletName, String className) 

This method allows the application to declare a servlet programmatically. It adds the
servlet with the given name, and class name to the servlet context.

- addServlet(String servletName, Servlet servlet)

This method allows the application to declare a servlet programmatically. It adds the
servlet with the given name, and servlet instance to the servlet context.

- addServlet(String servletName, Class 
servletClass)

This method allows the application to declare a servlet programmatically. It adds the
servlet with the given name, and an instance of the servlet class to the servlet
context.


# Spring 如何支持 Servlet 3.1

不知道大家有没有这样的一个疑问，当我们写一个 ServletContainerInitializer 的实现类时，web 容器怎么知道如何去识别呢？首先我们必须认清以下事实：

- Servlet 绝对不会去依赖 Spring 相关的 JAR 包，因为 Servlet 是规范。
- Tomcat 是对 Servlet 规范的实现。
- Tomcat 是绝对不会去依赖 Spring 的，因为 Tomcat 是 Servlet 实现者。
- Spring 整合 Tomcat 的代码，绝对是 Spring 去开发的。

 
那么 Spring 到底是如何做的呢？这里你应该可以想到，需要借助 SPI 机制来指定该初始化类。
这一步骤是通过在项目路径下创建 `META-INF/services/javax.servlet.ServletContainerInitializer` 来做到的，它只包含一行内容：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579221226908-f2b9f7a8-9301-4454-9f31-203efaa4baae.png#align=left&display=inline&height=302&name=image.png&originHeight=302&originWidth=968&size=30151&status=done&style=none&width=968)

你可以猜到，这里的 SpringServletContainerInitializer 就是实现了 ServletContainerInitializer .
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579221313958-30026f08-663a-403e-92ac-aafae33993f8.png#align=left&display=inline&height=243&name=image.png&originHeight=243&originWidth=871&size=20800&status=done&style=none&width=871)

而且在这个实现类中，你可以很清楚的看到如下的 javadoc .

```java
/**
 * Servlet 3.0 {@link ServletContainerInitializer} designed to support code-based
 * configuration of the servlet container using Spring's 
 * {@link WebApplicationInitializer} SPI as opposed to (or possibly in 
 * combination with) the traditional {@code web.xml}-based approach.
 *
 * 该段文档大意如下：
 * Servlet 3.0 规范中的 {@link ServletContainerInitializer} 被设计出来，就是去为了支持
 * 编程式地去配置 servlet container. 而在 Spring 当中,你可以使用 
 * {@link WebApplicationInitializer} 并结合 SPI 机制, 就可以达到与使用传统的 
 * web.xml 方式同样的效果.
 *
 * <h2>Mechanism of Operation</h2>
 * This class will be loaded and instantiated and have its {@link #onStartup}
 * method invoked by any Servlet 3.0-compliant container during container startup 
 * assuming that the {@code spring-web} module JAR is present on the classpath. 
 * This occurs through the JAR Services API {@link ServiceLoader#load(Class)} method 
 * detecting the {@code spring-web} module's 
 * {@code META-INF/services/javax.servlet.ServletContainerInitializer}
 * service provider configuration file. See the
 * <a href="https://download.oracle.com/javase/6/docs/technotes/guides/jar/jar.html>
 * JAR Services API documentation</a> as well as section <em>8.2.4</em> of the 
 * Servlet 3.0 Final Draft specification for complete details.
 *
 */
```

如果你细心一点的话，将会发现，在这个实现类里面，并没有去做 Servlet 的定义与注册？那这一步在哪里去实现的呢？你可能发现了，在 SpringServletContainerInitializer 实现类上面有 HandlesTypes 注解。其实当程序启动的时候，SpringServletContainerInitializer 的 onStartup 方法会被调用，这个方法的入参将会带入 servletContext 以及
HandlesTypes 注解里面的接口下面的所有的实现类。然后再这个 onStartup 方法内部，将会循环的去调用这些实现类的方法，并将 servletContext 传递给这个实现类。可以猜到，HandlesTypes 注解必定也是 Servlet 所提供的。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579272250624-c97d055a-baf4-46c8-bfe6-cdfa95d128c8.png#align=left&display=inline&height=513&name=image.png&originHeight=513&originWidth=812&size=37464&status=done&style=none&width=812)

如果你不相信的，那么请看 spring mvc 的官方是如何做的。

spring mvc 官方地址如下：
[https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579222844897-2045e560-b7a0-4476-ba8b-644f9f310a4f.png#align=left&display=inline&height=483&name=image.png&originHeight=483&originWidth=741&size=32984&status=done&style=none&width=741)

到目前为止，你已经搞清楚了基本原理，那么接下来我们按照这个理论，去全程模拟 spring boot 的零配置。

# 模拟 Spring Boot 零配置

首先参考 spring mvc 官方的做法，启动一个 spring 容器，然后直接 new 出来一个 servlet 实例。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579269689167-edaa08b7-5d45-408e-b0d2-5d24906daf72.png#align=left&display=inline&height=488&name=image.png&originHeight=488&originWidth=875&size=35966&status=done&style=none&width=875)

因为当前我使用的 tomcat 的版本是 8.5.43, 这个版本实现了 Servlet 3.1 的规范，也就是说只要在当前项目中的
classpath路径下，存在如下文件：
META-INF/services/javax.servlet.ServletContainerInitializer
并且在该文件中，存在 ServletContainerInitializer 的实现类，那么 tomcat 在启动的时候，就会去执行这个实现类的 onStartup 方法。很明显，在当前的项目下，我们是存在这种情况的。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579271004593-406808b2-2ff2-4116-b7da-fd8cd7bd9507.png#align=left&display=inline&height=523&name=image.png&originHeight=523&originWidth=1116&size=50529&status=done&style=none&width=1116)

用于测试的 Controller ：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579270036456-8b9b4999-a925-40ce-922e-df3c83cab1df.png#align=left&display=inline&height=400&name=image.png&originHeight=400&originWidth=464&size=18595&status=done&style=none&width=464)

其次，我们模仿 spring boot 的做法，也搞一个自己的 SpringApplication，然后在里面启动一个 tomcat 实例。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579412865790-a870eb1c-a1c3-4060-8bff-7f712cbfd07c.png#align=left&display=inline&height=427&name=image.png&originHeight=427&originWidth=763&size=41125&status=done&style=none&width=763)

然后运行我们的程序，发现并不是刚才我们分析的那样。SpringServletContainerInitializer 并没有如期执行。那么问题究竟出在什么地方呢？请注意一点的是，我们刚才分析的那么多，一切都是基于当前项目是一个 web 项目的前提下才成立。尽管当前我们的项目看上去不像是一个 web 项目，但是当 tomcat 执行了如下代码：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579413417287-eefee892-f3f4-4ac5-8d95-570c7120c2d1.png#align=left&display=inline&height=446&name=image.png&originHeight=446&originWidth=1031&size=52719&status=done&style=none&width=1031)
就相当于我们告诉了 tomcat，将当前项目当成是一个 web 项目来看待。评定一个项目是不是 web 项目，而是由 tomcat 决定。只要你调用了addWebapp方法，那么在 tomcat 看来，当前项目就是一个 web 项目。

现在再次重新启动，如下：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579270179556-efddd045-07c4-4472-ac0f-cb0f5bca85b7.png#align=left&display=inline&height=585&name=image.png&originHeight=585&originWidth=903&size=71646&status=done&style=none&width=903)

在启动的过程中，你将会看到，SpringServletContainerInitializer 的onStartup确实执行了。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579272497023-09cedeea-d040-4847-ac65-81a4b43f7622.png#align=left&display=inline&height=498&name=image.png&originHeight=498&originWidth=951&size=73666&status=done&style=none&width=951)

并且在我们的项目中，WebApplicationInitializer 这个接口的实现类的 onStartup 也被执行了。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579272635927-0a08d89f-392c-4028-97ab-e2bf3bd9c581.png#align=left&display=inline&height=552&name=image.png&originHeight=552&originWidth=1098&size=86024&status=done&style=none&width=1098)

同时你也看到，当程序完全程序启动成功后，在控制台这里报了一个错误，是一个JSP的问题，暂时可以不需要理会，后面我再来解释这个问题。接着，我们访问如下链接, 你将会看到，程序已经进入到了debug状态。
http://localhost:9090/hello 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579322438173-c5076602-5a8f-4cdf-93bd-5b7fd28bc17d.png#align=left&display=inline&height=551&name=image.png&originHeight=551&originWidth=782&size=58648&status=done&style=none&width=782)

## 如何配置解析器

这个是很简单的，其实 spring mvc 官方网站都给出了参考，链接如下：[https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-config-message-converters](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-config-message-converters)
如下代码是官方网站给出的案例。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579323705384-3f217a82-8ef1-4450-bfb9-f3ee7833ccf7.png#align=left&display=inline&height=361&name=image.png&originHeight=361&originWidth=906&size=31580&status=done&style=none&width=906)

如果是之前使用 XML 配置文件的话，需要写下面的一大坨配置。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579323837842-b3864b23-272e-4edb-876a-3495d73a71f0.png#align=left&display=inline&height=438&name=image.png&originHeight=438&originWidth=900&size=38119&status=done&style=none&width=900)

现在我们在代码里想配置 JSON 的解析器，代码可以如下：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579324267256-5ff14952-33a6-4873-a4b7-c508329a3d6a.png#align=left&display=inline&height=411&name=image.png&originHeight=411&originWidth=1122&size=44109&status=done&style=none&width=1122)

那么为什么我们实现了 WebMvcConfigurer , 里面的方法就会被调用呢？其实这里是一个回调。如果你不加 [@EnableWebMvc](#) 这个注解的话，这个方法是不会被回调的。官方网站上说的很清楚。官方网站如下 : 
[https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-async-configuration-spring-mvc](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-async-configuration-spring-mvc)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579324900041-2b166ac5-c570-4169-b828-72623ce57b3e.png#align=left&display=inline&height=171&name=image.png&originHeight=171&originWidth=736&size=22967&status=done&style=none&width=736)
使用 [@EnableWebMvc](#) 这个注解，相当于导入了 DelegatingWebMvcConfiguration 这个类。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579325160306-91406775-6755-4923-8ed8-eadbf116efe0.png#align=left&display=inline&height=470&name=image.png&originHeight=470&originWidth=876&size=45610&status=done&style=none&width=876)

同样，如果你不想使用 [@EnableWebMvc](#) ，那么你可以直接去继承 DelegatingWebMvcConfiguration . 如下所示 :
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579324553112-914d630d-743b-4724-be52-9b453cb3f982.png#align=left&display=inline&height=400&name=image.png&originHeight=400&originWidth=920&size=41661&status=done&style=none&width=920)

一切都配置好之后，现在我们来启动一下，你会发现启动不起来？ 为什么呢？ 这是因为 spring mvc 官方网站上面的一个坑，好吧。我们先看现象，然后我再去分析理论。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579331074855-3d4c2565-684f-4058-ae22-4787d322d9a0.png#align=left&display=inline&height=513&name=image.png&originHeight=513&originWidth=887&size=67933&status=done&style=none&width=887)

为什么会出现这个异常呢？
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579331658838-0870eaf0-15fe-4957-a766-f9a0a14bd082.png#align=left&display=inline&height=551&name=image.png&originHeight=551&originWidth=1139&size=80856&status=done&style=none&width=1139)

当 tomcat 启动的时候，执行到这行代码，这行代码就会去初始化 spring 容器，当它初始 spring容器的时候，WebConfiguration 中刚才我们使用的 @EnableWebMvc 这个注解的实现需要使用到 Servlet Context 上下文，但是此时我们的 Servlet 根本还没有实例化，故此时根本就不会有 Servlet Context 上下文，所以 spring 容器的初始化过程必定会失败。我带你去看一下 @EnableWebMvc 底层确实需要依赖 ServletContext 这个对象。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579334143720-074915fb-cf01-4f68-8fe7-f5abb0cee7f9.png#align=left&display=inline&height=560&name=image.png&originHeight=560&originWidth=998&size=77919&status=done&style=none&width=998)

其实 ac.refresh() 这行代码完全可以不需要？为什么呢？这里需要你有很深的 spring 功底，你才能想明白。现在我来带你看一下。

当执行下面代码的时候：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579332530260-0621bb00-2d66-46ac-b979-a470f5084362.png#align=left&display=inline&height=93&name=image.png&originHeight=93&originWidth=834&size=10028&status=done&style=none&width=834)
你注意看，有一行代码执行 setLoadOnStartup(1) , 意思是当 servlet 被实例化的时候，同时也要去执行这个 servlet 的 init 方法。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579332998938-4713490c-9d11-4951-9fd2-3d221937e86e.png#align=left&display=inline&height=587&name=image.png&originHeight=587&originWidth=844&size=70707&status=done&style=none&width=844)

而在这个 init 方法里面，会去调用 initServletBean 方法。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579333109922-cd885dcd-7846-41bb-bf3b-5c217bdc058b.png#align=left&display=inline&height=433&name=image.png&originHeight=433&originWidth=925&size=59654&status=done&style=none&width=925)

而在 initServletBean 方法内部，调用了 initWebApplicationContext 方法。之后在这里方法内部，将会去调用
configureAndRefreshWebApplicationContext 方法。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579334595137-20ade6ec-fd83-40c9-8d79-f1f63d488e3e.png#align=left&display=inline&height=542&name=image.png&originHeight=542&originWidth=1174&size=86768&status=done&style=none&width=1174)

然后在 configureAndRefreshWebApplicationContext  方法内部，会去执行 ac.refresh() 方法，就会去初始化 spring 的容器。这个 refresh 方法和刚才我们手动在外面执行的那个 refresh 方法是一样的。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579334806252-ddb0a623-8011-469b-a721-5f802187ed7d.png#align=left&display=inline&height=539&name=image.png&originHeight=539&originWidth=842&size=71863&status=done&style=none&width=842)  
这就是网上说的父容器和子容器等话题，也不是一两句就能说清楚的，之后我再撰文详细的说明。在这里，你只要知道，init 方法内部会去初始化 spring 容器。

## 利用Servlet 3.1 新规范

我们首先在自己的项目中 classpath 目录下，也搞一个如下文件：
META-INF/services/javax.servlet.ServletContainerInitializer
并且在该文件中，配置一个自己的 ServletContainerInitializer 的实现类。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579495795437-1dc9c401-93e5-43ac-9b8d-4cd1e08a3535.png#align=left&display=inline&height=420&name=image.png&originHeight=420&originWidth=949&size=36261&status=done&style=none&width=949)

NpfServletContainerInitializer 具体实现如下：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579495980227-d3a31ba0-a409-4031-bf68-9a54d208fb01.png#align=left&display=inline&height=512&name=image.png&originHeight=512&originWidth=1162&size=77697&status=done&style=none&width=1162)

请注意以下两点：

- NpfServletContainerInitializer 实现了 ServletContainerInitializer.
- NpfServletContainerInitializer 类上面有 @HandlesTypes 注解，注解里面是一个接口。

接下来，我们写一个 SpringBootDemoServletHandler 的实现类。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579496069794-8edf26b1-ce6d-4fbe-b798-45896c74cea0.png#align=left&display=inline&height=408&name=image.png&originHeight=408&originWidth=1133&size=48812&status=done&style=none&width=1133)

经过这些步骤后，我们启动应用程序，你应该可以看到，这些类的方法都被调用到了。

首先是 NpfServletContainerInitializer 的 onStartup 方法被调用。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579496159192-69cfe5eb-a2ab-4ad3-a6f1-f90dad6216fe.png#align=left&display=inline&height=516&name=image.png&originHeight=516&originWidth=1161&size=77028&status=done&style=none&width=1161)

其次是 NpfSpringBootDemoServletHandler 的 onStartup 方法被调用。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579496245501-cf15bba9-c22b-4b56-950f-87fb8d11ff99.png#align=left&display=inline&height=342&name=image.png&originHeight=342&originWidth=1131&size=42271&status=done&style=none&width=1131)

完全符合我们的预期。

## 解释 JSP 异常

现在我们再来解释一下刚才出现的那个JSP的异常。为什么会出现这个异常呢，因为少了一个 JAR 包。

```xml
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
    <version>9.0.30</version>
</dependency>
```

但是这个 JAR 包可有可无，因为 Tomcat 如果将当前项目已 web 项目形式启动的话，那么默认情况下，需要依赖 JSP。例如我们刚才上面所示，我们是直接调用 addWebApp 方法，直接告诉 tomcat ，让 tomcat 将当前项目已 web 形式启动，但是这种情况下，必须得依赖 JSP. 但是你想想，Spring Boot 并没有强依赖 JSP，甚至 Spring Boot 都不推荐 JSP. 那究竟是怎么做到的呢？ 我只想告诉你，刚才我所分析的那么多，Spring Boot 底层并不是那样做的，为什么呢？ 你也看到了，如果是这样的话，就会强依赖 JSP 了。你很急是么，那么请看下面的分析吧。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579594210961-ce9d5f87-09c3-470e-b055-7a7eeeb4de0d.png#align=left&display=inline&height=506&name=image.png&originHeight=506&originWidth=1253&size=98183&status=done&style=none&width=1253)

接下去，我们需要修改 Tomcat 的启动逻辑，将上述代码放入到和 tomca 一起。如下所示：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579594296873-470285f0-7be6-491f-8490-0807312d5df5.png#align=left&display=inline&height=474&name=image.png&originHeight=474&originWidth=1297&size=67925&status=done&style=none&width=1297)

现在我们再次启动程序，此时已经没有 JSP 异常了。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579594344151-d85d9712-84b2-4888-bdf8-7fd840833c36.png#align=left&display=inline&height=607&name=image.png&originHeight=607&originWidth=875&size=70580&status=done&style=none&width=875),

接着访问 http://localhost:9090/hello
![image.png](https://cdn.nlark.com/yuque/0/2020/png/749466/1579594400920-c30508b8-e474-47ed-8301-e55cd1911268.png#align=left&display=inline&height=379&name=image.png&originHeight=379&originWidth=747&size=42215&status=done&style=none&width=747)

你可以看到，我们已经成功了。我可以肯定地告诉你，Spring Boot 底层就是这么干的。
