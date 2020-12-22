

## SpringMVC概述

### SpringMVC的概念

就是Spring的一个模块，Spring框架中提供的一个表示层的解决方案。用来替换Servlet。Spring3.0出现，之前用struts比较流行。

### SpringMVC原理

MVC模型：是一种架构的新模式，本身不引入新的功能，只是帮助我们将开发的结构组织的更加合理。使展示与模型分离，流程逻辑控制、业务逻辑调用与展示逻辑分离。
model（模型）：数据模型，包含要展示的数据和业务。
View（视图）：用户界面，在界面上展示模型数据。
Controller（控制器）：起调度作用，接收用户请求，调用业务处理请求，共享数据模型并跳转界面。

### SpringMVC的执行流程

![](https://upload-images.jianshu.io/upload_images/25392987-006d51177607d708.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## SpringMVC入门程序

### 开发流程

1. 添加jar包
   ![](https://upload-images.jianshu.io/upload_images/25392987-366680b6177516d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 在web配置文件中，添加中央调度器的配置

```xml
<!-- 配置中央调度器 -->
   <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
    <!-- 配置监听器 -->
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>*.do</url-pattern>
  </servlet-mapping>

```

1. 定义控制器（处理器），实现Controller接口

```java
public class Mycontroler implements Controller {
	@Override
	public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","hello SpringMVC");
		mv.setViewName("/msg.jsp");
		return mv;
	}	
}

```

1. 添加spring配置文件，在里面配置上控制器的bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" 
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context.xsd"> <!-- bean definitions here -->
	 
	
	<bean id="/my.do" class="com.woniuxy.springmvc.Mycontroler"></bean>
</beans> 

```

### 测试

![](https://upload-images.jianshu.io/upload_images/25392987-91ee2f85525bf12f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### SpringMVC执行流程文字版

![](https://upload-images.jianshu.io/upload_images/25392987-2dd4832508357637.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 通过配置开发

### 处理器映射器

根据请求的url查找Handler（处理器），可以通过XML和注解方式来映射。

- BeanNameUrlHandlerMapping

```xml
<!-- 默认的处理器映射器，可以不用配置,不用加ID -->
	<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
	
	<!-- 缺点：配置一次就会创建一个对象 -->
	<bean id="/my.do" class="com.woniuxy.springmvc.Mycontroler"/>
	<bean id="/your.do" class="com.woniuxy.springmvc.Mycontroler"/>

```

- SimpleUrlHandlerMapping

```xml
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
		<property name="urlMap">
			<map>
			<!-- 多路径映射同一个bean -->
				<entry key="/my.do" value-ref="myController"></entry>
				<entry key="/first.do" value-ref="myController"></entry>
			</map>
		</property>
	</bean>
	
	<bean id="myController" class="com.woniuxy.springmvc.Mycontroler"/>

```

### 处理适配器

按照特定规则（handlerAdapter要求的规则）去执行Handler，且不同的接口可以统一工作。

- SimpleControllerHanderAdapter
  注意：凡是实现了Controller接口的处理器，都要使用该适配。

```java
public class Mycontroler implements Controller {
	@Override
	//有返回值
	public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","hello SpringMVC");
		mv.setViewName("/msg.jsp");
		return mv;
	}	
}

```

- HttpRequestHandlerAdapter
  注意：凡是实现了HttpRequestHandler接口的处理器都是该适配器统一调用

```java
public class Mycontroler implements HttpRequestHandler {
	@Override
	//无返回值
	public void handleRequest(HttpServletRequest request, HttpServletResponse response) 
			throws ServletException, IOException {
		request.setAttribute("msg", "mymsg");
		request.getRequestDispatcher("/msg.jsp").forward(request, response);
	}
}

```

### 处理器

- Controller
- HttpRequestHandler
- AbstractController
- MultiActionController

#### AbstractController

可以定义处理请求的方式，例如post。

```java
public class Mycontroler extends AbstractController {
	@Override
	protected ModelAndView handleRequestInternal(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","mysss");
		mv.setViewName("/msg.jsp");
		return mv;
	}	
}

```

xml配置

```xml
<bean id="myController" class="com.woniuxy.springmvc.Mycontroler">
		<property name="SupportedMethods" value="POST"/>
	</bean>

```

#### MultiActionController

多方法控制器。

1. InternalPathMethodNameResolver
   一个处理中多个方法，底层使用方法名称解析器，默认使用方法名称解析。

```java
public class Mycontroler extends MultiActionController {
	
	public ModelAndView doFirst(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}	
	public ModelAndView doSecend(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doSecend");
		mv.setViewName("/msg.jsp");
		return mv;
	}	
	public ModelAndView doThird(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doThird");
		mv.setViewName("/msg.jsp");
		return mv;
	}	
}

```

配置文件：

```xml
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
		<property name="urlMap">
			<map>
			<!-- *号换成方法名称 例如 doFirst-->
				<entry key="/*.do" value-ref="myController"></entry>
			</map>
		</property>
	</bean>
	
	<bean id="myController" class="com.woniuxy.springmvc.Mycontroler">
	</bean>

```

1. PropertiesMethodNameResolver
   请求路径名称和方法名字不同

```xml
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
		<property name="urlMap">
			<map>
			<!-- *号换成方法名称 例如 doFirst-->
				<entry key="/*.do" value-ref="myController"></entry>
			</map>
		</property>
	</bean>
	<!-- 配置方法解析器 -->
	<bean id="PropertiesMethodNameResolver" 
	class="org.springframework.web.servlet.mvc.multiaction.PropertiesMethodNameResolver">
		<property name="mappings">
			<props>
			<!-- 通过这个解析器，访问可以用first.do -->
				<prop key="/first.do">doFirst</prop>
				<prop key="/secend.do">doSecend</prop>
				<prop key="/third.do">doThird</prop>
			</props>
		</property>
	</bean>
	
	<bean id="myController" class="com.woniuxy.springmvc.Mycontroler">
	<!-- 指定处理器使用PropertiesMethodNameResolver方法解析器 -->
		<property name="methodNameResolver" ref="PropertiesMethodNameResolver"/>
	</bean>

```

1. ParameterMethodNameResolver
   请求路径 /my.do?method=doFirst

```xml
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
		<property name="urlMap">
			<map>
				<entry key="/my.do" value-ref="myController"></entry>
			</map>
		</property>
	</bean>
	<!-- 配置ParameterMethodNameResolver方法解析器 -->
	<bean id="ParameterMethodNameResolver" 
	class="org.springframework.web.servlet.mvc.multiaction.ParameterMethodNameResolver">
		<!-- 将默认参数名称action修改为method -->
		<property name="paramName" value="method"/>
	</bean>
	
	<bean id="myController" class="com.woniuxy.springmvc.Mycontroler">
	<!-- 指定处理器使用PropertiesMethodNameResolver方法解析器 -->
		<property name="methodNameResolver" ref="ParameterMethodNameResolver"/>
	</bean>

```

### 视图解析器

注意：无ID

#### 默认的内部资源视图解析器：InternalResourceVireResolver

![](https://upload-images.jianshu.io/upload_images/25392987-94fd885b137011d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![enter description here](https://i.loli.net/2019/06/22/5d0ded09ee10874090.jpg)

#### BeanNameViewResolver：bean名称的视图解析器

##### jstlView：定义内部资源，添加相关jar包。

![](https://upload-images.jianshu.io/upload_images/25392987-269b78323f186219.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### redirectView：定义外部资源

![](https://upload-images.jianshu.io/upload_images/25392987-d6dc444296cfb901.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
注意：配置了BeanNameViewResolver后，要将默认的视图解析器也配上。

## 通过注解开发

### 使用注解开发的好处

1. 一个controller类可以有多个处理办法
2. 请求映射不需要再XML中配置，开发效率更高。

### 基本结构

1. 注解开发依然需要在web.xml配置中央调度器。
2. 在spring配置文件中配置注解扫描器

```xml
<context:component-scan base-package="com.woniuxy.springmvc"/>

```

controller

```java
@Controller
public class Mycontroler{

	@RequestMapping("/my.do")//这个注解就相当于映射器
	protected ModelAndView doFirst(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}
	@RequestMapping({"/your.do","he.do"})//多路径映射同一个处理器,用数组
	protected ModelAndView doSecend(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doSecend");
		mv.setViewName("/msg.jsp");
		return mv;
	}
}

```

#### URI命名空间

controller

```java
@Controller
@RequestMapping("/test")//浏览器访问方式变为../test/first.do
public class Mycontroler{

	@RequestMapping("/my.do")//这个注解就相当于映射器
	protected ModelAndView doFirst(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}
}

```

#### URI统配符号

controller

```java
@Controller
public class Mycontroler{

	@RequestMapping("/my*.do")//my后面任意
	protected ModelAndView doFirst(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}
	@RequestMapping("/*your.do")//your前面任意
	protected ModelAndView doSecend(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doSecend");
		mv.setViewName("/msg.jsp");
		return mv;
	}
	@RequestMapping("/*/third.do")// /名称任意/third.do
	protected ModelAndView doThird(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doSecend");
		mv.setViewName("/msg.jsp");
		return mv;
	}
}

```

#### 限定提交方式

controller

```java
@Controller
public class Mycontroler{
	//仅支持post请求
	@RequestMapping(value="/my*.do",method=RequestMethod.POST)
	protected ModelAndView doFirst(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}
}

```

#### 限定请求参数

```java
@Controller
public class Mycontroler{
	//提交的请求参数中必须要有name和age
	@RequestMapping(value="/first*.do",params= {"name","age"})
	protected ModelAndView doFirst(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}
	//提交的请求参数中必须要有name,不能有age
	@RequestMapping(value="/secend*.do",params= {"name","!age"})
	protected ModelAndView doSecend(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}	
	//提交的请求参数中必须要有name和age,且name的值必须为zs.
	@RequestMapping(value="/third*.do",params= {"name=zs","age"})
	protected ModelAndView doThird(HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}
}

```

### 处理方法的参数

#### 接收单个参数

**页面：**

```html
<body>
<form action="/springmvc-01-annotation/first.do" methos="get">
	<input type="text" name="name" />
	<input type="text" name="age" />
	<input type="submit" value="ok">
</form>
</body>

```

**controller：**

```java
@Controller
public class Mycontroler{
	//单个参数接收，处理器方法的参数名和表单的name名一致
	@RequestMapping(value="/first.do")
	protected ModelAndView doFirst(String name,int age){
		System.out.println("name="+name);
		System.out.println("age="+age);
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}
}

```

**测试结果**
![](https://upload-images.jianshu.io/upload_images/25392987-764493e11da7478c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 乱码解决

1. get请求在tomkit中配置即可。
2. post请求在web配置文件添加如下配置

```xml
<!-- POST乱码解决 -->
  <filter>
  	<filter-name>CharacterEncodingFilter</filter-name>
  	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  	<!-- 指定编码 -->
  	<init-param>
  		<param-name>encoding</param-name>
  		<param-value>utf-8</param-value>
  	</init-param>
  	<!-- 使用强制编码 -->
  	<init-param>
  		<param-name>forceEncoding</param-name>
  		<param-value>true</param-value>
  	</init-param>
  </filter>
  <filter-mapping>
  	<filter-name>CharacterEncodingFilter</filter-name>
  	<url-pattern>/*</url-pattern>
  </filter-mapping>

```

#### 校正请求参数

```java
@Controller
public class Mycontroler{
	//当表单元素的name和方法参数名不相同的时候，使用@RequestParam注解
	@RequestMapping(value="/first.do")
	protected ModelAndView doFirst(@RequestParam("sname") String name,
			@RequestParam("sage") int age){
		System.out.println("name="+name);
		System.out.println("age="+age);
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}
}

```

#### 接收对象

1. 建立Student对象
2. Mycontroler.java

```java
@Controller
public class Mycontroler{
	@RequestMapping(value="/first.do")
	protected ModelAndView doFirst(Student student){
		System.out.println(student);
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}
}

```

1. 页面设置：

```html
<body>
<form action="/springmvc-01-annotation/first.do" method="post">
	<!-- <input type="text" name="sname" />
	<input type="text" name="sage" /> -->
	<input type="text" name="student.sname">
	<input type="text" name="student.age">
	<input type="submit" value="ok">
</form>
</body>

```

#### restful风格

1. 修改web配置文件中的调度器,将*.do改为"/"

```xml
 <!-- 配置中央调度器 -->
   <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
    <!-- 配置监听器 -->
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

```

1. 修改spring的配置文件

```xml
 <!-- 扫描注解 -->
	<context:component-scan base-package="com.woniuxy.springmvc"/>
	<!-- 这样配置才能使用静态资源 -->
	<mvc:annotation-driven/>
	<mvc:default-servlet-handler/>

```

1. cotroller

```java
@Controller
@RequestMapping("/test")
public class Mycontroler{
    //id相当于占位符
	@RequestMapping(value="/delete/{id}")
	protected ModelAndView doFirst(@PathVariable("id") String id){
		System.out.println(id);
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}
}

```

1. 浏览器输入：“http://localhost/springmvc-01-annotation/test/delete/1”

### 处理方法的返回值

#### ModelAndView

Model是数据，View视图,两个都需要就使用这个返回值

```java
@RequestMapping(value="/secend.do")
	protected ModelAndView doSecend(){
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}

```

#### String

返回的字符串作为视图ID（JstlView(使用它必须要导入jstl的jar包),RedirectView）

```java
    //返回的字符串为视图ID。
	@RequestMapping(value="/third.do")
	protected String doThird(){
		System.out.println("go go go");
		return "mymsg";
	}
	//转发：默认的资源视图解析器,前缀后缀不起作用
	@RequestMapping(value="/third.do")
	protected String doThird(){
		System.out.println("go go go");
		return "forward:/test.jsp";
	}
	//重定向：默认的资源视图解析器,前缀后缀不起作用
		@RequestMapping(value="/third1.do")
		protected String doThird1(){
			System.out.println("go go go");
			return "redirect:/test.jsp";
		}

```

#### void

使用我们原来学习的servlet来处理即可。

```java
@RequestMapping(value="/third.do")
	protected void doThird(HttpServletRequest req,HttpServletResponse resp) 
			throws ServletException, IOException{
		req.getRequestDispatcher("/msg.jsp").forward(req, resp);
	}

```

#### Object

使用Jackson，实现Ajax响应。

1. 注意要添加jackson的jar包
   ![](https://upload-images.jianshu.io/upload_images/25392987-546a06e531d3fb6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 在Spring配置文件中配置MVC注解驱动

```xml
<!-- 配置MVC注解驱动，JAcson底层使用HttpMessageConver，框架会自动创建需要的核心类对象 -->
	<mvc:annotation-driven/>

```

1. 添加ResponseBody注解，将对象注入到响应体。

```java
@Controller
@RequestMapping("/test")
public class Mycontroler{
	@RequestMapping(value="/first.do")
	@ResponseBody//将返回的对象注入到响应体中
	protected Object doFirst(){
		System.out.println("执行处理");
		return new Student("lzff",11);
	}
}

```

4.页面发送AJAX请求

```javascript
<script type="text/javascript">
	$(function(){
		$("#btnok").click(function(){
			$.post("/springmvc-01-annotation/test/first.do",
					null,
					function(data){
					alert(data.sname);
					alert(data.age);
			})
		})
	})
</script>

```

注意：注解驱动一定要配置，框架底层才能自动创建相关的对象。使用Jackson，AJAX不用将获得的数据在eval化。直接使用即可。

## MVC中的异常处理

在Serverlet中处理异常有两种方式，try-catcah或者是在web.xml中配置错误页面。在SpringMVC中，

### 普通的异常处理器配置

在Spring配置文件中注册异常处理器，配上相关的属性即可。

```xml
<!-- 注册异常处理器，不需要ID -->
	<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
		<!-- 配置默认的错误页面 -->
		<property name="defaultErrorView" value="/errors/error.jsp" />
		<!-- 异常信息存储到域中，可以在页面中显示 -->
		<property name="exceptionAttribute" value="ex" />
		<!-- 配置不同的异常转到不同的错误页面 -->
		<property name="exceptionMappings">
			<props>
				<prop key="java.lang.NullPointerException">/errors/nullpoint.jsp</prop>
				<prop key="java.lang.ArithmeticException">/errors/aritherror.jsp</prop>
			</props>
		</property>
	</bean>

```

添加错误页面，使用${ex.message}获取异常信息
![](https://upload-images.jianshu.io/upload_images/25392987-3ba4ca6a76e0bf60.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
缺点:该异常处理只能做页面跳转，不能执行其他操作，例如想在错误的时候执行保存错误日志等。

### 自定义异常处理器

自定义异常处理器，可以用注解实现，也可以用配置实现。视使用场景不同自行选择。

#### 使用配置实现

1. 创建一个类，实现HandlerExceptionResolver接口，并实现接口中的方法

```java
public class MyHandlerExceptionResolver implements HandlerExceptionResolver{
	@Override
	public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler,
			Exception ex) {
		ModelAndView mv=new ModelAndView();
		mv.addObject("ex",ex);
		if(ex instanceof ArithmeticException) {
			System.out.println("记录产生ArithmeticException异常");
			mv.setViewName("/errors/aritherror.jsp");
		}else if(ex instanceof NullPointerException) {
			System.out.println("记录产生NullPointerException异常");
			mv.setViewName("/errors/nullpoint.jsp");
		}else{
			System.out.println("Exception记录日志");
			mv.setViewName("/errors/error.jsp");
		}
		return mv;
	}
}

```

1. 在Spring配置文件中配置该类的BEAN。不需要ID。

```xml
<bean class="com.woniuxy.springmvc.MyHandlerExceptionResolver"></bean>
1
```

1. 测试成功。
   ![](https://upload-images.jianshu.io/upload_images/25392987-5529c4885671d17d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
   ![](https://upload-images.jianshu.io/upload_images/25392987-74e3dba98514fd79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 使用注解实现

使用注解实现异常处理只需在上例基础做如下修改：

1. 删除配置文件中异常解析器的配置
2. 不需要实现特定接口的异常处理类
3. 将接口中的方法添加到处理器方法中，添加@ExceptionHandler注解。

```java
@RequestMapping(value="/third.do")
	protected ModelAndView doThird(){
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doSecend");
		mv.setViewName("/msg.jsp");
		return mv;
		
	}
	@ExceptionHandler
	public ModelAndView resolveException(Exception ex) {
		ModelAndView mv=new ModelAndView();
		mv.addObject("ex",ex);
		if(ex instanceof ArithmeticException) {
			System.out.println("记录产生ArithmeticException异常");
			mv.setViewName("/errors/aritherror.jsp");
		}else if(ex instanceof NullPointerException) {
			System.out.println("记录产生NullPointerException异常");
			mv.setViewName("/errors/nullpoint.jsp");
		}else{
			System.out.println("Exception记录日志");
			mv.setViewName("/errors/error.jsp");
		}
		return mv;
	}

```

## SpringMVC的日期转换

### 通过配置解决

1. 配置页面

```html
<body>
<form action="${pageContext.request.contextPath}/test/first.do">
	<input type="text" name="name" /></br>
	<input type="text" name="birthdate" /></br>
	<input type="submit" value="OK">
</form>
</body>

```

1. 处理器接受参数，希望接收的是date类

```java
@Controller
@RequestMapping("/test")
public class Mycontroler{
//页面传过来的 birthdate应该是个string类型，而我们想转换成date类型，MVC不会自动转换， 此时会出400或500错误
	@RequestMapping(value="/first.do")
	protected ModelAndView doFirst(String name,Date birthdate){
		System.out.println("name"+name);
		System.out.println("birthdate"+birthdate);
		ModelAndView mv=new ModelAndView();
		mv.addObject("msg","doFirst");
		mv.setViewName("/msg.jsp");
		return mv;
	}
}

```

1. 配置转换类DateConvert，需要实现Converter接口（S原数据类型，T目标数据类型）

```java
public class DateConvert implements Converter<String, Date>{
	@Override
	public Date convert(String source) {
		if(source!=null && !source.trim().equals("")) {
			DateFormat df=new SimpleDateFormat("yyyy-MM-dd");
			try {
				return df.parse(source);
			} catch (ParseException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
		return null;
	}
}

```

1. 此时程序还不知道什么时候用，需要再Spring的配置文件中配置。

```xml
 <!-- 扫描注解 -->
	<context:component-scan base-package="com.woniuxy.springmvc"/>
	<!-- 注册自定义转换器 -->
	<bean id="dateConvert" class="com.woniuxy.convert.DateConvert"/>
	<!-- 注册类型转换服务bean -->
	<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
		<property name="converters">
			<set>
				<ref bean="dateConvert"/>
			</set>
		</property>
	</bean>
	<!-- 配置MVC注解驱动。-->
	<mvc:annotation-driven conversion-service="conversionService"/>

```

1. 再次传值，此时处理器成功接收。
   ![](https://upload-images.jianshu.io/upload_images/25392987-d7610a7cd6aa1adc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 通过注解解决

1. 在处理器类中，添加方法，方法返回值为空
2. 在该方法上添加@InitBinder注解
3. 注册原类型和目标类型的转换格式

```java
@InitBinder//添加initBinder注解
	public void initBinder(WebDataBinder binder) {
		DateFormat dateFormat=new SimpleDateFormat("yyyy-MM-dd");
		//注册原类型和目标类型的转换格式
		binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, true));
	}

```

日期转换使用注解缺点和自定义异常使用注解的缺点相同，只有该处理器类能够使用。

## SpringMVC的数据验证

使用hibernate-validator验证插件。

1. 添加jar包
   ![](https://upload-images.jianshu.io/upload_images/25392987-8dd675f7d94b5c43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 注册验证器并配置注解驱动

```xml
	 <!-- 扫描注解 -->
	<context:component-scan base-package="com.woniuxy.springmvc"/>
	<!-- 注册验证器 -->
	<bean id="localValidator" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
		<!-- 提供验证器 -->
		<property name="providerClass" value="org.hibernate.validator.HibernateValidator "/>
	</bean>
	<!-- 配置注解驱动，使用validator="localValidator-->
	<mvc:annotation-driven validator="localValidator"/>

```

1. 在实体类需要验证的数据上添加注解

```java
public class Student {
	@NotEmpty(message="姓名不能为空")//对象为空
	@Size(min=3,max=5,message="长度{min}--{max}之间")
	private String name;
	@NotNull(message="成绩不能为空")
	@Min(value=0,message="不能小于{value}")
	@Max(value=100,message="不能大于{value}")
	private Double score;
	@NotEmpty(message="手机号不能为空")
	@Pattern(regexp="^1[38]\\d{9}$",message="手机号格式错误")
	private String mobile;
	@NotNull(message="生日不能为空")
	@DateTimeFormat(pattern="^\\d{4}-\\d{2}-\\d{2}$")
	private Date birthdate;
	@NotEmpty(message="邮箱不能为空")
	@Email(message="邮箱格式错误")
	private String email;
	}

```

1. 修改处理器中得方法

```java
@RequestMapping(value="/first.do")
	//BindingResult将验证的错误信息记录
	protected ModelAndView doFirst(@Validated Student student,BindingResult br){
		ModelAndView mv=new ModelAndView();
		int count =br.getErrorCount();//获取验证错误条数
		if(count>0) {
			//获取错误消息字段
			FieldError fieldErrorName = br.getFieldError("name");
			FieldError fieldErrorScore = br.getFieldError("score");
			FieldError fieldErrorMobile = br.getFieldError("mobile");
			FieldError fieldErrorBirthdate = br.getFieldError("birthdate");
			FieldError fieldErrorEmail = br.getFieldError("email");
			if(fieldErrorName!=null) {
				mv.addObject("errorname", fieldErrorName.getDefaultMessage());
			}
			if(fieldErrorScore!=null) {
				mv.addObject("errorscore", fieldErrorScore.getDefaultMessage());
			}
			if(fieldErrorMobile!=null) {
				mv.addObject("errormobile", fieldErrorMobile.getDefaultMessage());
			}
			if(fieldErrorBirthdate!=null) {
				mv.addObject("errorbirthdate", fieldErrorBirthdate.getDefaultMessage());
			}
			if(fieldErrorEmail!=null) {
				mv.addObject("erroremail", fieldErrorEmail.getDefaultMessage());
			}
			mv.setViewName("/index.jsp");
			return mv;
		}
		mv.addObject("msg","登陆成功");
		mv.setViewName("/msg.jsp");
		return mv;
	}

```

## SpringMVC的图片上传功能

1. 表单的要求
   method=”post” encytype=”multipart/form-data”
2. 导入jar包
   fileupload.jar io.jar
3. jsp页面

```html
<body>
<form action="/springmvc-01-fileupload/test/first.do" method="post" 
			enctype="multipart/form-data">
	<!-- <input type="text" name="sname" />
	<input type="text" name="sage" /> -->
	<input type="file" name="newImg">
	<input type="submit" value="ok">
</form>
</body>

```

1. 在处理器中使用MultipartFile上传

```java
@RequestMapping("/test")
public class Mycontroler{
	@RequestMapping(value="/first.do")
	@ResponseBody//将返回的对象注入到响应体中
	//MultipartFile对象名必须和表单元素name一致
	protected ModelAndView doFirst(MultipartFile newImg,HttpServletRequest request) throws IllegalStateException, IOException{
		if(newImg!=null) {
			//文件打散，UUID取前两位
			//将虚拟路径转换成绝对路径
			String path=request.getServletContext().getRealPath("/img");
			//获取文件名字
			String fileName=newImg.getOriginalFilename();
			//获取IO流
			File file=new File(path,fileName);
			//上传
			newImg.transferTo(file);
		}
		ModelAndView mv=new ModelAndView();
		mv.setViewName("/msg.jsp");
		return mv;
	}
}

```

1. 在Spring的配置文件中添加配置

```xml
<!-- 上传参数是一个接口，底层的类由他创建-->
	<mvc:annotation-driven/>
	<!-- 多部件解析器 ,id值不能乱取，dispathcherservlet会调用-->
	<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>

```

## SpringMVC的拦截器

拦截器和Servlet的过滤器类似，用来拦截请求。不同的是， 拦截器不会拦截页面，只会拦截请求。

### 拦截器流程

![](https://upload-images.jianshu.io/upload_images/25392987-ede8ba95578d7783.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 多个拦截器的执行流程

- 图上方框中的部分是一个整体链条:Ainterceptoer—preHandle()方法返回假，整个链条结束；返回真，afterCompletion()方法压栈，继续往下执行；

- Binterceptoer—preHandle()方法返回假，整个链条结束；返回真，afterCompletion()方法压栈，继续往下执行；

- 最后执行栈中的方法。

  配置：

  ![](https://upload-images.jianshu.io/upload_images/25392987-0e626a4479a1b52d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  拦截器类的创建：
  
  ![](https://upload-images.jianshu.io/upload_images/25392987-1e5eb695c3b43ea5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
