# JSP
## 1 JSP技术的特征

  JSP技术所开发的web应用程序是基于Java的，它拥有Java跨平台的特性，以及业务代码分离，组建重用，基础Java servlet功能和预编译功能。

  **1）跨平台**

​    由于JSP是基于Java语言的，因而它可以使用Java的API，所以也是跨平台的，可以应用在Windows、Linux、Mac和Solaris。

  **2）业务代码分离**

​    采用JSP开发的项目，通常使用HTML语言来设计和格式化静态页面内容，而使用JSP标签来实现动态部分，业务代码通常使用servlet、struts、springmvc等业务控制层来处理，从而实现业务层和视图层分离，这样，JSP只负责显示数据即可，这样，修改业务代码不会影响JSP页面代码。

  **3）组件重用**     

​    JSP中，可以使用JavaBean编写业务组件，也就是使用一个JavaBean封装业务处理代码或者作为一个数据处理模型，这个JavaBean可以重复使用，也可以应用到其他应用程序中。

  **4）继承Java servlet功能**

​    JSP的本质是servlet，因此说JSP拥有servlet的所有功能。

   **5）预编译**

​    用户首次通过浏览器访问JSP页面时，服务器对JSP页面代码进行编译，并且仅执行一次编译，编译后被保存，下次访问时直接执行编译过的代码，节约了服务器资源，提升了客户端访问速度。

## 2 JSP技术的原理

​    JSP的工作方式是请求/应答模式，客户端发出HTTP请求，JSP收到请求后进行处理，并返回处理结果。在一个JSP文件首次被请求时，JSP引擎首先把这个JSP文件转换成一个servlet，而该引擎本身也是一个servlet。运行过程如下：

​    1）JSP引擎首先把该JSP文件转换成一个Java源文件（servlet），在转换时，如果发现JSP文件中有任何语法错误，则中断转换过程，并向服务端和客户端输出错误信息。

​    2）如果转换成功，JSP引擎用javac把该Java源文件编译成相应的class文件。

​    3）创建一个servlet（JSP页面的转换结果）实例，该servlet的jspInit()方法被执行，jspInit()方法在servlet生命周期中只调用一次。

​    4）用jspService()方法处理客户端的请求。对每一个请求，JSP引擎创建一个新的线程来处理。如果多个客户端同时请求该JSP文件，则JSP引擎会创建多个线程来处理每个请求。由于该servlet始终驻留与内存，所以可以非常迅速的响应客户端的请求。

​    5）如果JSP文件被修改了，服务器将根据设置决定是否对该文件重新编译，如果需要重新编译，则将以编译结果取代内存中的servlet，并继续以上过程。

​    6）虽然JSP的效率很高，但首次调用时，由于需要转换和编译，会有一些轻微的延迟。此外，在任何时候，由于系统资源不足的原因，JSP引擎将以某种不确定的方式将servlet中从内存中移去。在此情况下，jspDestroy()方法首先被调用，然后servlet实例将被回收。

  在jspInit()中可进行一些初始化工作，如建立与数据库的连接或其他配置。

![](https://upload-images.jianshu.io/upload_images/25392987-cddee89ca9576954.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
