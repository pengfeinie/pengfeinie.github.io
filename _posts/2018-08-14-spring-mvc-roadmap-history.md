---
title: 'spring mvc roadmap history'
date: 2018-08-14
tags:
  - spring
---
## 1. Introduction

When using Spring in a web application, we have several options for organizing the application contexts that wire it all up. In this article, we're going to analyze and explain the most common options that Spring offers.

## 2. Java Ecosystem

### 2.1 Servlet timeline

![image-20210901140126970](https://pengfeinie.github.io/images/image-20210901140126970.png)

### 2.2 J2EE timeline

The Java EE - Java Platform, Enterprise Edition (formerly known as J2EE) has undergone several changes since its inception. This page covers high level time line of different versions and features introduced in each version in chronological order.

![image-20210901175023598](https://pengfeinie.github.io/images/image-20210901175023598.png)

### 2.3 Java timeline

Java has been the one of the most used and popular language. This page covers high level time line of different versions and features introduced in each version in chronological order.![image-20210901175517116](https://pengfeinie.github.io/images/image-20210901175517116.png)

## 3. Service Provider Interface

### 3.1 Overview

Java 6 has introduced a feature for discovering and loading implementations matching a given interface: Service Provider Interface (SPI). Service Provider Interface is an API proposed to be implemented or customized by a third-party provider. It can use as an extension or replaceable by existing implementations. A Service is an extension of the set of interfaces, and Service Provider is an implementation of the service. Classes in the provider category will generally implement the service interface or expand the classes provided in the service. A Service Provider will provide a feature to customize the provider according to the requirements. Service Provider provides the feature to extend the service without modifying the original code base.

### 3.2 Terms and Definitions of Java SPI

**Service**

A well-known set of programming interfaces and classes that provide access to some specific application functionality or feature.

**Service Provider Interface**

An interface or abstract class that acts as a proxy or an endpoint to the service. A specific implementation of the SPI. The Service Provider contains one or more concrete classes that implement or extend the service type. If the service is one interface, then it is the same as a service provider interface. Service and SPI together are well-known in the Java Ecosystem as API.

**Service Provider**

A Service Provider is configured and identified through a provider configuration file which we put in the resource directory *META-INF/services*. The file name is the fully-qualified name of the SPI and its content is the fully-qualified name of the SPI implementation. The Service Provider is installed in the form of extensions, a jar file which we place in the application classpath, the Java extension classpath or the user-defined classpath.

**ServiceLoader**

At the heart of the SPI is the [*ServiceLoader*](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/ServiceLoader.html) class. This has the role of discovering and loading implementations lazily. It uses the context classpath to locate provider implementations and put them in an internal cache.

## 4. Servlet container

### 4.1 Overview

This section provides a list of API specifications which are implemented by Apache Tomcat.

The specifications are developed and maintained by the [Java Community Process (JCP)](https://www.jcp.org/). The members of the JCP are coming from software industry, other organizations like the Apache Software Foundation (ASF), educational institutions but include also individual (personal) members.

Each specifications starts its life as a so-called Java Specification Request JSR. The JSRs are also known by the unique number they receive once the specification process starts. On the web site of the JCP you can find an overview page for each spec, and a separate download page. The download page lists various stages of each spec reflecting the development process of JCP specs. Examples are "Early Draft Review", "Public Final Draft" and "Final Release". You would like to make sure that you always access the latest documents.

Different Tomcat versions implement **different** versions of the specifications (see [main site](https://tomcat.apache.org/whichversion.html), [wiki](https://cwiki.apache.org/confluence/display/TOMCAT/Tomcat+Versions)).[https://cwiki.apache.org/confluence/display/TOMCAT/Specifications](https://cwiki.apache.org/confluence/display/TOMCAT/Specifications)

| Spec versions:          | Servlet 2.5                                                  | Servlet 3.0                                                  | Servlet 3.1                                                  |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Main page:              | [JSR154](https://www.jcp.org/en/jsr/summary?id=154)          | [JSR315](https://www.jcp.org/en/jsr/summary?id=315)          | [JSR340](https://www.jcp.org/en/jsr/detail?id=340)           |
| Stable:                 | Maintenance Release                                          | Final Release                                                | Final Release                                                |
| Date:                   | 11 May, 2006                                                 | 10 Dec, 2009                                                 | 28 May, 2013                                                 |
| Download Page:          | [Overview](https://jcp.org/aboutJava/communityprocess/mrel/jsr154/index.html) [Direct Download - Javadoc, classes](https://download.oracle.com/otndocs/jcp/servlet-2.5-mrel-eval-oth-JSpec/) | [Overview](https://jcp.org/aboutJava/communityprocess/final/jsr315/index.html) [Direct Download](https://download.oracle.com/otndocs/jcp/servlet-3.0-fr-eval-oth-JSpec/) | [Overview](https://jcp.org/aboutJava/communityprocess/final/jsr340/index.html) [Direct Download](https://download.oracle.com/otndocs/jcp/servlet-3_1-fr-eval-spec/index.html) |
| Online Javadoc:         | [Java EE 5](https://docs.oracle.com/javaee/5/api/)           | [Java EE 6](https://docs.oracle.com/javaee/6/api/)           | [Java EE 7](https://docs.oracle.com/javaee/7/api/)           |
| Minimum Tomcat version: | 6.0.0                                                        | 7.0.0                                                        | 8.0.0                                                        |

## 5. Spring

![image-20210901141009993](https://pengfeinie.github.io/images/image-20210901141009993.png)

## 6. How to Register Servlet

### 6.1 Xml-based

[Servlet-2.5](https://download.oracle.com/otn-pub/jcp/servlet-2.5-mrel2-eval-oth-JSpec/servlet-2_5-mrel2-spec.pdf) The most common way to register a servlet within your J2EE application is to add it to your *web.xml* file:

```
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
         http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">

    <servlet>
        <servlet-name>HelloWorldServlet</servlet-name>
        <servlet-class>org.example.HelloWorldServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloWorldServlet</servlet-name>
        <url-pattern>/v1/hello/world</url-pattern>
    </servlet-mapping>

</web-app>
```

As you can see, this involves two steps: (1) adding our servlet to the *servlet* tag, making sure to also specify the source path to the class the servlet resides within, and (2) specifying the URL path the servlet will be exposed on in the *url-pattern* tag. The J2EE *web.xml* file is usually found in *WebContent/WEB-INF*.

### 6.2 Annotations-based

[Servlet-3.0](https://download.oracle.com/otn-pub/jcp/servlet-3.0-fr-oth-JSpec/servlet-3_0-final-spec.pdf) Now let's register our servlet using the *@WebServlet* annotation on our custom servlet class. This eliminates the need for servlet mappings in the *web.xml* and registration of the servlet in *web.xml*:

```
package org.example;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name = "helloWorldServlet",value = {"/v1/hello/world"})
public class HelloWorldServlet extends HttpServlet {

    protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
        String name = req.getParameter("name");
        resp.getOutputStream().write(name.getBytes());
    }
}
```

The code above demonstrates how to add that annotation directly to a servlet. The servlet will still be available at the same URL path as before.

### 6.3 Programmatic-based

[Servlet-3.0](https://download.oracle.com/otn-pub/jcp/servlet-3.0-fr-oth-JSpec/servlet-3_0-final-spec.pdf) The ability to programmatically add a servlet to a context is useful for framework developers. For example a framework could declare a controller servlet using this method.

```
package org.example;

import org.springframework.web.WebApplicationInitializer;
import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;
import org.springframework.web.servlet.DispatcherServlet;
import javax.servlet.ServletContext;
import javax.servlet.ServletRegistration;

public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) {
    	AnnotationConfigWebApplicationContext ac = new AnnotationConfigWebApplicationContext();
    	ac.register(AppConfig.class);
        DispatcherServlet d = new DispatcherServlet(ac);
        ServletRegistration.Dynamic r = servletContext.addServlet("dispatcherServlet", d);
        r.setLoadOnStartup(1);
        r.addMapping("/");
    }
}
```

The return value of this method is a ServletRegistration or a ServletRegistration.Dynamic object which further allows you to setup the other parameters of the servlet like init-params, url-mappings etc.

## 7. Web Application

### 7.1 Servlet 2.5

#### 7.1.1 [Servlet2.5-helloworld](https://github.com/pengfeinie/springmvc-history/tree/master/servlet2.5-helloworld)

Here you define the inherited methods to be generated inside the servlet, by default each servlet should implement doGet methods. After clicking finish, eclipse automatically creates a servlet class named HelloWorldServlet.java under *org.example* package as the following:

| Spec versions:          | Servlet 2.5                                                  |
| ----------------------- | ------------------------------------------------------------ |
| Main page:              | [JSR154](https://www.jcp.org/en/jsr/summary?id=154)          |
| Stable:                 | Maintenance Release                                          |
| Date:                   | 11 May, 2006                                                 |
| Download Page:          | [Overview](https://jcp.org/aboutJava/communityprocess/final/jsr315/index.html) [Direct Download](https://download.oracle.com/otndocs/jcp/servlet-3.0-fr-eval-oth-JSpec/) |
| Online Javadoc:         | [Java EE 5](https://docs.oracle.com/javaee/5/api/)(2006), JDK1.6(2006) |
| Minimum Tomcat version: | 6.0.0 (2007)                                                 |

```
package org.example;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class HelloWorldServlet extends HttpServlet {
	
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
        String name = req.getParameter("name");
        resp.getOutputStream().write(name.getBytes());
    }
}
```

it also writes the definition of the servlet under web.xml as the following:

```
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
         http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">

    <servlet>
        <servlet-name>HelloWorldServlet</servlet-name>
        <servlet-class>org.example.HelloWorldServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloWorldServlet</servlet-name>
        <url-pattern>/v1/hello/world</url-pattern>
    </servlet-mapping>

</web-app>
```

And you can see screen short as below:

![image-20210903125025664](https://pengfeinie.github.io/images/image-20210903125025664.png)

Now, let's test.

![image-20210903125137871](https://pengfeinie.github.io/images/image-20210903125137871.png)

#### 7.1.2 [Servlet2.5-springmvc2.5-helloworld](https://github.com/pengfeinie/springmvc-history/tree/master/servlet2.5-springmvc2.5-helloworld)

Every Spring webapp has an associated application context that is tied to its lifecycle: the root web application context.

This is an old feature that predates Spring Web MVC, so it's not tied specifically to any web framework technology.

The context is started when the application starts, and it's destroyed when it stops, thanks to a servlet context listener. The most common types of contexts can also be refreshed at runtime, although not all *ApplicationContext* implementations have this capability.

Anyway, applications usually should not be concerned about those implementation details: the root web application context is simply a centralized place to define shared beans.

The root web application context described in the previous section is managed by a listener of class *org.springframework.web.context.ContextLoaderListener*, which is part of the *spring-web* module. 

| Spec versions:          | Servlet 2.5                                                  | Spring MVC 2.5                                               |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Main page:              | [JSR154](https://www.jcp.org/en/jsr/summary?id=154)          | [Spring MVC 2.5](https://docs.spring.io/spring-framework/docs/2.5.x/reference/mvc.html) |
| Stable:                 | Maintenance Release                                          | Final Release                                                |
| Date:                   | 11 May, 2006                                                 | Nov, 2008                                                    |
| Download Page:          | [Overview](https://jcp.org/aboutJava/communityprocess/final/jsr315/index.html) [Direct Download](https://download.oracle.com/otndocs/jcp/servlet-3.0-fr-eval-oth-JSpec/) | [Overview](https://jcp.org/aboutJava/communityprocess/final/jsr340/index.html) [Direct Download](https://mvnrepository.com/artifact/org.springframework/spring-web/2.5.6) |
| Online Javadoc:         | [Java EE 5](https://docs.oracle.com/javaee/5/api/)(2006), JDK1.6(2006) | [Spring MVC 2.5](https://docs.spring.io/spring-framework/docs/2.5.x/reference/mvc.html) |
| Minimum Tomcat version: | 6.0.0 (2007)                                                 | /                                                            |

It writes the definition of the servlet under web.xml as the following:

```
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">
    
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
        	<param-name>contextConfigLocation</param-name>
        	<param-value>classpath:dispatcherServlet-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>     
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

By default, the listener will load an XML application context from applicationContext.xml.

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
     http://www.springframework.org/schema/beans/spring-beans-2.5.xsd 
     http://www.springframework.org/schema/context 
     http://www.springframework.org/schema/context/spring-context.xsd">


   <context:component-scan base-package="org.example.service"/>

</beans>
```

And the dispatch servlet will load an XML application context from dispatchServlet-servlet.xml.

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-2.5.xsd 
    http://www.springframework.org/schema/context 
    http://www.springframework.org/schema/context/spring-context.xsd">

  <context:component-scan base-package="org.example.controller"/>

</beans>
```

We can show hello world controller as below.

```
package org.example.controller;

import org.example.service.HelloService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import javax.servlet.http.HttpServletResponse;

@Controller
public class HelloWorldController {


    @Autowired
    private HelloService helloService;
	
    @RequestMapping(value = "/v1/hello/world")
    public void hello(@RequestParam String name, HttpServletResponse response) throws Exception{
        response.getOutputStream().write(helloService.getHelloMessage(name).getBytes());
    }

}
```

And you can see screen short as below:

![image-20210909180918831](https://pengfeinie.github.io/images/image-20210909180918831.png)

Now, let's test.

![image-20210909181059729](https://pengfeinie.github.io/images/image-20210909181059729.png)

### 7.2 Servlet 3.0

Version 3 of the Servlet API has made configuration through the web.xml file completely optional. Libraries can provide their web fragments, which are pieces of XML configuration that can register listeners, filters, servlets and so on. Also, users have access to an API that allows defining programmatically every element of a servlet-based application.

#### 7.2.1 [Servlet3.0-helloworld](https://github.com/pengfeinie/springmvc-history/tree/master/servlet3.0-helloworld)

| Spec versions:          | Servlet 3.0                                                  |
| ----------------------- | ------------------------------------------------------------ |
| Main page:              | [JSR315](https://www.jcp.org/en/jsr/summary?id=315)          |
| Stable:                 | Final Release                                                |
| Date:                   | 10 Dec, 2009                                                 |
| Download Page:          | [Overview](https://jcp.org/aboutJava/communityprocess/final/jsr315/index.html) [Direct Download](https://download.oracle.com/otndocs/jcp/servlet-3.0-fr-eval-oth-JSpec/) |
| Online Javadoc:         | [Java EE 6](https://docs.oracle.com/javaee/6/api/)(2009), JDK1.6(2006) |
| Minimum Tomcat version: | 7.0.0 (2010)                                                 |

```
package org.example;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name = "helloWorldServlet",value = {"/v1/hello/world"})
public class HelloWorldServlet extends HttpServlet {
	
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
        String name = req.getParameter("name");
        resp.getOutputStream().write(name.getBytes());
    }
}
```

And you can see screen short as below:

![image-20210909182759183](https://pengfeinie.github.io/images/image-20210909182759183.png)

Now, let's test.

![image-20210903132932282](https://pengfeinie.github.io/images/image-20210903132932282.png)

#### 7.2.2 Servlet3.0+springmvc3.0

We also talked about Servlet 3.0 new interface `ServletContainerInitializer` briefly . Here we are going to give an example. Followings are the key points first.

- [ServletContainerInitializer](https://javaee.github.io/javaee-spec/javadocs/javax/servlet/ServletContainerInitializer.html) is based on Service Provider Interface (SPI) concept. 

- Implementation of `ServletContainerInitializer` interface must be configured in META-INF/services directory.

- ServletContainerInitializer has only one method:

  ```
  /**
   *
   * @since Servlet 3.0
   */
  public interface ServletContainerInitializer {
  
      public void onStartup(Set<Class<?>> c, ServletContext ctx)throws ServletException; 
  }
  ```

  `ServletContainerInitializer#onStartup` method is called by the servlet container (must be supporting at least Servlet 3.0 version). 

| Spec versions:          | Servlet 3.0                                                  | Spring MVC 3.0.0.RELEASE                                     |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Main page:              | [JSR315](https://www.jcp.org/en/jsr/summary?id=315)          | [Spring MVC 3.0](https://docs.spring.io/spring-framework/docs/3.0.x/spring-framework-reference/html/mvc.html) |
| Stable:                 | Final Release                                                | Final Release                                                |
| Date:                   | 10 Dec, 2009                                                 | Dec, 2009                                                    |
| Download Page:          | [Overview](https://jcp.org/aboutJava/communityprocess/final/jsr315/index.html) [Direct Download](https://download.oracle.com/otndocs/jcp/servlet-3.0-fr-eval-oth-JSpec/) | [Overview](https://jcp.org/aboutJava/communityprocess/final/jsr340/index.html) [Direct Download](https://mvnrepository.com/artifact/org.springframework/spring-web/3.0.0.RELEASE) |
| Online Javadoc:         | [Java EE 6](https://docs.oracle.com/javaee/6/api/)(2009), JDK1.6(2006) | [Spring MVC 3.0](https://docs.spring.io/spring-framework/docs/3.0.x/spring-framework-reference/html/mvc.html) |
| Minimum Tomcat version: | 7.0.0 (2010)                                                 | /                                                            |

##### 7.2.2.1 [Servlet3.0-springmvc3.0-helloworld1](https://github.com/pengfeinie/springmvc-history/tree/master/servlet3.0-springmvc3.0-helloworld1)

```
package org.example;

import org.springframework.web.context.ContextLoaderListener;
import org.springframework.web.servlet.DispatcherServlet;
import java.util.Set;
import javax.servlet.ServletContainerInitializer;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletRegistration;

public class MyServletContainerInitializer implements ServletContainerInitializer {

	
	public void onStartup(Set<Class<?>> c, ServletContext ctx)throws ServletException{
	    ctx.setInitParameter("contextConfigLocation", "classpath:applicationContext.xml");
            ctx.addListener(new ContextLoaderListener());        
            DispatcherServlet d = new DispatcherServlet();
            d.setContextConfigLocation("classpath:dispatcherServlet-servlet.xml");
            ServletRegistration.Dynamic re = ctx.addServlet("dispatcherServlet", d);
            re.setLoadOnStartup(1);
            re.addMapping("/");
	}
}
```

And in META-INF/services, you should add a file which named javax.servlet.ServletContainerInitializer, you should put org.example.MyServletContainerInitializer in it.

![image-20210909143814027](https://pengfeinie.github.io/images/image-20210909143814027.png)

If you deploy your app to tomcat, you will see the screen as below:

![image-20210909144719169](https://pengfeinie.github.io/images/image-20210909144719169.png)

![image-20210909144143687](https://pengfeinie.github.io/images/image-20210909144143687.png)

![image-20210909150032612](https://pengfeinie.github.io/images/image-20210909150032612.png)

##### 7.2.2.2 [Servlet3.0-springmvc3.0-helloworld2](https://github.com/pengfeinie/springmvc-history/tree/master/servlet3.0-springmvc3.0-helloworld2)

```
package org.example;

import org.springframework.core.annotation.AnnotationAwareOrderComparator;
import java.lang.reflect.Modifier;
import java.util.Collections;
import java.util.LinkedList;
import java.util.List;
import java.util.Set;
import javax.servlet.ServletContainerInitializer;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.HandlesTypes;

@HandlesTypes(value= {NpfWebApplicationInitializer.class})
public class NpfSpringServletContainerInitializer implements ServletContainerInitializer {

	
	public void onStartup(Set<Class<?>> initializer, ServletContext servletContext)
	throws ServletException{
	 List<NpfWebApplicationInitializer> initializers = new LinkedList<NpfWebApplicationInitializer>();
	 if (initializer != null) {
	  for (Class<?> waiClass : initializer) {
		if (!waiClass.isInterface() && !Modifier.isAbstract(waiClass.getModifiers()) 
		  && NpfWebApplicationInitializer.class.isAssignableFrom(waiClass)) {
		  try {
			initializers.add((NpfWebApplicationInitializer) waiClass.newInstance());
		  }catch (Throwable ex) {
			throw new ServletException("Failed to instantiate class", ex);
		  }
		}
	   }
	  }
	  if (initializers.isEmpty()) {
		servletContext.log("No NpfWebApplicationInitializer types detected on classpath");
		return;
	  }
	  Collections.sort(initializers, new AnnotationAwareOrderComparator());
	  for (NpfWebApplicationInitializer item : initializers) {
		item.onStartup(servletContext);
	  }
	}
}
```

```
package org.example;

import javax.servlet.ServletContext;

public interface NpfWebApplicationInitializer {
	
	public void onStartup(ServletContext ctx);
}
```

```
package org.example;

import javax.servlet.ServletContext;
import javax.servlet.ServletRegistration;
import org.springframework.web.context.ContextLoaderListener;
import org.springframework.web.servlet.DispatcherServlet;

public class MyNpfWebApplicationInitializer implements NpfWebApplicationInitializer{

	public void onStartup(ServletContext ctx) {
	    ctx.setInitParameter("contextConfigLocation", "classpath:applicationContext.xml");
            ctx.addListener(new ContextLoaderListener());
            DispatcherServlet d = new DispatcherServlet();
            d.setContextConfigLocation("classpath:dispatcherServlet-servlet.xml");
            ServletRegistration.Dynamic r = ctx.addServlet("dispatcherServlet", d);
            r.setLoadOnStartup(1);
            r.addMapping("/");
	}
}
```

![image-20210909152817674](https://pengfeinie.github.io/images/image-20210909152817674.png)

![image-20210909152924856](https://pengfeinie.github.io/images/image-20210909152924856.png)

![image-20210909153033310](https://pengfeinie.github.io/images/image-20210909153033310.png)

#### 7.2.3 Servlet3.0+springmvc3.1

The *spring-web* module makes use of these features and offers its API to register components of the application when it starts.

Spring scans the application's classpath for instances of the *org.springframework.web.WebApplicationInitializer* class. This is an interface with a single method, *void onStartup(ServletContext servletContext) throws ServletException*, that's invoked upon application startup.

Let's now look at how we can use this facility to create the same types of root web application contexts that we've seen earlier.

[https://github.com/spring-projects/spring-framework/tree/3.1.x/org.springframework.web/src/main/resources/META-INF/services](https://github.com/spring-projects/spring-framework/tree/3.1.x/org.springframework.web/src/main/resources/META-INF/services)

[https://github.com/spring-projects/spring-framework/commit/c9e67b2ef25ff1f5de32e4fc75a36f076fd14f35](https://github.com/spring-projects/spring-framework/commit/c9e67b2ef25ff1f5de32e4fc75a36f076fd14f35)

![image-20210913134424120](https://pengfeinie.github.io/images/image-20210913134424120.png)

| Spec versions:          | Servlet 3.0                                                  | Spring MVC 3.1.0.RELEASE                                     |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Main page:              | [JSR315](https://www.jcp.org/en/jsr/summary?id=315)          | [Spring MVC 3.1](https://docs.spring.io/spring-framework/docs/3.1.x/spring-framework-reference/html/mvc.html) |
| Stable:                 | Final Release                                                | Final Release                                                |
| Date:                   | 10 Dec, 2009                                                 | Dec, 2011                                                    |
| Download Page:          | [Overview](https://jcp.org/aboutJava/communityprocess/final/jsr315/index.html) [Direct Download](https://download.oracle.com/otndocs/jcp/servlet-3.0-fr-eval-oth-JSpec/) | [Overview](https://jcp.org/aboutJava/communityprocess/final/jsr340/index.html) [Direct Download](https://mvnrepository.com/artifact/org.springframework/spring-web/3.1.0.RELEASE) |
| Online Javadoc:         | [Java EE 6](https://docs.oracle.com/javaee/6/api/)(2009), JDK1.6(2006) | [Spring MVC 3.1](https://docs.spring.io/spring-framework/docs/3.1.x/spring-framework-reference/html/mvc.html) |
| Minimum Tomcat version: | 7.0.0 (2010)                                                 | /                                                            |

##### 7.2.3.1 [Servlet3.0-springmvc3.1-helloworld0](https://github.com/pengfeinie/springmvc-history/tree/master/servlet3.0-springmvc3.1-helloworld0)

![image-20210909184335118](https://pengfeinie.github.io/images/image-20210909184335118.png)

So we can see there is a bug that tomcat 7 cannot load third party jar meta-info services. This bug is fixed in  tomcat 8. 

##### 7.2.3.2 [Servlet3.0-springmvc3.1-helloworld1](https://github.com/pengfeinie/springmvc-history/tree/master/servlet3.0-springmvc3.1-helloworld1)

![image-20210910124314829](https://pengfeinie.github.io/images/image-20210910124314829.png)

##### 7.2.3.3 [Servlet3.0-springmvc3.1-helloworld1_1](https://github.com/pengfeinie/springmvc-history/tree/master/servlet3.0-springmvc3.1-helloworld1_1)

```
package org.example;

import org.springframework.web.WebApplicationInitializer;
import org.springframework.web.servlet.DispatcherServlet;
import javax.servlet.ServletContext;
import javax.servlet.ServletRegistration;

public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) {
        DispatcherServlet d = new DispatcherServlet();
        d.setContextConfigLocation("classpath:dispatcherServlet-servlet.xml");
        ServletRegistration.Dynamic r = servletContext.addServlet("dispatcherServlet", d);
        r.setLoadOnStartup(1);
        r.addMapping("/");
    }
}
```

##### 7.2.3.4 [Servlet3.0-springmvc3.1-helloworld1_2](https://github.com/pengfeinie/springmvc-history/tree/master/servlet3.0-springmvc3.1-helloworld1_2)

```
package org.example;

import org.springframework.web.WebApplicationInitializer;
import org.springframework.web.context.support.XmlWebApplicationContext;
import org.springframework.web.servlet.DispatcherServlet;
import javax.servlet.ServletContext;
import javax.servlet.ServletRegistration;

public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) {
        XmlWebApplicationContext xmlApplicationContext = new XmlWebApplicationContext();
        xmlApplicationContext.setConfigLocation("classpath:applicationContext.xml");
        
        DispatcherServlet d = new DispatcherServlet(xmlApplicationContext);
        ServletRegistration.Dynamic r = servletContext.addServlet("dispatcherServlet", d);
        r.setLoadOnStartup(1);
        r.addMapping("/");
    }
}
```

##### 7.2.3.5 [Servlet3.0-springmvc3.1-helloworld2](https://github.com/pengfeinie/springmvc-history/tree/master/servlet3.0-springmvc3.1-helloworld2)

```
package org.example;

import org.springframework.web.WebApplicationInitializer;
import org.springframework.web.context.ContextLoaderListener;
import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;
import org.springframework.web.servlet.DispatcherServlet;
import javax.servlet.ServletContext;
import javax.servlet.ServletRegistration;

public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) {
    	AnnotationConfigWebApplicationContext ac = new AnnotationConfigWebApplicationContext();
    	ac.register(AppConfig.class);
    	servletContext.addListener(new ContextLoaderListener(ac));
    	
        DispatcherServlet d = new DispatcherServlet();
        d.setContextClass(AnnotationConfigWebApplicationContext.class);
        d.setContextConfigLocation(AppMvcConfig.class.getCanonicalName());
        ServletRegistration.Dynamic r = servletContext.addServlet("dispatcherServlet", d);
        r.setLoadOnStartup(1);
        r.addMapping("/");
    }
}
```

##### 7.2.3.6 [Servlet3.0-springmvc3.1-helloworld2_1](https://github.com/pengfeinie/springmvc-history/tree/master/servlet3.0-springmvc3.1-helloworld2_1)

```
package org.example;

import org.springframework.web.WebApplicationInitializer;
import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;
import org.springframework.web.servlet.DispatcherServlet;
import javax.servlet.ServletContext;
import javax.servlet.ServletRegistration;

public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) {
        DispatcherServlet d = new DispatcherServlet();
        d.setContextClass(AnnotationConfigWebApplicationContext.class);
        d.setContextConfigLocation(AppMvcConfig.class.getCanonicalName());
        ServletRegistration.Dynamic r = servletContext.addServlet("dispatcherServlet", d);
        r.setLoadOnStartup(1);
        r.addMapping("/");
    }
}
```

##### 7.2.3.7 [Servlet3.0-springmvc3.1-helloworld2_2](https://github.com/pengfeinie/springmvc-history/tree/master/servlet3.0-springmvc3.1-helloworld2_2)

```
package org.example;

import org.springframework.web.WebApplicationInitializer;
import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;
import org.springframework.web.servlet.DispatcherServlet;
import javax.servlet.ServletContext;
import javax.servlet.ServletRegistration;

public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) {
    	AnnotationConfigWebApplicationContext ac = new AnnotationConfigWebApplicationContext();
    	ac.register(AppConfig.class);
    	
        DispatcherServlet d = new DispatcherServlet(ac);
        ServletRegistration.Dynamic r = servletContext.addServlet("dispatcherServlet", d);
        r.setLoadOnStartup(1);
        r.addMapping("/");
    }
}
```

##### 7.2.3.8 [Servlet3.0-springmvc3.1-helloworld3](https://github.com/pengfeinie/springmvc-history/tree/master/servlet3.0-springmvc3.1-helloworld3)

```
package org.example;

import org.apache.catalina.Context;
import org.apache.catalina.Engine;
import org.apache.catalina.Host;
import org.apache.catalina.LifecycleException;
import org.apache.catalina.Server;
import org.apache.catalina.Service;
import org.apache.catalina.Wrapper;
import org.apache.catalina.connector.Connector;
import org.apache.catalina.core.StandardContext;
import org.apache.catalina.core.StandardEngine;
import org.apache.catalina.core.StandardHost;
import org.apache.catalina.startup.Tomcat;
import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;
import org.springframework.web.servlet.DispatcherServlet;

public class App {
	
	public static void main(String[] args) throws LifecycleException {
          Tomcat tomcat = new Tomcat();

          Server server = tomcat.getServer();
          Service service = server.findService("Tomcat");

          Connector connector = new Connector();
          connector.setPort(9090);

          Engine engine = new StandardEngine();
          engine.setDefaultHost("localhost");

          Host host = new StandardHost();
          host.setName("localhost");

          String contextPath = "";
          Context context = new StandardContext();
          context.setPath(contextPath);
          context.addLifecycleListener(new Tomcat.FixContextListener());

          host.addChild(context);
          engine.addChild(host);

          service.setContainer(engine);
          service.addConnector(connector);
          AnnotationConfigWebApplicationContext ac = new AnnotationConfigWebApplicationContext();
          ac.register(AppConfig.class);
          DispatcherServlet servlet = new DispatcherServlet(ac);
          Wrapper wrapper = tomcat.addServlet(contextPath,"app", servlet);
          wrapper.setLoadOnStartup(1);
          wrapper.addMapping("/");
          tomcat.start();
          tomcat.getServer().await();
	}
}
```

