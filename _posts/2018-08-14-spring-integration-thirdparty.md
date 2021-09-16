---
title: 'thinking of spring integration thirdparty'
date: 2018-08-14
tags:
  - spring
---

## 1. Configuration Metadata

Configuration metadata represents how you, as an application developer, tell the Spring Container to instantiate, configure, and assemble the objects in your application.

This configuration metadata is nothing but bean definitions. `BeanDefinitions` are provided in the form of an XML file, annotations, or a Java config class.

Forms of metadata with the Spring container are: **XML-based configuration, annotation-based configuration, and Java-based configuration.**

The following diagram shows a high-level view of how Spring works. Your application classes are combined with configuration metadata so that, after the `ApplicationContext` is created and initialized, you have a fully configured and executable system or application.

![image-20210915125903022](https://docs.spring.io/spring-framework/docs/5.2.x/spring-framework-reference/images/container-magic.png)

As the preceding diagram shows, the Spring IoC container consumes a form of configuration metadata. This configuration metadata represents how you, as an application developer, tell the Spring container to instantiate, configure, and assemble the objects in your application.

![https://pengfeinie.github.io/images/jg555.png](https://pengfeinie.github.io/images/jg555.png)

### 1.1 XML-based configuration

XML-based configuration metadata configures these beans as `<bean/>` elements inside a top-level `<beans/>` element.

![https://docs.spring.io/spring-framework/docs/3.0.0.M3/reference/html/images/eclipse-setup-2.png](https://docs.spring.io/spring-framework/docs/3.0.0.M3/reference/html/images/eclipse-setup-2.png)

### 1.2 Annotation-based configuration

Spring 2.5 introduced support for annotation-based configuration metadata. An alternative to XML setup is provided by annotation-based configuration, which relies on the bytecode metadata for wiring up components instead of angle-bracket declarations. Instead of using XML to describe a bean wiring, the developer moves the configuration into the component class itself by using annotations on the relevant class, method, or field declaration.

![https://pengfeinie.github.io/images/Slide1.JPG](https://pengfeinie.github.io/images/Slide1.JPG)

### 1.3 Java-based configuration

Java configuration typically uses `@Bean`-annotated methods within a `@Configuration` class. The central artifacts in Spring’s new Java-configuration support are `@Configuration`-annotated classes and `@Bean`-annotated methods.

The `@Bean` annotation is used to indicate that a method instantiates, configures, and initializes a new object to be managed by the Spring IoC container. For those familiar with Spring’s `<beans/>` XML configuration, the `@Bean` annotation plays the same role as the `<bean/>` element. You can use `@Bean`-annotated methods with any Spring `@Component`. However, they are most often used with `@Configuration` beans.

Annotating a class with `@Configuration` indicates that its primary purpose is as a source of bean definitions. Furthermore, `@Configuration` classes let inter-bean dependencies be defined by calling other `@Bean` methods in the same class. 

![image-20210915125617325](https://pengfeinie.github.io/images/image-20210915125617325.png)

**@Configuration Classes are Subclassed by CGLIB**

All @Configuration classes are subclassed at startup-time with CGLIB. In the subclass, the child method checks the container first for any cached (scoped) beans before it calls the parent method and creates a new instance.

 CGLIB proxying is the means by which invoking methods or fields within @Bean methods in @Configuration classes creates bean metadata references to collaborating objects; such methods are not invoked with normal Java semantics but rather go through the container in order to provide the usual lifecycle management and proxying of Spring beans even when referring to other beans via programmatic calls to @Bean methods.

That's why all methods will return the same instance at multiple calls (if they are singleton scoped which is the default scope).

There has to be `@Configuration` annotation, otherwise this runtime manipulation won't be done.

## 2. Container extension points

### 2.1 Customizing beans using `BeanPostProcessor`

[Spring 1.1.x](https://docs.spring.io/spring-framework/docs/1.1.x/reference/beans.html#beans-factory-customizing) The `BeanPostProcessor` interface defines callback methods that you can implement to provide your own (or override the container’s default) instantiation logic, dependency resolution logic, and so forth. If you want to implement some custom logic after the Spring container finishes instantiating, configuring, and initializing a bean, you can plug in one or more custom `BeanPostProcessor` implementations.

You can configure multiple `BeanPostProcessor` instances, and you can control the order in which these `BeanPostProcessor` instances run by setting the `order` property. You can set this property only if the `BeanPostProcessor` implements the `Ordered` interface. If you write your own `BeanPostProcessor`, you should consider implementing the `Ordered` interface, too. For further details, see the javadoc of the [`BeanPostProcessor`](https://docs.spring.io/spring-framework/docs/5.2.16.RELEASE/javadoc-api/org/springframework/beans/factory/config/BeanPostProcessor.html) and [`Ordered`](https://docs.spring.io/spring-framework/docs/5.2.16.RELEASE/javadoc-api/org/springframework/core/Ordered.html) interfaces. 

![https://pengfeinie.github.io/images/beanpostprocessor-overview.jpg](https://pengfeinie.github.io/images/beanpostprocessor-overview.jpg)

#### 2.1.1 How to create BeanPostProcessor

To create a bean post processor in spring: implement the `BeanPostProcessor` interface and implement the callback methods.

```
@Component
public class GreetingBeanPostProcessor implements BeanPostProcessor {

    @Nullable
    @Override
    public Object postProcessBeforeInitialization(Object bean,String name) throws BeansException {
        if (bean instanceof GreetingService) {
            System.out.printf("BeforeInitialization");
        }
        return bean;
    }

    @Nullable
    @Override
    public Object postProcessAfterInitialization(Object bean,String name) throws BeansException {
        if (bean instanceof GreetingService) {
            System.out.printf("AfterInitialization");
        }
        return bean;
    }
}
```

#### 2.1.2 How to register BeanPostProcessor

An *ApplicationContext* automatically detects any beans that are defined in the configuration metadata which implement the *BeanPostProcessor* interface. It registers these beans as post-processors so that they can be called later upon bean creation.

Then Spring will pass each bean instance to these two methods before and after calling the initialization callback method where you can process the bean instance the way you like.

### 2.2 Customizing configuration metadata with `BeanFactoryPostProcessor`

[Spring 1.1.x](https://docs.spring.io/spring-framework/docs/1.1.x/reference/beans.html#beans-factory-customizing)  The next extension point that we look at is the `org.springframework.beans.factory.config.BeanFactoryPostProcessor`. The semantics of this interface are similar to those of the `BeanPostProcessor`, with one major difference: `BeanFactoryPostProcessor` operates on the bean configuration metadata. That is, the Spring IoC container lets a `BeanFactoryPostProcessor` read the configuration metadata and potentially change it *before* the container instantiates any beans other than `BeanFactoryPostProcessor` instances.

You can configure multiple `BeanFactoryPostProcessor` instances, and you can control the order in which these `BeanFactoryPostProcessor` instances run by setting the `order` property. However, you can only set this property if the `BeanFactoryPostProcessor` implements the `Ordered` interface. If you write your own `BeanFactoryPostProcessor`, you should consider implementing the `Ordered` interface, too. See the javadoc of the [`BeanFactoryPostProcessor`](https://docs.spring.io/spring-framework/docs/5.2.16.RELEASE/javadoc-api/org/springframework/beans/factory/config/BeanFactoryPostProcessor.html) and [`Ordered`](https://docs.spring.io/spring-framework/docs/5.2.16.RELEASE/javadoc-api/org/springframework/core/Ordered.html) interfaces for more details.

[MapperScannerConfigurer](https://github.com/mybatis/spring/blob/1.2.x/src/main/java/org/mybatis/spring/mapper/MapperScannerConfigurer.java)

### 2.3 Customizing instantiation logic using `FactoryBean`

[Spring 1.1.x](https://docs.spring.io/spring-framework/docs/1.1.x/reference/beans.html#beans-factory-customizing)  You can implement the `org.springframework.beans.factory.FactoryBean` interface for objects that are themselves factories.

The `FactoryBean` interface is a point of pluggability into the Spring IoC container’s instantiation logic. If you have complex initialization code that is better expressed in Java as opposed to a (potentially) verbose amount of XML, you can create your own `FactoryBean`, write the complex initialization inside that class, and then plug your custom `FactoryBean` into the container. 

The `FactoryBean` concept and interface is used in a number of places within the Spring Framework. When you need to ask a container for an actual `FactoryBean` instance itself instead of the bean it produces, preface the bean’s `id` with the ampersand symbol (`&`) when calling the `getBean()` method of the `ApplicationContext`. So, for a given `FactoryBean` with an `id` of `myBean`, invoking `getBean("myBean")` on the container returns the product of the `FactoryBean`, whereas invoking `getBean("&myBean")` returns the `FactoryBean` instance itself.

```
  @Bean
  public MapperFactoryBean<UserMapper> userMapper() throws Exception {
	 MapperFactoryBean<UserMapper> factoryBean = new MapperFactoryBean<UserMapper>();
	 factoryBean.setMapperInterface(UserMapper.class);
	 factoryBean.setSqlSessionFactory(sqlSessionFactory().getObject());
	 return factoryBean;
  }
```

### 2.4 @Configuration+@Bean

The central artifacts in Spring’s new Java-configuration support are `@Configuration`-annotated classes and `@Bean`-annotated methods.

The `@Bean` annotation is used to indicate that a method instantiates, configures, and initializes a new object to be managed by the Spring IoC container. For those familiar with Spring’s `<beans/>` XML configuration, the `@Bean` annotation plays the same role as the `<bean/>` element. You can use `@Bean`-annotated methods with any Spring `@Component`. However, they are most often used with `@Configuration` beans.

Annotating a class with `@Configuration` indicates that its primary purpose is as a source of bean definitions. Furthermore, `@Configuration` classes let inter-bean dependencies be defined by calling other `@Bean` methods in the same class.

```
@Configuration
public class AppConfig {
	
	  @Bean
	  public MapperFactoryBean<UserMapper> userMapper() throws Exception {
	    MapperFactoryBean<UserMapper> factoryBean = new MapperFactoryBean<UserMapper>();
	    factoryBean.setMapperInterface(UserMapper.class);
	    factoryBean.setSqlSessionFactory(sqlSessionFactory().getObject());
	    return factoryBean;
	  }
}
```

### 2.5 @Configuration+@Import+ImportSelector

Spring’s Java-based configuration feature lets you compose annotations, which can reduce the complexity of your configuration. Much as the `<import/>` element is used within Spring XML files to aid in modularizing configurations, the `@Import` annotation allows for loading `@Bean` definitions from another configuration class, as the following example shows:

```
/**
 * @author Chris Beams
 * @since 3.1
 * @see DeferredImportSelector
 * @see Import
 * @see ImportBeanDefinitionRegistrar
 * @see Configuration
 */
public interface ImportSelector {

	/**
	 * Select and return the names of which class(es) should be imported based on
	 * the {@link AnnotationMetadata} of the importing @{@link Configuration} class.
	 */
	String[] selectImports(AnnotationMetadata importingClassMetadata);

}
```

### 2.6 @Configuration+@Import+ImportBeanDefinitionRegistrar

Spring officially by ImportBeanDefinitionRegistrar injection mechanism to achieve a dynamic annotation @ Component, @ Service and so on.

Many constituents integrated with the Spring framework, through the interface will achieve the specified class scanning, and then registered to the spring container. Mapper interfaces in such Mybatis, springCloud FeignClient the interface are registered custom logic implemented through this interface.

All the classes that implement the interface will be treated ConfigurationClassPostProcessor, ConfigurationClassPostProcessor achieved BeanFactoryPostProcessor interface, dynamically registered ImportBeanDefinitionRegistrar bean bean initialization which depends on the priority thereof, can also be aop, validator processing mechanisms.

The basic steps:

- ImportBeanDefinitionRegistrar implement the interface;
- Implement particular class initializing registerBeanDefinitions;
- @Import introduced using implementation class on the class @Configuration annotation configuration;

[MapperScannerRegistrar](https://github.com/mybatis/spring/blob/1.3.x/src/main/java/org/mybatis/spring/annotation/MapperScannerRegistrar.java)

```
@Configuration
@MapperScan(basePackages= {"org.example1.mapper"})
@Import(MapperScannerRegistrar.class)
public class AppConfig1 {
	  
	  @Bean
	  public SqlSessionFactoryBean sqlSessionFactory() {
		  SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
		  sqlSessionFactoryBean.setDataSource(dataSource());
		  return sqlSessionFactoryBean;
	  }
}
```

What is the difference between ImportSelector and ImportBeanDefinitionRegistar in Spring?

1. What is the difference between `ImportSelector` and `ImportBeanDefinitionRegistar`?
2. Which scenario should be use `ImportSelector` or `ImportBeanDefinitionRegistar` and why?

The [`ImportSelector`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ImportSelector.html) is an

> Interface to be implemented by types that determine which @Configuration class(es) should be imported based on a given selection criteria, usually one or more annotation attributes.

The [`ImportBeanDefinitionRegistrar`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ImportBeanDefinitionRegistrar.html) is an

> Interface to be implemented by types that register additional bean definitions when processing @Configuration classes. Useful when operating at the bean definition level (as opposed to @Bean method/instance level) is desired or necessary.

In one hand, the `ImportSelector` is used to import configurations based on given criteria and on the other hand, the `ImportBeanDefinitionRegistrar` is used to register additional beans when processing configurations. This is two different usages.
