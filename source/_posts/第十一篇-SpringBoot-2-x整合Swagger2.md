---
title: 第十一篇-SpringBoot-2-x整合Swagger2
date: 2018/11/05
updated: 2018/11/05
---

> 程序猿最烦两件事，第一件事是别人要他给自己的代码写文档，第二件呢？是别人的程序没有留下文档。

> 程序员最讨厌的四件事：写注释、写文档、别人不写注释、别人不写文档……

关于写文档这个事情，争论已久，今天就介绍一个解决这个问题的东东，`Swagger`。
这里介绍的是由[程序员 DD 翟永超](http://blog.didispace.com/)提供的`spring-boot-starter-swagger`关于其详细设置在文章底部。

### pom.xml

```
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<!-- 引入swagger2 -->
		<dependency>
			<groupId>com.spring4all</groupId>
			<artifactId>swagger-spring-boot-starter</artifactId>
			<version>1.8.0.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
```

在这里我们需要做的事情就不像引入官方依赖一样需要一个个的加注解，只需要一个注解就 OK 了。`@EnableSwagger2Doc`，这个注解加在启动类上

```java
package com.priv.gabriel;

import com.spring4all.swagger.EnableSwagger2Doc;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@EnableSwagger2Doc
@SpringBootApplication
public class Demoforswagger2Application {

	public static void main(String[] args) {
		SpringApplication.run(Demoforswagger2Application.class, args);
	}
}

```

输入`http://localhost:8080/swagger-ui.html`进行访问
![image.png](https://upload-images.jianshu.io/upload_images/9988457-911f0757ec754fd9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
打开`UserController`看一下
![image.png](https://upload-images.jianshu.io/upload_images/9988457-099114d14bc855ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
继续查看一个具体的请求
![image.png](https://upload-images.jianshu.io/upload_images/9988457-6bf97cc4a3c1bbbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
基本信息都有了，也可以在这里测试接口。

可以看到我们几乎没有怎么操作一个文档就建立完毕了。
[spring-boot-starter-swagger 项目及文档](https://github.com/SpringForAll/spring-boot-starter-swagger)
[本项目的下载地址](https://gitee.com/phoenixs_101/SpringBoot2.x-Learning/tree/master/demoforswagger2)
