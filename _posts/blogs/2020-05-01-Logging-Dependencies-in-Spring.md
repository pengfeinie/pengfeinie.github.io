---
title: 'Logging Dependencies in Spring'
date: 2020-05-01
tags:
  - Spring
  - Logging
---

This article deals with the choices that Spring makes and the options that developers have for logging in applications built with Spring. This is timed to coincide with the imminent release of Spring 3.0 not because we have changed anything much (although we are being more careful with dependency metadata now), but so that you can make an informed decision about how to implement and configure logging in your application. First we look briefly at what the mandatory dependencies are in Spring, and then go on to discuss in more detail how to set your application up to use some examples of common logging libraries. As an example I’ll show the dependency configuration using Maven Central style artifact naming conventions.

### The Java Logging Ecosystem

Log4j was introduced in 1999 and was one of the first widely adopted logging frameworks. A few years later in 2002, Sun introduced an alternative framework, java.util.logging (commonly referred to by the acronym “JUL”), which is distributed with the Java platform. In reaction, the Apache Commons Logging project was created with the intention of providing a unified logging API that could be used to interact with whichever backing logging implementation that the user preferred, including Log4j and JUL (among others.) However, yet another alternative “unified” logging API emerged in 2005 from the author of Log4j — SLF4J, the “Simple Logging Facade for Java.” Later, the author of Log4j and SLF4J released logback, which provided a native implementation of the SLF4J interface. Around the same time, development also began on the (now stable) Log4j 2 — without the author of the first version. Simple, right?

![image-20210618135636962](..\images\image-20210618135636962.png)

![image-20210618105800255](..\images\image-20210618105800255.png)

[https://en.wikipedia.org/wiki/Java_version_history](https://en.wikipedia.org/wiki/Java_version_history)

![image-20210618111210755](..\images\image-20210618111210755.png)

There was a lot of code that was written at different points in time during this history and like so much software there was — and still is — lot of debates on which framework is the best choice.

[https://commons.apache.org/](https://commons.apache.org/)

[https://commons.apache.org/proper/commons-logging/](https://commons.apache.org/proper/commons-logging/)

## Bridging legacy APIs

Often, some of the components you depend on rely on a logging API other than SLF4J. You may also assume that these components will not switch to SLF4J in the immediate future. To deal with such circumstances, SLF4J ships with several bridging modules which redirect calls made to log4j, JCL and java.util.logging APIs to behave as if they were made to the SLF4J API instead. The figure below illustrates the idea.

![image-20210618154345854](..\images\image-20210618154345854.png)



![image-20210618155418422](..\images\image-20210618155418422.png)

![image-20210618155552509](..\images\image-20210618155552509.png)

## Gradual migration to SLF4J from JCL

#### *jcl-over-slf4j.jar*

To ease migration to SLF4J from JCL, SLF4J distributions include the jar file *jcl-over-slf4j.jar*. This jar file is intended as a drop-in replacement for JCL version 1.1.1. It implements the public API of JCL but using SLF4J underneath, hence the name "JCL over SLF4J."

Our JCL over SLF4J implementation will allow you to migrate to SLF4J gradually, especially if some of the libraries your software depends on continue to use JCL for the foreseeable future. You can immediately enjoy the benefits of SLF4J's reliability and preserve backward compatibility at the same time. Just replace *commons-logging.jar* with *jcl-over-slf4j.jar*. Subsequently, the selection of the underlying logging framework will be done by SLF4J instead of JCL [but without the class loader headaches plaguing JCL](http://articles.qos.ch/classloader.html). The underlying logging framework can be any of the frameworks supported by SLF4J. Often times, replacing *commons-logging.jar* with *jcl-over-slf4j.jar* will immediately and permanently solve class loader issues related to commons logging.

### *slf4j-jcl.jar*

Some of our users after having switched to SLF4J API realize that in some contexts the use of JCL is mandatory and their use of SLF4J can be a problem. For this uncommon but important case, SLF4J offers a JCL binding, found in the file *slf4j-jcl.jar*. The JCL binding will delegate all logging calls made through SLF4J API to JCL. Thus, if for some reason an existing application *must* use JCL, your part of that application can still code against the SLF4J API in a manner transparent to the larger application environment. Your choice of SLF4J API will be invisible to the rest of the application which can continue to use JCL.

### *jcl-over-slf4j.jar* should not be confused with *slf4j-jcl.jar*

JCL-over-SLF4J, i.e. *jcl-over-slf4j.jar*, comes in handy in situations where JCL needs to be supported for backward compatibility reasons. It can be used to fix problems associated with JCL, without necessarily adopting the SLF4J API, a decision which can be deferred to a later time.

On the other hand, *slf4j-jcl.jar* is useful **after** you have already adopted the SLF4J API for your component which needs to be embedded in a larger application environment where JCL is a formal requirement. Your software component can still use SLF4J API without disrupting the larger application. Indeed, *slf4j-jcl.jar* will delegate all logging decisions to JCL so that the dependency on SLF4J API by your component will be transparent to the larger whole.

Please note that *jcl-over-slf4j.jar* and *slf4j-jcl.jar* cannot be deployed at the same time. The former jar file will cause JCL to delegate the choice of the logging system to SLF4J and the latter jar file will cause SLF4J to delegate the choice of the logging system to JCL, resulting in an [infinite loop](http://www.slf4j.org/codes.html#jclDelegationLoop).

## log4j-over-slf4j

SLF4J ship with a module called *log4j-over-slf4j*. It allows log4j users to migrate existing applications to SLF4J without changing *a single line of code* but simply by replacing the *log4j.jar* file with *log4j-over-slf4j.jar*, as described below.

#### How does it work?

The log4j-over-slf4j module contains replacements of most widely used log4j classes, namely `org.apache.log4j.Category`, `org.apache.log4j.Logger`, `org.apache.log4j.Priority`, `org.apache.log4j.Level`, `org.apache.log4j.MDC`, and `org.apache.log4j.BasicConfigurator`. These replacement classes redirect all work to their corresponding SLF4J classes.

To use log4j-over-slf4j in your own application, the first step is to locate and then to replace *log4j.jar* with *log4j-over-slf4j.jar*. Note that you still need an SLF4J binding and its dependencies for log4j-over-slf4j to work properly.

In most situations, replacing a jar file is all it takes in order to migrate from log4j to SLF4J.

Note that as a result of this migration, log4j configuration files will no longer be picked up. If you need to migrate your log4j.properties file to logback, the [log4j translator](http://logback.qos.ch/translator/) might be of help. For configuring logback, please refer to [its manual](http://logback.qos.ch/manual/index.html).

#### When does it not work?

The *log4j-over-slf4j* module will not work when the application calls log4j components that are not present in the bridge. For example, when application code directly references log4j appenders, filters or the PropertyConfigurator, then log4j-over-slf4j would be an insufficient replacement for log4j. However, when log4j is configured through a configuration file, be it *log4j.properties* or *log4j.xml*, the log4j-over-slf4j module should just work fine.

#### What about the overhead?

There overhead of using log4j-over-slf4j instead of log4j directly is relatively small. Given that log4j-over-slf4j immediately delegates all work to SLF4J, the CPU overhead should be negligible, in the order of a few *nanoseconds*. There is a memory overhead corresponding to an entry in a hashmap per logger, which should be usually acceptable even for very large applications consisting of several thousand loggers. Moreover, if you choose logback as your underlying logging system, and given that logback is both much faster and more memory-efficient than log4j, the gains made by using logback should compensate for the overhead of using log4j-over-slf4j instead of log4j directly.

#### log4j-over-slf4j.jar and slf4j-log4j12.jar cannot be present simultaneously

The presence of *slf4j-log4j12.jar*, that is the log4j binding for SLF4J, will force all SLF4J calls to be delegated to log4j. The presence of *log4j-over-slf4j.jar* will in turn delegate all log4j API calls to their SLF4J equivalents. If both are present simultaneously, slf4j calls will be delegated to log4j, and log4j calls redirected to SLF4j, resulting in an [endless loop](http://www.slf4j.org/codes.html#log4jDelegationLoop).

## jul-to-slf4j bridge

The *jul-to-slf4j.jar* artifact includes a java.util.logging (jul) handler, namely `SLF4JBridgeHandler`, which routes all incoming jul records to the SLF4j API. Please see [SLF4JBridgeHandler javadocs](http://www.slf4j.org/api/org/slf4j/bridge/SLF4JBridgeHandler.html) for usage instructions.

**NOTE ON PERFORMANCE** Contrary to other bridging modules, namely jcl-over-slf4j and log4j-over-slf4j, which reimplement JCL and respectively log4j, the jul-to-slf4j module does not reimplement the java.util.logging because packages under the java.* namespace cannot be replaced. Instead, jul-to-slf4j translates [LogRecord](http://java.sun.com/j2se/1.5.0/docs/api/java/util/logging/LogRecord.html?is-external=true) objects into their SLF4J equivalent. Please note this translation process incurs the cost of constructing a `LogRecord` instance regardless of whether the SLF4J logger is disabled for the given level or nor. **Consequently, j.u.l. to SLF4J translation can seriously increase the cost of disabled logging statements (60 fold or 6000%) and measurably impact the performance of enabled log statements (20% overall increase).** As of logback version 0.9.25, it is possible to completely eliminate the 60 fold translation overhead for disabled log statements with the help of [LevelChangePropagator](http://logback.qos.ch/manual/configuration.html#LevelChangePropagator).

If you are concerned about application performance, then use of `SLF4JBridgeHandler` is appropriate only if any one of the following two conditions is true:

1. few j.u.l. logging statements are in play
2. `LevelChangePropagator` has been installed

#### jul-to-slf4j.jar and slf4j-jdk14.jar cannot be present simultaneously

The presence of slf4j-jdk14.jar, that is the jul binding for SLF4J, will force SLF4J calls to be delegated to jul. On the other hand, the presence of jul-to-slf4j.jar, plus the installation of SLF4JBridgeHandler, by invoking "SLF4JBridgeHandler.install()" will route jul records to SLF4J. Thus, if both jar are present simultaneously (and SLF4JBridgeHandler is installed), slf4j calls will be delegated to jul and jul records will be routed to SLF4J, resulting in an endless loop.

[http://www.slf4j.org/legacy.html](http://www.slf4j.org/legacy.html)

## Spring Dependencies and Depending on Spring

Although Spring provides integration and support for a huge range of enterprise and other external tools, it intentionally keeps its mandatory dependencies to an absolute minimum: you shouldn’t have to locate and download (even automatically) a large number of jar libraries in order to use Spring for simple use cases. For basic dependency injection there is only one mandatory external dependency,and that is for logging (see below for a more detailed description of logging options). If you are using Maven for dependency management you don’t even need to supply the logging dependency explicitly. For example, to create an application context and use dependency injection to configure an application, your Maven dependencies will look like this:

![image-20210618150752486](..\images\image-20210618150752486.png)

## Logging

Logging is a very important dependency for Spring because **a)** it is the only mandatory external dependency, **b)** everyone likes to see some output from the tools they are using, and **c)** Spring integrates with lots of other tools all of which have also made a choice of logging dependency. One of the goals of an application developer is often to have unified logging configured in a central place for the whole application, including all external components. This is more difficult than it might have been since there are so many choices of logging framework.

The mandatory logging dependency in Spring is the Jakarta Commons Logging API (JCL). We compile against JCL and we also make JCL Log objects visible for classes that extend the Spring Framework. It’s important to users that all versions of Spring use the same logging library: migration is easy because backwards compatibility is preserved even with applications that extend Spring.The way we do this is to make one of the modules in Spring depend explicitly on commons-logging (the canonical implementation of JCL), and then make all the other modules depend on that at compile time. If you are using Maven for example, and wondering where you picked up the dependency on commons-logging, then it is from Spring and specifically from the central module called spring-core.

The nice thing about commons-logging is that you don’t need anything else to make your application work. It has a runtime discovery algorithm that looks for other logging frameworks in well known places on the classpath and uses one that it thinks is appropriate (or you can tell it which one if you need to). If nothing else is available you get pretty nice looking logs just from the JDK (java.util.logging or JUL for short). You should find that your Spring application works and logs happily to the console out of the box in most situations, and that’s important.

Unfortunately, the worst thing about commons-logging, and what has made it unpopular with new tools, is also the runtime discovery algorithm. If we could **turn back the clock** and start Spring now as a new project it would use a different logging dependency. Probably the first choice would be the Simple Logging Facade for Java ([SLF4J](http://www.slf4j.org/)), which is also used by a lot of other tools that people use with Spring inside their applications.

To switch off commons-logging is easy: just make sure it isn’t on the classpath at runtime. In Maven terms you exclude the dependency, and because of the way that the Spring dependencies are declared, you only have to do that once.

![image-20210618153234335](..\images\image-20210618153234335.png)

Now this application is probably broken because there is no implementation of the JCL API on the classpath, so to fix it a new one has to be provided. In the next section we show you how to provide an alternative implementation of JCL using SLF4J as an example.

### Using SLF4J

SLF4J is a cleaner dependency and more efficient at runtime than commons-logging because it uses compile-time bindings instead of runtime discovery of the other logging frameworks it integrates. This also means that you have to be more explicit about what you want to happen at runtime, and declare it or configure it accordingly. SLF4J provides bindings to many common logging frameworks, so you can usually choose one that you already use, and bind to that for configuration and management.

SLF4J provides bindings to many common logging frameworks,including JCL, and it also does the reverse: bridges between other logging frameworks and itself. So to use SLF4J with Spring you need to replace the commons-logging dependency with the SLF4J-JCL bridge. Once you have done that then logging calls from within Spring will be translated into logging calls to the SLF4J API, so if other libraries in your application use that API, then you have a single place to configure and manage logging.

A common choice might be to bridge Spring to SLF4J, and then provide explicit binding from SLF4J to Log4J. You need to supply 4 dependencies (and exclude the existing commons-logging): the bridge, theSLF4J API, the binding to Log4J, and the Log4J implementation itself. In Maven you would do that like this.

![image-20210618164500046](..\images\image-20210618164500046.png)

That might seem like a lot of dependencies just to get some logging. Well it is, but it is optional, and it should behave better than the vanilla commons-logging with respect to class loader issues, notably if you are in a strict container like an OSGi platform. Allegedly there is also a performance benefit because the bindings are at compile-time not runtime.

A more common choice amongst SLF4J users, which uses fewer step sand generates fewer dependencies, is to bind directly to [Logback](http://logback.qos.ch/). This removes the extra binding step because Logback implements SLF4J directly, so you only need to depend on two libaries not four (jcl-over-slf4j and logback). If you do that you might also need to exclude the slf4j-api dependency from other external dependencies (not Spring), because you only want one version of that API on the classpath.

### Using Log4J

Many people use <ahref="[http://logging.apache.org/log4j"](https://logging.apache.org/log4j)Log4j as a logging framework for configuration and management purposes. It’s efficient and well established, and in fact it’s what we use at runtime when we build and test Spring. Spring also provides some utilities for configuring and initializing Log4j, so it have an optional compile time dependency on Log4j in some modules.

To make Log4j work with the default JCL dependency (commons-logging) all you need to do is put Log4j on the classpath, and provide it with a configuration file(log4j.properties or log4j.xml in the root of the classpath). So for Maven users this is your dependency declaration:

![image-20210618165251438](..\images\image-20210618165251438.png)

And here’s a sample log4j.properties for logging to the console:

```
log4j.rootCategory=INFO, stdout

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %t %c{2}:%L - %m%n

log4j.category.org.springframework.beans.factory=DEBUG
```

[https://spring.io/blog/2009/12/04/logging-dependencies-in-spring](https://spring.io/blog/2009/12/04/logging-dependencies-in-spring)

[http://www.slf4j.org/manual.html#swapping](http://www.slf4j.org/manual.html#swapping)
