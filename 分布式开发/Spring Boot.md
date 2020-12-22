

# Spring Boot简介

Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。有了它，你可以更加敏捷地开发Spring应用程序，专注于应用程序的功能，而不用在Spring的配置上多花功夫，甚至完全不用配置。

Spring Boot提供了四个核心功能：

- 自动配置：针对很多Spring应用程序的常见的应用功能，Spring Boot能自动提供相关的配置；
- 起步依赖：告诉Spring Boot需要什么功能，它就能引入需要的库；
- Spring Boot CLI：你只需写代码就能完成完整的应用程序，无需传统项目构建；
- Actuator：让你深入运行中的Spring Boot应用程序，一探究竟。

每一个特性都能以自己的方式简化Spring应用的开发。

首先，我们看一下如何开发一个Spring Boot的Hello World程序。

# Spring Boot工程搭建

Spring Boot工程与普通的maven/gradle工程无异，只是在依赖中需要添加对Spring Boot的依赖。不过，有一种更简单的方式是直接用Spring官网的Spring Initializer来生成项目骨架。

打开：https://start.spring.io/，填写工程类型、语言、Spring Boot版本号及构件信息，如下图：

![](https://upload-images.jianshu.io/upload_images/25392987-50db80b2d327a7ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击Generate Project后，会自动下载完整的项目骨架下来，并且在pom.xml中已经帮你依赖好了所需依赖的构件。

pom.xml如下：

```html
<? xml version = "1.0" encoding = "UTF-8" ? >


				  < project xmlns = "http://maven.apache.org/POM/4.0.0" xmlns : xsi = "http://www.w3.org/2001/XMLSchema-instance"


xsi: schemaLocation = "http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" >


		      < modelVersion > 4.0 .0 < / modelVersion >


		      < groupId > com.example</ groupId>


		      < artifactId > demo</ artifactId>


		      < version > 0.0 .1 - SNAPSHOT</ version>


		      < packaging > jar</ packaging>


		      < name > demo</ name>


		      < description > Demo project for Spring Boot</ description>


		      < parent >


		      < groupId > org.springframework.boot</ groupId>


		      < artifactId > spring - boot - starter - parent</ artifactId>


		      < version > 2.1 .0.RELEASE</ version>


		      < relativePath / > < !--lookup parent from repository-- >


		      < / parent >


		      < properties >


		      < project.build.sourceEncoding > UTF - 8 < / project.build.sourceEncoding >


		      < project.reporting.outputEncoding > UTF - 8 < / project.reporting.outputEncoding >


		      < java.version > 1.8 < / java.version >


		      < / properties >


		      < dependencies >


		      < dependency >


		      < groupId > org.springframework.boot</ groupId>


		      < artifactId > spring - boot - starter - web</ artifactId>


		      < / dependency >


		      < dependency >


		      < groupId > org.springframework.boot</ groupId>


		      < artifactId > spring - boot - starter - test</ artifactId>


		      < scope > test</ scope>


		      < / dependency >


		      < / dependencies >


		      < build >


		      < plugins >


		      < plugin >


		      < groupId > org.springframework.boot</ groupId>


		      < artifactId > spring - boot - maven - plugin</ artifactId>


		      < / plugin >


		      < / plugins >


		      < / build >


		      < / project>
```

同时可以看到主类DemoApplication.java：

```java
package com.example.demo;


import org.springframework.boot.SpringApplication;


import org.springframework.boot.autoconfigure.SpringBootApplication;


@SpringBootApplication


public class DemoApplication {
	public static void main( String[] args )
	{
		SpringApplication.run( DemoApplication.class, args );
	}
}
```

运行该类，看到如下输出即为启动成功：

![](https://upload-images.jianshu.io/upload_images/25392987-58a9f8b8732bdf5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

至此，我们便完成了一个最简项目的搭建和运行。

# Spring Boot应用的启动流程

DemoApplication类在我们的Spring Boot应用中有两个作用：配置和启动引导。

我们可以看到在主类上，有一个@SpringBootApplication注解，这个注解开启了Spring的组件扫描和Spring Boot的自动配置功能，查看其源码：

```java
/*  */


/* Source code recreated from a .class file by IntelliJ IDEA */


/* (powered by Fernflower decompiler) */


/*  */


package org.springframework.boot.autoconfigure;


import java.lang.annotation.Documented;


import java.lang.annotation.ElementType;


import java.lang.annotation.Inherited;


import java.lang.annotation.Retention;


import java.lang.annotation.RetentionPolicy;


import java.lang.annotation.Target;


import org.springframework.boot.SpringBootConfiguration;


import org.springframework.boot.context.TypeExcludeFilter;


import org.springframework.context.annotation.ComponentScan;


import org.springframework.context.annotation.FilterType;


import org.springframework.context.annotation.ComponentScan.Filter;


import org.springframework.core.annotation.AliasFor;


@Target( { ElementType.TYPE } )


@Retention( RetentionPolicy.RUNTIME )


@Documented


@Inherited


@SpringBootConfiguration


@EnableAutoConfiguration


@ComponentScan(


	excludeFilters = { @Filter(


				   type		= FilterType.CUSTOM,


				   classes	= { TypeExcludeFilter.class }


				   ), @Filter(


				   type		= FilterType.CUSTOM,


				   classes	= { AutoConfigurationExcludeFilter.class }


				   ) }


	)


public @interface SpringBootApplication
{
	@AliasFor(


		annotation = EnableAutoConfiguration.class


		)


	Class<?>[] exclude() default
	{
	};


	@AliasFor(


		annotation = EnableAutoConfiguration.class


		)


	String[] excludeName() default
	{
	};


	@AliasFor(


		annotation = ComponentScan.class,


		attribute = "basePackages"


		)


	String[] scanBasePackages() default
	{
	};


	@AliasFor(


		annotation = ComponentScan.class,


		attribute = "basePackageClasses"


		)


	Class<?>[] scanBasePackageClasses() default
	{
	};
}
```

可以看到它是把Spring的@Configuration、Spring的@ComponentScan和Spring Boot的@EnableAutoConfiguration组合到了一起。与@Configuration类似，我们可以通过scanBasePackages或者scanBasePackageClasses属性指定组件扫描的包。

SpringApplication.run(DemoApplication.class, args)开启了项目的运行

# Spring Boot起步依赖

在传统的Spring应用中，使用某个特定的功能时，需要自己手动添加对应的依赖，不仅麻烦，而且容易因为不同构件版本之间的兼容问题而出错。

Spring Boot提供了众多的起步依赖，降低项目依赖的复杂度。起步依赖本质上是一个Maven项目对象模型，定义了对其他库的传递依赖，这些东西加在一起即支持某项功能。

在上面的例子中，期望开发一个Web应用，我们依赖了spring-boot-starter-web，由它去依赖其他的构件。

# Spring Boot自动配置

Spring Boot自动配置是一个运行时(更准确地说，是应用程序启动时)的过程，考虑了众多因素，才决定Spring配置应该用哪个，不该用哪个。

例如：Spring的JdbcTemplate是不是在Classpath下？如果是，并且有DataSource Bean，那么就自动配置一个JdbcTemplate的Bean等等。

自动配置的原理是利用了Spring4.0引入的条件化配置的特性，条件化配置允许配置存在于应用程序中，但在满足特定的条件之前都忽略这些配置。

以Spring Boot下的国际化为例，其自动配置类在org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration类中。大致源码如下：

```java
package org.springframework.boot.autoconfigure.context;


@Configuration


@ConditionalOnMissingBean(


	value = { MessageSource.class },


	search = SearchStrategy.CURRENT


	)


@AutoConfigureOrder( -2147483648 )


@Conditional(
	{
		MessageSourceAutoConfiguration.ResourceBundleCondition.class
	}


	)


@EnableConfigurationProperties


public class MessageSourceAutoConfiguration {
	private static final Resource[] NO_RESOURCES = new Resource[0];


	public MessageSourceAutoConfiguration()
	{
	}


	@Bean


	@ConfigurationProperties(


		prefix = "spring.messages"


		)


	public MessageSourceProperties messageSourceProperties()
	{
		return(new MessageSourceProperties() );
	}


	@Bean


	public MessageSource messageSource( MessageSourceProperties properties )
	{
		...
```

可以看到这个配置类上使用了条件化注解@ConditionalOnMissingBean(value = MessageSource.class, search = SearchStrategy.CURRENT)，表示在当前上下文中不存在MessageSource Bean时，这个配置类才生效。然后根据其messageSource(MessageSourceProperties)方法，自动创建一个MessageSource Bean。

## 自定义属性配置

Spring Boot内置的配置和bean都会有一些自己的配置项，有些配置项Spring Boot已经提供了默认值。比如Spring Boot内置的tomcat启动时所监听的HTTP端口号，它是读取的server.port配置项；以及上面我们所提到的国际化配置，它是读取的spring.messages.basename所指定的路径下的国际化文件，默认值是messages。很多时候我们期望自定义这些属性值，或者增加我们自己的一些配置项，该怎么做呢？

实际上，Spring Boot里我们有多种方式可以设置属性值。包括以下几种：

1. 命令行参数；
2. java:comp/env里的JNDI属性；
3. JVM系统属性；
4. 操作系统的环境变量；
5. 随机生成的带random.*前缀的属性(在设置其他属性时，可以引用它们，比如${random.long})；
6. 应用程序以外的application.properties或者application.yml文件；
7. 打包在应用内的application.properties或者application.yml文件；
8. 通过@PropertySource标注的属性源；
9. 默认属性；

这个列表按照优先级递减排序，也就是说，命令行参数会覆盖在其他地方配置的属性。

application.properties和application.yml是Spring Boot默认就会加载的，不需要手动指定为属性源。它们可以放在以下位置：

1. 外置，相对于应用程序运行目录的/config子目录里；
2. 外置，在应用程序运行的目录里；
3. 内置，在config包内；
4. 内置；在classpath根目录；

这个列表同样按照优先级递减排序。

举个例子，我们通过@PropertySource来配置自定义属性，修改Spring Boot的内置tomcat启动时所监听的HTTP端口号：

```java
package com.example.demo;


import lombok.extern.slf4j.Slf4j;


import org.springframework.boot.SpringApplication;


import org.springframework.boot.autoconfigure.SpringBootApplication;


import org.springframework.context.annotation.PropertySource;




@SpringBootApplication( scanBasePackages = { "com.example.demo.controller", "com.example.demo.dao" } )


@Slf4j


@PropertySource( value = "file:./demo.properties" )


public class DemoApplication {
	public static void main( String[] args )
	{
		SpringApplication.run( DemoApplication.class, args );
	}
}
```

如图，我们在DemoApplication上配置了@PropertySource注解，从同目录下的demo.properties里读入配置，根据优先级规则，这个配置应该覆盖Spring Boot的内置默认配置。

demo.properties配置如下：

```
server.port=9090
```

然后我们运行Spring Boot应用，看到如下输出：

```
2019-09-28 09:59:20.126  INFO 9416 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.error(javax.servlet.http.HttpServletRequest)



2019-09-28 09:59:20.126  INFO 9416 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)



2019-09-28 09:59:20.146  INFO 9416 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]



2019-09-28 09:59:20.146  INFO 9416 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]



2019-09-28 09:59:20.286  INFO 9416 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup



2019-09-28 09:59:20.326  INFO 9416 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 9090 (http) with context path ''



2019-09-28 09:59:20.336  INFO 9416 --- [           main] com.example.demo.DemoApplication         : Started DemoApplication in 4.817 seconds (JVM running for 6.322)
```

可以看到，现在Tomcat的监听端口号变成了我们所配置的9090。

# Spring Boot应用的JUnit单元测试

熟悉Spring的JUnit单元测试的同学应该都很清楚，要运行Spring JUnit单元测试，我们需要在测试类上加@RunWith(SpringJUnit4ClassRunner.class)，SpringJUnit4ClassRunner会为JUnit测试加载Spring应用程序上下文。同时我们需要通过@ContextConfiguration指定配置class。一个典型的样例如下：

```java
package com.huawei.nlz.snippets.cbb;


import org.junit.runner.RunWith;


import org.springframework.test.context.ContextConfiguration;


import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;


import org.springframework.test.context.support.AnnotationConfigContextLoader;


@RunWith( SpringJUnit4ClassRunner.class )


@ContextConfiguration( classes = CbbConfig.class, loader = AnnotationConfigContextLoader.class )


public class SpringJUnitContext {
}
```

@ContextConfiguration在加载Spring应用上下文的过程中做了很多事情，但是它不能完整的加载Spring Boot。Spring Boot下，我们用@SpringBootTest替代之。代码如下：

```java
import com.example.demo.DemoApplication;


import org.junit.Test;


import org.junit.runner.RunWith;


import org.springframework.boot.test.context.SpringBootTest;


import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;


@RunWith( SpringJUnit4ClassRunner.class )


@SpringBootTest( classes = DemoApplication.class )


public class DemoTest {
	@Test


	public void test()
	{
	}
}
```

运行test方法，会提示JUnit测试执行成功。

# Spring Boot应用的打包和运行

Spring Boot应用可以被打包成jar文件直接运行，如果是Web应用，也可以打包成war包丢进传统的应用服务器(如Tomcat等)中运行。由于Spring Boot在应用程序里嵌入了一个Servlet容器，所以Spring Boot开发的Web应用也可以打包成jar包直接运行，而不用部署到传统的Java应用服务器里。

举一个Spring Boot开发RESTful接口的例子：

在前面我们创建好的工程中，加入一个RestController如下：

```java
package com.example.demo;


import org.springframework.web.bind.annotation.RequestBody;


import org.springframework.web.bind.annotation.RequestMapping;


import org.springframework.web.bind.annotation.RequestMethod;


import org.springframework.web.bind.annotation.RestController;


import java.util.HashMap;


import java.util.Map;


@RestController


public class DemoService {
	@RequestMapping( method = RequestMethod.POST, path = "/rest/evaluate" )


	public Map<String, Object> query( @RequestBody Map<String, Object> params )
	{
		Map<String, Object> obj = new HashMap<String, Object>( 5 )
		{
			{
				put( "price", 19.25 );


				put( "amount", 17891 );


				put( "commodity", "iPhone X" );
			}
		};


		return(obj);
	}
}
```

然后我们对工程执行mvn clean package，打出jar包，然后通过java -jar命令运行起该jar包，通过postman访问本地8080端口：

![](https://upload-images.jianshu.io/upload_images/25392987-e651bb82d128f540.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以访问成功。

在我们的pom.xml中，可以看到使用了插件spring-boot-maven-plugin，这个插件的作用是在打jar包时打出一个超级jar，把应用程序所有依赖都打进这个jar中，同时为jar添加一个描述文件，其中的内容能让你用java -jar运行应用程序。我们打开jar包的BOOT-INF/lib目录，可以看到依赖的jar包都被打进来了：

![](https://upload-images.jianshu.io/upload_images/25392987-972ed41c5fa6d817.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 

至此，我们完成了对Spring Boot的一个概要的了解，掌握了如何创建Spring Boot应用，Spring Boot的核心特性以及如何打包部署运行Spring Boot程序。
