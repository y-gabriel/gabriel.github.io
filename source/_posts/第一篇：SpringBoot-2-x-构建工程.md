---
title: 第一篇：SpringBoot-2-x-构建工程
date: 2018/10/08
updated: 2018/10/08
---

# 简介

一直以来都想写点关于 springboot 的东西，每次遇到的问题又记不住，本次的记录也是拾人牙慧，写一点关于自己的理解。
SpringBoot 关于它在官网上的介绍是这样的

![image.png](https://upload-images.jianshu.io/upload_images/9988457-7d31eef5f79573f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
一个基于 Spring 可以轻松创建的独立的，生产级别的应用，让你可以 _“只管运行”_

官方的解释不是重点，我们只需要去关注 SpringBoot 是如何做到*只管运行*

在这里使用官方的 springio 构建项目的方式以及 eclipse 的 springboot 插件的构建方式，我就说下关于 idea 的搭建方式，其余的方式这里就不再赘述了，想要了解的同学可以去网上去查下资料。

## 接下来

![next](https://upload-images.jianshu.io/upload_images/9988457-d49ed57934a098f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![next](https://upload-images.jianshu.io/upload_images/9988457-f093296d809bf652.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在此处勾选需要使用的功能模块，当然可以什么都不勾，等项目创建完毕后直接在 pom.xml 中添加也是可以的，需要注意的是 SpringBoot 在 2.0 之前 JDK1.7 都是支持的，但更新到 2.0 之后就需要 1.8+了，所以在构建项目时，JDK 的版本最好也更新到 1.8+,maven 也升级到 3.2+<br/>
![next](https://upload-images.jianshu.io/upload_images/9988457-7bab81607ca5c144.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
最后，填好工程名以及选择好工程地址就可以开始使用了 SpringBoot 了。<br/>
![image.png](https://upload-images.jianshu.io/upload_images/9988457-7b2256eb2aee2a32.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
最后的结构如下图所示<br/>
![image.png](https://upload-images.jianshu.io/upload_images/9988457-cf8f039f7188a81f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个 Demo1Application 是自动生成的也是我们待会需要运行的文件，这里需要注意的最好把该文件放在根目录下，不然可能会出现一些问题。

好了，接下来的话，开始运行这个文件

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.0.5.RELEASE)

2018-10-08 14:22:07.086  INFO 5956 --- [           main] com.gabriel.Demo1Application             : Starting Demo1Application on stu with PID 5956 (C:\Users\Administrator.CDEDUASK_PC\IdeaProjects\demo1\target\classes started by Administrator in C:\Users\Administrator.CDEDUASK_PC\IdeaProjects\demo1)
2018-10-08 14:22:07.090  INFO 5956 --- [           main] com.gabriel.Demo1Application             : No active profile set, falling back to default profiles: default
2018-10-08 14:22:07.258  INFO 5956 --- [           main] ConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@2aa5fe93: startup date [Mon Oct 08 14:22:07 CST 2018]; root of context hierarchy
2018-10-08 14:22:09.050  INFO 5956 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2018-10-08 14:22:09.076  INFO 5956 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2018-10-08 14:22:09.076  INFO 5956 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.34
2018-10-08 14:22:09.090  INFO 5956 --- [ost-startStop-1] o.a.catalina.core.AprLifecycleListener   : The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [C:\WorkSofe\java\jdk\bin;C:\Windows\Sun\Java\bin;C:\Windows\system32;C:\Windows;C:\WorkSofe\java\jdk\bin;%JAVA_HOME\jre\bin;C:\ProgramData\Oracle\Java\javapath;C:\WorkSofe\Oracle11\product\11.2.0\dbhome_1\bin;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\WorkSofe\TortoiseSVN\bin;C:\Program Files (x86)\MySQL\MySQL Fabric 1.5.3 & MySQL Utilities 1.5.3 1.5\;C:\Program Files (x86)\MySQL\MySQL Fabric 1.5.3 & MySQL Utilities 1.5.3 1.5\Doctrine extensions for PHP\;C:\Program Files\Git\cmd;C:\Go\bin;C:\Program Files\VisualSVN Server\bin;C:\Users\Administrator.CDEDUASK_PC\AppData\Local\Programs\Python\Python37\Scripts\;C:\Users\Administrator.CDEDUASK_PC\AppData\Local\Programs\Python\Python37\;D:\sshAZ;%GOPATH%\bin;.]
2018-10-08 14:22:09.191  INFO 5956 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2018-10-08 14:22:09.191  INFO 5956 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1938 ms
2018-10-08 14:22:09.264  INFO 5956 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Servlet dispatcherServlet mapped to [/]
2018-10-08 14:22:09.270  INFO 5956 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
2018-10-08 14:22:09.271  INFO 5956 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2018-10-08 14:22:09.271  INFO 5956 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2018-10-08 14:22:09.271  INFO 5956 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
2018-10-08 14:22:09.419  INFO 5956 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-10-08 14:22:09.628  INFO 5956 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@2aa5fe93: startup date [Mon Oct 08 14:22:07 CST 2018]; root of context hierarchy
2018-10-08 14:22:09.727  INFO 5956 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2018-10-08 14:22:09.728  INFO 5956 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2018-10-08 14:22:09.756  INFO 5956 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-10-08 14:22:09.756  INFO 5956 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-10-08 14:22:09.957  INFO 5956 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2018-10-08 14:22:10.000  INFO 5956 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2018-10-08 14:22:10.004  INFO 5956 --- [           main] com.gabriel.Demo1Application             : Started Demo1Application in 3.753 seconds (JVM running for 4.316)
```

启动日志如上，看到端口了，访问一下吧
![image.png](https://upload-images.jianshu.io/upload_images/9988457-976c65a4a106eb1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看到 error page 了？ don't worry 我们来看看启动日志

![image.png](https://upload-images.jianshu.io/upload_images/9988457-36f5a3106f08a3c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

原来是没有配置映射，ok 我们打开 Demo1Application 来看看
![image.png](https://upload-images.jianshu.io/upload_images/9988457-392d2d15eabefd30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

给这个类加上映射来访问看看

![image.png](https://upload-images.jianshu.io/upload_images/9988457-ea4a9a4698823ef8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看看这次的启动日志
![image.png](https://upload-images.jianshu.io/upload_images/9988457-e7b13a5ec0304782.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

明显有个 / 被映射到了，这次应该没什么问题了，再次访问

![image.png](https://upload-images.jianshu.io/upload_images/9988457-d49b0f38dd9b8690.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

SpringBoot YES! 好了，我们的工程构建完毕了。
